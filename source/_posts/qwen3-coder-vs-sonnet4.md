---
title: 通义灵码和Claude模型
date: 2025-08-09 09:19:18
categories: [编程]
tags: [前端,AI,Trae]
---

AI的发展势不可挡，深度使用通义灵码和Trae后更有体会。

之前对我来说，前端开发一直是弱势环节，调不好CSS样式，经常让我对独立完成前端项目望而生畏。现在好了，你在VS Code里装一个通义灵码插件，或者下载字节出的Trae开发工具，只要准确说出你的需求，基本上所有代码AI都能帮你自动生成。

通义灵码我用的是qwen3-coder模型，Trae里我用的是sonnet4模型，我用下来的感受是它们完全可以代替初级前端开发。qwen3-coder是免费模型，你说改哪，它就改哪，不会去改其它资源。sonnet4上下文理解能力明显更强，你让它改A，它把A改好后，发现B和C也需要修改，会自动把B和C也改过来，整体的推理明显比qwen3-coder强，另外就是它能很好的生成图片，qwen3-coder则不行。

业务流程中有一个需要企业用户上传营业执照图片提交注册的环节。看下面这张图片，整个页面就是我用sonnet4生成的，调试时发现图片上传功能不符合预期，因为它不知道我要调用后端接口将图片保存。

![](qwen3-coder-vs-sonnet4/business_license_upload.png)

于是，我在Trae聊天窗口里输入"客户上传营业执照图片后，先调用后端接口将图片保存到服务器，使用返回后的数据再调用OCR识别接口，将识别结果填充到对应输入框"，它帮我生成下面这段代码。

```typescript
// 处理营业执照上传
const handleBusinessLicenseUpload = (uploadFile: UploadFile) => {
  const rawFile = uploadFile.raw;
  if (rawFile) {
    const formData = new FormData();
    formData.append('file', rawFile);
    fileUpload(formData).then((uploadRes: any) => {
      if (uploadRes.code === 200) {
        companyForm.businessLicenseUrl = uploadRes.data.url;
        const ocrData = {
          url: null,
          file: null,
          ocrType: 'businessLicense',
          fileName: uploadRes.data.fileName,
        };
        ocrBusinessLicense(ocrData).then((ocrRes: any) => {
          console.log('ocrRes:', ocrRes);
          // 处理OCR识别结果，自动填充表单
          if (ocrRes.code === 200 && ocrRes.data) {
            const ocrData = ocrRes.data;          
        // 填充企业名称
        if (ocrData.companyName) {
          companyForm.companyName = ocrData.companyName;
        }
        
        // 填充统一社会信用代码
        if (ocrData.creditCode || ocrData.unifiedSocialCreditCode) {
          companyForm.creditCode = ocrData.creditCode || ocrData.unifiedSocialCreditCode;
        }
        
        // 填充地址
        if (ocrData.address || ocrData.registeredAddress) {
          companyForm.address = ocrData.address || ocrData.registeredAddress;
        }
        
        // 填充经营范围
        if (ocrData.businessScope || ocrData.scope) {
          companyForm.businessScope = ocrData.businessScope || ocrData.scope;
        }
        
        // 处理营业期限
        if (ocrData.validityPeriod || ocrData.licenseValidity) {
          const validity = ocrData.validityPeriod || ocrData.licenseValidity;
          if (validity.startDate && validity.endDate) {
            companyForm.licenseValidity = [new Date(validity.startDate), new Date(validity.endDate)];
          } else if (typeof validity === 'string') {
            // 如果是字符串格式，尝试解析日期范围
            const dateMatch = validity.match(/(\d{4}-\d{2}-\d{2}).*?(\d{4}-\d{2}-\d{2})/);
            if (dateMatch) {
              companyForm.licenseValidity = [new Date(dateMatch[1]), new Date(dateMatch[2])];
            }
          }
        }
        
        ElMessage.success('营业执照信息识别成功，已自动填充表单');
      } else {
        ElMessage.warning('营业执照识别失败，请手动填写信息');
      }
    }).catch((error: any) => {
      console.error('OCR识别错误:', error);
      ElMessage.error('营业执照识别失败，请重试或手动填写');
    });
  }
}
```
fileUpload是图片上传接口，ocrBusinessLicense是营业执照OCR识别接口。生成完后，我稍微调了下ocrBusinessLicense接口上传需要使用的参数，这段代码就跑通了 ，我被深深地震惊了。试想：你没有前端开发经验，也不熟悉TypeScript语言，就这段代码，让你自己去查资料、去开源项目里找相同功能的代码段复制粘贴，要花多少时间？

AI辅助编程简直太有帮助了，这节省多少时间！

当然AI模型生成出来的代码目前只是初稿，不过我觉得再怎么发展，它生成出来的内容始终也只能是初稿，它做不到100%符合要求，细节地方仍然需要用它的人仔细打磨。