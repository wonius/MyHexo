---
title: Spring集成HanLP分词
copyright: true
date: 2018-06-15 15:46:20
tags: 
- Spring
- 分词
categories: Spring
keywords: HanLP
description: Spring集成HanLP分词，支持用户自定义字典
---

# 配置pom文件

```
<dependency>
    <groupId>com.hankcs</groupId>
    <artifactId>hanlp</artifactId>
    <version>portable-1.6.4</version>
</dependency>
```

当前最新版本为1.6.4。可以在[这里](https://github.com/hankcs/HanLP)查看最新版本。

# 添加自定义字典文件

在resources下面建立目录，存放自定义字典。

我的目录是resources/hanlp/custom/CustomDic.txt

# 编写Java类

```
public class WordUtil {

    public static final String CUSTOM_DICTIONARY_PATH = "/hanlp/custom/CustomDic.txt";
    public static final String SYNONYM_WORD_PATH = "/hanlp/synonym/SynonDic.txt";

    private static List<Term> list;
    private static Segment segment;
    private static Map synonymMap;

    public static void main(String[] args) {

//        refreshDictionary();
        segmentSentence("你是柴犬狗狗吧");
    }

    /**
     * 刷新用户自定义词典
     */
    public static void refreshDictionary() {
        HanLP.Config.CustomDictionaryPath = new String[] {
                WordUtil.class.getResource(CUSTOM_DICTIONARY_PATH).getPath()
        };

        CustomDictionary.reload();
        segment = HanLP.newSegment().enableCustomDictionary(true);
    }

    /**
     * 分词
     * @param text
     * @return
     */
    public static String segmentSentence(String text) {
        segment = HanLP.newSegment().enableCustomDictionary(true);

        list = segment.seg(text);
        String result = list.get(0).word;
        for(int i =1; i < list.size(); i++) {
            result+=" "+list.get(i).word;
        }
        System.out.println(result);
        return result;
    }

    /**
     * 刷新同义词字典
     */
    public static void refreshSynonym() {
        String path = WordUtil.class.getResource(SYNONYM_WORD_PATH).getPath();
        File file = new File(path);
        BufferedReader reader;
        synonymMap = new HashMap();

        try {
            reader = new BufferedReader(new FileReader(file));
            String line;
            while ((line = reader.readLine()) != null) {
                String[] words = line.split(" ");
                for (String word: words) {
                    synonymMap.put(word, words[0]);
                }
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    /**
     * 获取同义词
     * @param word
     * @return
     */
    public static String getSynonym(String word) {
        return (String) synonymMap.get(word);
    }

}
```

为了提高效率，HanLP会将字典文件转义成bin文件，每次从bin文件中读取单词。

所以当字典更新时，需要删除bin文件，重新生成，官方提供reload方法，但是需要指定自定义字典的位置。

当调用segment()方法时会先从对应的bin文件中读取，如果没有bin文件，先重新生成bin文件，再读取。