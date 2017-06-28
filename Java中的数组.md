---
title: Java中的数组
date: 2016-11-29 20:44:17
tags:
- Java
- 数组
category: Java
keywords:
- Java
- 数组
---

Java中的数组和Swift以及OC中的数组不同，原以为会和他们一样会有可变和不可变的概念，但是并没有，那简单了？但Java身后却是一个庞大、使用的集合家族。

<!-- more -->
<!-- toc -->

# 基本概念

Java的数组一旦初始化后，数组在内存中所占的空间就固定下来了，即使数组中的元素被清空，数组的长度仍然不变。特别的是，数组中的元素类型必须一致！

{% alert info no-icon %}
由于受到泛型的影响，数组中的元素可能会出现类型不一致的假象，但其实是一样的，因为他们的父类一定会是一致的。比如，水果数组中可能存放香蕉和苹果两种类型，但它们都是水果类型。
{% endalert %}

{% alert info no-icon %}
注意这里只是说数组的长度是不能变化的，但是数组的值还是可以变化的。
{% endalert %}


# 数组定义和初始化

数组(Arrays)的定义有两种方式，推荐使用第一种（作为初学者，还没有体验到为什么好，就这样写吧先）。

`Type[] arrayName`
`Type arrayName[]`

这时候，数组只是一个引用变量还没有指向任何内存空间，也不能指定长度，所以还要进行初始化。如果没有显式初始化，那么系统会自动分配初始化值。

## 静态初始化

由程序员指定初始化值，系统决定数组长度。

{% tabbed_codeblock %}
<!-- tab Java -->
int[] intArr;
intArr = new int[]{1,1,2,3,4};

Object[] objArr = new String[]{"Yanqh","Bamboo"};

Object[] objArr2 = {"Yanqh","Bamboo"};
<!-- endtab -->
{% endtabbed_codeblock %}

## 动态初始化

程序员指定数组长度，系统为数组元素分配初始值。

{% tabbed_codeblock %}
<!-- tab Java -->
int[] intArr2;
intArr2 = new int[3];

Object[] objArr3 = new Object[3];
<!-- endtab -->
{% endtabbed_codeblock %}

如果没有指定默认值，那么系统会分配给数组什么呢？

{% alert success no-icon %}
数组中的元素为基本类型中的整型（byte，short，int，long），则初始化值为0。
{% endalert %}

{% alert success no-icon %}
数组中的元素为基本类型中的浮点型（float，double），则初始化值为0.0。
{% endalert %}

{% alert success no-icon %}
数组中的元素为基本类型中的字符型（char），则初始化值为‘\u0000’
{% endalert %}

{% alert success no-icon %}
数组中的元素为基本类型中的布尔型（boolean），则初始化值为false。
{% endalert %}

{% alert success no-icon %}
数组中的元素为基本类型中的引用类型（类，接口，数组），则初始化值为null。
{% endalert %}


# 数组的使用

数组用length属性可以直接返回数组的长度，这样就可以遍历数组的每一个元素。

{% tabbed_codeblock %}
<!-- tab Java -->
for (int i = 0;i < objArr.length;i++) {
  System.out.println(objArr[i]);
}
<!-- endtab -->
{% endtabbed_codeblock %}

Java中有和Swift中for-in相同的循环foreach。
{% tabbed_codeblock %}
<!-- tab Java -->
for (Object name: objArr) {
  System.out.println(name);
}
<!-- endtab -->
{% endtabbed_codeblock %}

{% alert warning no-icon %}
在循环体中的元素都是系统自动生成的临时变量代替，这些变量并不是指向元素对象，所以在循环体中想要修改数组元组是值是不可能的。
{% endalert %}

# 深入数组

数组是引用类型，数组的引用变量只是一个引用，数组元素的和数组变量是分开存放的。数组存放在堆内存中，而引用变量存放在栈内存中。

