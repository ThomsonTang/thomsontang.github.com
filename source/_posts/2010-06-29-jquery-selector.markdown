---
layout: post
title: "jQuery实现联动下拉菜单"
date: 2010-06-29 16:46
comments: true
categories: [jQuery]
---

今天，新的一个功能模块开发结束啦，回头总结一下用到的知识点，有些也是最近刚学习到的，写篇总结，加深理解吧！  
**功能需求**：实现两个下拉框“企业类型”和“企业子类型”的联动。  
**关键技术点**：JQuery中的全局函数——$.getJSON()的使用  
<!-- more -->
**具体实现**：  
### 1. html页面部分代码：（创建两个select下拉框）  
{% codeblock selector.html lang:html %}
<tr>
　　　　　　<td>
<label style="color:red; vertical-align:middle">*</label>企业类型：
</td>
<td>
<select id="entTypeId" name="entType" class="default_select">
<option value="1" id="produceEnt">生产企业</option>
<option value="2" id="wholeSaleEnt">批发企业</option>
<option value="3" id="retailEnt">零售企业</option>
<option value="4" id="specialUser">特殊用户</option>
<option value="5" id="thirdPartyLogistics">第三方物流</option>
<option value="6" id="useUnit">使用单位</option>
</select>
</td>
</tr>
<tr>
<td>
&nbsp;企业子类型：
</td>
<td>
<select id="entSubTypeId" name="entSubType" class="default_select">
<option id="entSubTypeOption" value="${curRasEntBaseForAudit.curRasEntBase.entSubType}"></option>
</select>
</td>
</tr>
{% endcodeblock %}
### 2. jQuery脚本代码：
{% codeblock selector.js lang:javascript %}
　　　　//实现修改表单中的“企业类型”与“企业子类型”的联动
function connectEntTypeAndEntSubType(entTypeValue) {
    $.getJSON("<c:url value='/entInfoMod/queryEntType.jhtml'/>", {index:entTypeValue}, function(data) {
            var entSubTypeOtherOption = '';
            for(var i=0; i<data.length; i++) {
            entSubTypeOtherOption += '<option value="' + data.typeValue + '">' + data.typeName + '</option>';
            }
            $("#entSubTypeId").html(entSubTypeOtherOption);
            });
}
　　　　//选择"企业类型"时联动“企业子类型”
$("#entTypeId").change(function() {
        connectEntTypeAndEntSubType($("#entTypeId").val());
        });
{% endcodeblock %}
### 3. 服务器端生成JSON串的部分代码：
(1). "企业类型"和“企业子类型”的映射关系，最终已JSON串的形式返回。  
{% codeblock EntTypeMap.java lang:java %}
package com.piats.manage.util;
public class EntTypeMap {
    //企业类型
    private static final String PRODUCE_ENT = "生产企业";
    private static final String WHOLESALE_ENT = "批发企业";
    private static final String RETAIL_ENT = "零售企业";
    private static final String SPECIAL_USER = "特殊用户";
    private static final String THIRD_PARTY_LOGISTICS = "第三方物流";
    private static final String INUSE_UNITS = "使用单位";
    //"生产企业"的子类型
    private static final String DESIGNATED_PRODUCE_ENT = "定点生产企业";
    private static final String UNDESIGNATED_PRODUCE_ENT = "非定点药品生产企业";
    //"批发企业"的子类型
    private static final String GENERAL_DRUG_WHOLESALE_ENT = "普通药品批发企业";
    private static final String SPECIAL_DRUG_WHOLESALE_ENT_NATIONAL = "特殊药品定点批发企业_全国性批发企业";
    private static final String SPECIAL_DRUG_WHOLESALE_ENT_REGIONAL = "特殊药品定点批发企业_区域性批发企业";
    private static final String CDC_DISEASE_CONTROL_CENTER = "CDC疾病控制中心";
    //"使用单位"的子类型
    private static final String MEDICAL_INSTITUTION = "医疗机构";
    private static final String DISEASE_PREVENTION = "疾病预防机构";
    private static final String FAMILY_PLANNING_SERVICE = "计划生育服务机构";
    //映射企业类型和子类型，并将其拼接成一个json串返回
    public static String mapingEntType(String index) {
        int key = Integer.parseInt(index);
        String jsonText = "";
        switch (key) {
            case 1:
                jsonText = "[{typeValue:'1',typeName:'" + DESIGNATED_PRODUCE_ENT + "'},"
                    + "{typeValue:'2',typeName:'" + UNDESIGNATED_PRODUCE_ENT + "'}"
                    + "]";
                break;
            case 2:
                jsonText = "[{typeValue:'1',typeName:'" + GENERAL_DRUG_WHOLESALE_ENT + "'},"
                    + "{typeValue:'2',typeName:'" + SPECIAL_DRUG_WHOLESALE_ENT_NATIONAL + "'},"
                    + "{typeValue:'3',typeName:'" + SPECIAL_DRUG_WHOLESALE_ENT_REGIONAL + "'},"
                    + "{typeValue:'4',typeName:'" + CDC_DISEASE_CONTROL_CENTER + "'}"
                    + "]";
                break;
            case 3:
                jsonText = "[{typeValue:'1',typeName:'" + RETAIL_ENT + "'}]";
                break;
            case 4:
                jsonText = "[{typeValue:1,typeName:'" + SPECIAL_USER + "'}]";
                break;
            case 5:
                jsonText = "[{typeValue:'1',typeName:'" + THIRD_PARTY_LOGISTICS + "'}]";
                break;
            case 6:
                jsonText = "[{typeValue:'1',typeName:'" + MEDICAL_INSTITUTION + "'},"
                    + "{typeValue:'2',typeName:'" + DISEASE_PREVENTION + "'},"
                    + "{typeValue:'3',typeName:'" + FAMILY_PLANNING_SERVICE + "'}"
                    + "]";
                break;
            default:
                break;
        }
        return jsonText;
    }
}
{% endcodeblock %}

(2). Action中将JSON串返回到客户端：  
{% codeblock  Action.java lang:java %}
/**
 * Query the type of enterprise.
 * @param mapping
 * @param form
 * @param request
 * @param response
 * @return
 */
private ActionForward queryEntType(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response) {
    String index = getNotBlankString(request.getParameter("index"));
    String jsonText = EntTypeMap.mapingEntType(index);
    ClientOutPut.printout(response, jsonText, "json");
    return null;
}
{% endcodeblock %}
知识点总结：$.getJSON()向服务器发送请求，获取相应的JSON数据。它会自动将其转换为可以直接访问的JSON对象以供客户端操作。
