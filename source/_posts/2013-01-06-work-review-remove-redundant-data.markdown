---
layout: post
title: "那些干过的活——筛选并更新重复的商家数据"
date: 2013-01-06 16:14
comments: true
categories: [Sql]
---

### 一、问题场景简述   
因遗留问题导致数据表qp_shop中部分商家的电话号码出现了重复，经分析，一组重复的商家中，最先插入到表中的商家的电话是正确的，而其余商家的电话是错误的，应该为空，但因在抓取（商家信息都是通过网页抓取获得的）过程中，没有做好处理，导致其后的商家电话都与该组中最先抓取到的商家的电话相同。现在要做的便是将这些重复的商家电话更新为其本来的值，即为空。  
总结来说，便是筛选出表中电话重复的商家，然后针对这些电话相同的商家，将seqno（即主键id）最小的商家信息保留，而将seqno大于该最小seqno的其他商家的电话更新为空。
<!-- more -->

### 二、处理过程
该表的数据量是很大的，大约有100多万记录。我首先做的便是对电话重复的商家进行去重。要对查询结构中的数据进行去重，自然会想到**distinct**，但是distinct是针对单个字段查询去重的（且必须放在第一个查询的字段之前），当要查询多个字段时，distinct会作用于其后的所有字段，此时产生的结果可能就不是我们想要的了。  
还有一种方法也可以用来去重，便是使用group by语句。因为解决该问题中，需要查询的不只是telephone这个字段，还需要查询主键seqno，所以只能使用group by语句。group by是用来分组的，我需要对电话去重，因此就按telephone来分组，然后取到seqno个数大于1的数据，即count(seqno)&gt;1。所以可以得到如下的sql：
{% codeblock %}
SELECT 
    seqno AS seqno, telephone AS telephone, count(seqno)
FROM
    goldeneye.qp_shop_detail
WHERE
    telephone != ''
GROUP BY telephone
HAVING count(seqno) > 1
{% endcodeblock %}
本来以为该条sql查询到的结果便是电话相同的商家中最先被插入的那条商家的记录，然而执行后的结果并非是我想要的，结果中的商家并非全部是seqno（即主键）最小的商家。查资料后发现，mysql中的group by子句在分组时会按照某种顺序去处理，但具体是什么并没有讲清楚，如此看来，并非会按照主键的顺序去自动处理，没办法，只能在sql中加入排序处理了，修改后的sql如下：
{% codeblock %}
SELECT
    t.seqno AS seqno, t.telephone AS telephone
FROM
    (
      SELECT q.seqno AS seqno, q.telephone AS telephone
      FROM qp_shop_detail q
      WHERE q.telephone != ''
      ORDER BY q.seqno ASC
    ) AS t
GROUP BY t.telephone
HAVING count(t.seqno) > 1
{% endcodeblock %}
现在查询到的结果便满足了以上的要求。  
另外需要注意的是因数据量较大，需要对telephone字段建立索引，提高查询电话相同但主键不同的商家时的效率，另外sql中不要使用“！=”去判断seqno，而应该使用“&gt;”（因为与它电话相同的其他商家的seqno肯定比它的大），同样可以提高查询速度。sql如下：
{% codeblock %}
SELECT
  seqno AS seqno,
  telephone AS telephone
FROM
  qp_shop_detail
WHERE
  seqno > #{seqno}
AND
  telephone = #{telephone}
{% endcodeblock %}
最后对查询出的结果集进行相应的处理即可。