如果两个数组的存储类型是相同的时候，那么它们的引用变量可以相互赋值交换，这样会形成数组长度可变的假象，记得刚开始学习OC的时候也遇到了这样的疑惑，为什么不可变字符串可以改变值呢？这并不是对象发生了改变，而是引用变量中的内存地址发生了改变，在这里是一个道理。

{% tabbed_codeblock %}
<!-- tab Java -->
int[] a = {1,2,3};
int[] b = new int[2];
a = b;
System.out.println(b.length);//2
<!-- endtab -->
{% endtabbed_codeblock %}

不同的是，OC中不可变的数组中值也是不可变的，但Java中的数组在初始化后，元素的值还是可以改变的。

{% tabbed_codeblock %}
<!-- tab Java -->
int[] a = {1,2,3};
a[1] = 4;
System.out.println("a[1] = " + a[1]);//a[1] = 4
<!-- endtab -->
{% endtabbed_codeblock %}

# 操作数组的工具类

Java为数组提供了一些静态方法来操作数组，也就是Swift中的类方法。

{% alert success no-icon %}
public static int binarySearch(Type[] a, Type key)
{% endalert %}
这个方法会查询数组a中是否包含了key这个值，如果包含，返回1，不包含返回-1，但使用是有前提的，数组a中的元素必须是可以比较的，并进行了升序排序的，不然会抛出`test2.translateCharToChinese cannot be cast to java.lang.Comparable`异常。

{% tabbed_codeblock %}
<!-- tab Java -->
translateCharToChinese z = new translateCharToChinese();
translateCharToChinese h = new translateCharToChinese();
translateCharToChinese[] zh = {z,z};
int[] a = {1,2,3};
final int i = Arrays.binarySearch(a,1);// 1
final int i = Arrays.binarySearch(zh,z);// 异常
<!-- endtab -->
{% endtabbed_codeblock %}

{% alert success no-icon %}
public static int binarySearch(int[] a, int fromIndex, int toIndex, int key)
{% endalert %}
这个方法与上一个方法相似，但可以搜索一个范围，但是要注意toIndex不包含，是一个左闭右开的区间。

{% alert success no-icon %}
public static type[] copyOf(type[] original, int newLength)
{% endalert %}
{% tabbed_codeblock %}
<!-- tab Java -->
int[] a = {1,2,3};
int[] b = Arrays.copyOf(a,5);
for (int num: b) {
  System.out.println(num);
}// 1 2 3 0 0
<!-- endtab -->
{% endtabbed_codeblock %}

{% alert success no-icon %}
public static type[] copyOfRange(type[] original, int from, int to)
{% endalert %}
这个方法也与上一个方法相似，但同样的，to是不包含的。

{% alert success no-icon %}
public static boolean equals(type[] a, type[] a2) {
{% endalert %}
数组是否相同判断，必须是长度和元素都相等才会返回true。

{% alert success no-icon %}
public static void fill(type[] a, type val) 
{% endalert %}
把数组中的所有元素都赋值为val

{% alert success no-icon %}
public static void fill(type[] a, int fromIndex, int toIndex, type val)
{% endalert %}
与上一个方法相同，但是范围也是左闭右开。

{% alert success no-icon %}
public static void sort(type[] a) 
{% endalert %}
对数组进行排序，但是如果是非数值类型必须实现Comparable接口。


{% alert success no-icon %}
public static void sort(type[] a, int fromIndex, int toIndex)
{% endalert %}
对数组进行排序，toIndex是不包含的。

{% alert success no-icon %}
public static String toString(type[] a)
{% endalert %}
将数组转换成字符串，如果是对象将输出对象的包、类名、地址。

{% alert success no-icon %}
public static native void arraycopy(Object src,  int  srcPos,Object dest, int destPos,int length);
{% endalert %}
System类中的数组方法，将数组src中的元素从第srcPos开始的length个赋值到dest数组，起始位置为destPos。

    




