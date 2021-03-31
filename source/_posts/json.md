---
title: Json处理时需要注意的那些事儿
copyright: true
date: 2018-09-12 16:11:12
tags: JSON
categories: 积累
keywords: JSON
description: Json处理过程中遇到的坑
---

# @JsonIgnore

```java
@Data
public class Student {
    
    private String name;
    private String age;
    private String gender;
    ……
    
    @JsonIgnore
    public boolean isName() {
        ……
    }
}
```

（忽略命名规范）上述代码在序列化、反序列化时，都会导致name属性被忽略。

除非在属性上添加@JsonProperty。

