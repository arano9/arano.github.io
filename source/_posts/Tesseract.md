---
title: Tesseract
date: 2020-06-15 19:23:29
tags: notes
categories: 笔记
---

# OCR调研

## OCR技术简介

​		光学字符识别（Optical Character Recognition, OCR）是指对文本资料的图像文件进行分析识别处理，获取文字及版面信息的过程。

## Tesseract简介

​	Tesseract是惠普布里斯托实验室在1985到1995年间开发的一一个开源的OCR引擎，曾经在1995 UNLV精确度测试中名列前茅。但1996年后基本停止了开发。2005年，惠普将其对外开源，2006 由Google对Tesseract进行改进、消除Bug、优化工作。目前项目地址为: https://github.com/tesseract-ocr/tesseract。

它与Leptonica图片处理库结合，可以读取各种格式的图像并将它们转化成超过60种语言的文本，我们还可以**不断训练自己的库**，使图像转换文本的能力不断增强。

### Tess4j简介

> ### Description:
>
> > A Java JNA wrapper for Tesseract OCR API.
> >
> > Tess4J is released and distributed under the [Apache License, v2.0](http://www.apache.org/licenses/LICENSE-2.0.html) and is also available from [Maven Central Repository](http://mvnrepository.com/artifact/net.sourceforge.tess4j).

  上面摘自Tess4j的官网描述，Tess4j即对Tesseract OCR API的封装Java库。这也是目前当时采用Tesseract的想法，可以**本地直接调用**，而不需使用第三方云服务。

### 对Tess4j准确性进行验证

1. #### 安装tesseract环境

```bash
//这里建议更新为国内源 或者为git配置github代理
brew install tesseract
```

2. #### 创立maven项目

   导入关键依赖

   ```xml
   		<dependency>
   			<groupId>net.java.dev.jna</groupId>
   			<artifactId>jna</artifactId>
   			<version>4.1.0</version>
   		</dependency>
   		<dependency>
   			<groupId>net.sourceforge.tess4j</groupId>
   			<artifactId>tess4j</artifactId>
   			<version>3.4.0</version>
   			<exclusions>
   				<exclusion>
   					<groupId>com.sun.jna</groupId>
   					<artifactId>jna</artifactId>
   				</exclusion>
   			</exclusions>
   		</dependency>
   
   ```

   

3. 进行测试 

   ```java
   				//核心代码就以下几句 比较简单 
   				//把文件路径赋值 然后准备好中文训练数据即可       
           File imageFile = new File(this.testResourcesDataPath,"card.jpg");
           //set language
           instance.setDatapath(testResourcesLanguagePath);
           instance.setLanguage("chi_sim");
           String result = instance.doOCR(imageFile);
           logger.info(result);
   ```

   

   测试结果表明，简单的白底黑字图片是可以较为准确的识别，但是对于复杂点的情况，比如证件之类，会出现较为严重的乱码情况。

### Tess4j调研结论

​		经过上述流程，可以知道tess4j只不过就是个api调用，底层实现的核心在于tesseract，而对于tesseract的识别准确度，就又在于计算机不断识别加上人工干预矫正后训练的数据集。

