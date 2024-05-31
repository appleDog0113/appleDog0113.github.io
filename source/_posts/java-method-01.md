---
title: Java 函數的認識 01
date: 2024-05-31 09:22:39
tags:
categories:
description: 
認識Java的函數
學習函數引數傳遞的方法
學習函數遞迴的撰寫
認識函數的多數

認識類別
在類別裡使用資料成員與函數成員
學習this關鍵字的用法
在類別裡設計函數的多載
學習如何使用類別裡的公有與私有成員
---

#### 認識Java的函數

Java將函數稱為`method`
```java
public static 傳回值型態 函數名稱(型態 引數1, 型態 引數2,...){
    程式敘述;
    return 運算式;      // 沒有要回傳的話可以省略
}
```


範例：

```java
public class hw7_1{
public static void main(String args[]){
   proverb();
}
private static void proverb(){
      System.out.print("Two heads are better than one.");
   }
}
```
> 執行 `proverb();` 
> 可得 `System.out.print("Two heads are better than one.");`印出結果
> 印出 `Two heads are better than one.`



##### 函數引數傳遞的方法
##### 函數遞迴的撰寫
##### 認識函數的多數

#### 認識類別
##### 在類別裡使用資料成員與函數成員
##### 學習this關鍵字的用法

> this：強調類別本身的成員，而非`變數成員`。
> 可以在前方加上this

```java
class test{
    int num=5;
    void show(){
        int num=10;
        System.out.println("this.num: "+this.num);
        System.out.println("num: "+num);
    }
}

public class aaaaaa{
    public static void main(String args[]){
        test obj;
        obj = new test();
        obj.show();
    }
}
```
> print：
>> this.num: 5
>> num: 10

如果把obj.num變數設為 8
```java
class test{
    int num=5;
    void show(){
        int num=10;
        System.out.println("this.num: "+this.num);
        System.out.println("num: "+num);
    }
}

public class aaaaaa{
    public static void main(String args[]){
        test obj;
        obj = new test();
        obj.num=8
        obj.show();
    }
}
```
> print 的結果：
>> this.num: 8
>> num: 10


##### 在類別裡設計函數的多載
##### 學習如何使用類別裡的公有與私有成員

