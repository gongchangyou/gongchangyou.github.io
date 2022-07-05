---
layout: post
title: "pdfreader"
date: 2022-07-06 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# pdfbox

添加依赖
```
  <!-- https://mvnrepository.com/artifact/org.apache.pdfbox/pdfbox -->
        <dependency>
            <groupId>org.apache.pdfbox</groupId>
            <artifactId>pdfbox</artifactId>
            <version>3.0.0-alpha3</version>
        </dependency>
```



遍历文件夹

```
 String path = "/path/to/pdf";		//要遍历的路径
        File file = new File(path);		//获取其file对象
        File[] fs = file.listFiles();	//遍历path下的文件和目录，放在File数组中
        for(File f:fs){					//遍历File[]数组
            if(!f.isDirectory()) {        //若非目录(即文件)，则打印
//                System.out.println(f);
                parse(f);
            }
        }
```



逐页读取

```
 try {
        PDDocument document = Loader.loadPDF(file);
        if (!document.isEncrypted()) {
            PDFTextStripper stripper = new PDFTextStripper();
            var lastNo = "";
            var goodOffset = 1;

            for(int i = 1; i<=document.getNumberOfPages(); i ++){
                stripper.setSortByPosition(true);
                stripper.setStartPage(i);
                stripper.setEndPage(i);

                String text = stripper.getText(document);
            }
            
        }
    } catch (IOException e) {
        e.printStackTrace();
 }
```

