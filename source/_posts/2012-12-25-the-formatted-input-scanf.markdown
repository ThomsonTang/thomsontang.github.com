---
layout: post
title: "C语言中的格式化输入函数——scanf()"
date: 2012-12-25 14:50
comments: true
categories: [C/C++] 
---

**scanf()**函数和**printf()**函数类似，只是其功能刚好与printf相反，它是用来从标准输入中读取字符的，其形式如下：  
```
int scanf(char *format, ...)
```
其中，format是格式化字符串，与printf()中的格式化串类似；后面可以跟若干个参数，用来存储从输入中读入的结果。这里需要注意的是，后面的每个参数都必须是一个指针。返回值是一个整数，是指成功读入值的个数。
<!-- more -->
当按照格式化串读取字符完成，或者输入的字符不匹配格式化串时，scanf函数就会结束执行。  

下面列出一些使用scanf的例子：  

1.读入日期格式：  
{% codeblock scanfDate.c lang:c %}
void scanfDate()
{
    int day, year;
    char month[20];

    scanf("%d %s %d", &day, month, &year);
}
{% endcodeblock %}
scanf中用来存储读入字符的参数必须是指针，因此对于day和year这两个变量必须要使用取值符号**&**，而month是数组，本身就是数组，因此不需要＆。  

2.匹配格式化串内容  
scanf函数中格式串可以包含任意字符，但是必须保证和输入中的相匹配。
{% codeblock scanfDate.c lang:c %}
void scanfDate()
{
    int day, month, year;

    scanf("%d/%d/%d", &month, &day, &year);
}
{% endcodeblock %}
针对以上函数，输入的格式应为: mm/dd/yy，scanf函数在查找输入值的时候会自动跳过空白字符。  

3.输入double型数据
{% codeblock sum.c lang:c %}
void sum() 
{
    double a, b;

    scanf("%lf %lf", &a, &b);
    printf("%.2f\n", a + b);
}
{% endcodeblock %}
这里需要注意scanf的格式化串中使用的是**“%ld”**，来表示double类型的转换。另外，如果scanf中的格式串若是以“\n”结束，则需要使用EOF来结束读取，通过ctrl-d组合键实现。
