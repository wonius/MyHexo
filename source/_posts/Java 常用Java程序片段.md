---
title: 常用Java程序片段
date: 2017-08-02 23:27:26
tags: Java
categories: Java
keywords: Java
description: 常用Java程序片段
copyright: true
---

## 字符串 <-> 整型
```
String a = String.valueOf(2);		//integer to numeric string

int i = Integer.parseInt(a);		//numeric string to an int
```

## 字符串 -> 日期
```
//Method 1
java.util.Date = java.text.DateFormat.getDateInstance().parse(String date);

//Method 2
SimpleDateFormat sdf = new SimpleDateFormat(“dd.MM.yyyy”);
Date date = sdf.parse(myString);
```

## util.Date -> sql.Date
```
java.util.Date utilDate = new java.util.Date();
java.sql.Date sqlDate = new java.sql.Date(utilDate.getTime());
```

## Array -> Map
```
String[][] countries = {{ "United States", "New York" }, { "United Kingdom", "London" },  
        { "Netherland", "Amsterdam" }, { "Japan", "Tokyo" }, { "France", "Paris" }};
        
Map countryCapitals = ArrayUtils.toMap(countries);
```

## 向文件末尾添加内容
```
BufferedWriter out = null;
try {
	out = new BufferedWriter(new FileWriter(“fileName”, true));
	out.write(“aString”);
} catch (IOException e) {
	//error processing code
} finally {
	if (out != null) {
		out.close();
	}
}
```

## 得到当前方法的名字
```
String methodName = Thread.currentThread().getStackTrace()[1].getMethodName();
```

## 发送邮件
```java
public void postMail( String recipients[ ], String subject, String message , String from) throws MessagingException  
{  
    boolean debug = false;  
  
     //Set the host smtp address  
     Properties props = new Properties();  
     props.put("mail.smtp.host", "smtp.example.com");  
  
    // create some properties and get the default Session  
    Session session = Session.getDefaultInstance(props, null);  
    session.setDebug(debug);  
  
    // create a message  
    Message msg = new MimeMessage(session);  
  
    // set the from and to address  
    InternetAddress addressFrom = new InternetAddress(from);  
    msg.setFrom(addressFrom);  
  
    InternetAddress[] addressTo = new InternetAddress[recipients.length];  
    for (int i = 0; i < recipients.length; i++)  
    {  
        addressTo[i] = new InternetAddress(recipients[i]);  
    }  
    msg.setRecipients(Message.RecipientType.TO, addressTo);  
  
    // Optional : You can also set your custom headers in the Email if you Want  
    msg.addHeader("MyHeaderName", "myHeaderValue");  
  
    // Setting the Subject and Content Type  
    msg.setSubject(subject);  
    msg.setContent(message, "text/plain");  
    Transport.send(msg);  
} 
```

## 列出文件与目录
```Java
File dir = new File("directoryName");  
  String[] children = dir.list();  
  if (children == null) {  
      // Either dir does not exist or is not a directory  
  } else {  
      for (int i=0; i < children.length; i++) {  
          // Get filename of file or directory  
          String filename = children[i];  
      }  
  }  
  
  // It is also possible to filter the list of returned files.  
  // This example does not return any files that start with `.'.  
  FilenameFilter filter = new FilenameFilter() {  
      public boolean accept(File dir, String name) {  
          return !name.startsWith(".");  
      }  
  };  
  children = dir.list(filter);  
  
  // The list of files can also be retrieved as File objects  
  File[] files = dir.listFiles();  
  
  // This filter only returns directories  
  FileFilter fileFilter = new FileFilter() {  
      public boolean accept(File file) {  
          return file.isDirectory();  
      }  
  };  
  files = dir.listFiles(fileFilter); 
```

## 创建图片缩略图

```java
private void createThumbnail(String filename, int thumbWidth, int thumbHeight, int quality, String outFilename)  
        throws InterruptedException, FileNotFoundException, IOException  
    {  
        // load image from filename  
        Image image = Toolkit.getDefaultToolkit().getImage(filename);  
        MediaTracker mediaTracker = new MediaTracker(new Container());  
        mediaTracker.addImage(image, 0);  
        mediaTracker.waitForID(0);  
        // use this to test for errors at this point: System.out.println(mediaTracker.isErrorAny());  
  
        // determine thumbnail size from WIDTH and HEIGHT  
        double thumbRatio = (double)thumbWidth / (double)thumbHeight;  
        int imageWidth = image.getWidth(null);  
        int imageHeight = image.getHeight(null);  
        double imageRatio = (double)imageWidth / (double)imageHeight;  
        if (thumbRatio < imageRatio) {  
            thumbHeight = (int)(thumbWidth / imageRatio);  
        } else {  
            thumbWidth = (int)(thumbHeight * imageRatio);  
        }  
  
        // draw original image to thumbnail image object and  
        // scale it to the new size on-the-fly  
        BufferedImage thumbImage = new BufferedImage(thumbWidth, thumbHeight, BufferedImage.TYPE_INT_RGB);  
        Graphics2D graphics2D = thumbImage.createGraphics();  
        graphics2D.setRenderingHint(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BILINEAR);  
        graphics2D.drawImage(image, 0, 0, thumbWidth, thumbHeight, null);  
  
        // save thumbnail image to outFilename  
        BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream(outFilename));  
        JPEGImageEncoder encoder = JPEGCodec.createJPEGEncoder(out);  
        JPEGEncodeParam param = encoder.getDefaultJPEGEncodeParam(thumbImage);  
        quality = Math.max(0, Math.min(quality, 100));  
        param.setQuality((float)quality / 100.0f, false);  
        encoder.setJPEGEncodeParam(param);  
        encoder.encode(thumbImage);  
        out.close();  
    } 
```

## 按照规则取目录下的文件名数组

```
File rootFile = new File("/XX/XX");
String[] dirList = rootFile.list(new FilenameFilter() {
    @Override
    public boolean accept(File dir, String name) {
    	//XXX开通的文件
        if (name.startsWith("XXX")) {
            return true;
        } else {
            return false;
        }
    }
});
```


