<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [类索引、父类索引、接口索引集合](#%E7%B1%BB%E7%B4%A2%E5%BC%95%E3%80%81%E7%88%B6%E7%B1%BB%E7%B4%A2%E5%BC%95%E3%80%81%E6%8E%A5%E5%8F%A3%E7%B4%A2%E5%BC%95%E9%9B%86%E5%90%88)
  - [类索引](#%E7%B1%BB%E7%B4%A2%E5%BC%95)
  - [父类索引](#%E7%88%B6%E7%B1%BB%E7%B4%A2%E5%BC%95)
  - [接口索引集合](#%E6%8E%A5%E5%8F%A3%E7%B4%A2%E5%BC%95%E9%9B%86%E5%90%88)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 类索引、父类索引、接口索引集合

类索引和父类索引都是一个u2类型的数据，而接口索引集合是一组u2类型的数据的集合，Class文件中由这三项数据来确定这个类的继承关系。

## 类索引

类索引用于确定这个类的全限定名，它用一个u2类型的数据表示，其指向了类型为CONSTANT_Class_info的类描述符常量，通过CONSTANT_Class_info类型的常量中的索引值可以找到定义在CONSTANT_Utf8_info类型的常量中的全限定符字符串。

在ClassDemo类的字节码文件中，索引类对应的两个字节为0x0003，这表示其指向了第3个常量。查询可以知道第3个常量的类型就是CONSTANT_Class_info，并且其指向的字符串常量为com/jvm/ClassDemo。

## 父类索引

父类索引用于确定这个类的父类的全限定名，因为Java不允许多继承，所以在Java中父类索引只有一个，并且除了java.lang.Object之外，所有Java类的父类索引都不为0。父类索引用一个u2类型的数据表示，其指向了类型为CONSTANT_Class_info类型的常量，通过CONSTANT_Class_info类型的常量中的索引值可以找到定义在CONSTANT_Utf8_info类型的常量中的全限定符字符串。

在ClassDemo类的字节码文件中，类索引后紧接着的两个字节就是父类索引的标志位0x0004，这表示其指向了第4个常量。查询可以知道第4个常量的类型就是CONSTANT_Class_info，并且其指向的字符串常量为java/lang/Object。

## 接口索引集合

接口索引集合就用来描述哪个类实现了哪些接口，这些被实现的接口将按implements语句（如果这个类本身就是一个接口，则应当是extends语句）后的接口顺序从左到右排列在接口索引集合中。

对于接口索引集合，入口第一项——u2类型的数据为接口计数器（interfaces_count），表示索引表的容量。如果该类没有实现任何接口，则该计数器值为0，后面接口的索引表不再占用任何字节。

在ClassDemo类的字节码文件中，紧跟着父类索引后的两个字节是0x0000，这表示该类没有实现任何接口。因此后面的接口索引表为空。

```
00 03 类索引
00 04 父类索引
00 00 接口计数器
```
