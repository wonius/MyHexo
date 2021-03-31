---
title: lombok应用
copyright: true
date: 2018-08-30 14:28:17
tags: lombok
categories: Java
keywords: lombok
description: lombok应用
---

lombok的方面之处就不多说了，这里列一下使用中遇到的一些问题。

# @Builder继承

@Builder无法继承。所以当我们同时有父类、子类，无法都使用@Builder。

解决方案：父类不用@Builder，子类构造函数上加@Builder

```java
@AllArgsConstructor
@NoArgsConstructor
public class Father {

    private String fatherName;
}

public class Children {
    
    private String childName;
    
    @Builder
    public Children(String fatherName, String childName) {
        super(fatherName);
        this.childName = childName;
    }
}
```

缺点：

1. 父类无法使用builder()；
2. 子类的构造得是全参构造；

# @Data继承

子类继承时，需加@EqualsAndHashCode(callSuper=true)

