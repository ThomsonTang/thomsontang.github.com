---
layout: post
title: "解决回车后自动刷新页面的问题"
date: 2010-07-12 17:31
comments: true
categories: [jQuery]
---

**问题描述**：完善分页功能时，在小文本框中输入要跳转的页码后敲击回车键，无法提交分页请求，却刷新了当前页面。  
**解决方法**：经仔细查找反复测试后，终于找到了问题的所在：原来是将要显示的内容放在了一个表单中。只需将显示的内容放在表单外，便不会出现以上问题了！  
<!-- more -->
我的例子：
### 1. 主页面(ent_info_query.jsp):
{% codeblock ent_info_query.jsp lang:html %}
<div id="rs" style="display:none">
<form id="entModForm" name="entModForm">
<table id="table_c" border="0" cellpadding="0" cellspacing="0">
<tr>
<td colspan="3" class="title_t">查询结果</td>
</tr>
<tr>
<td colspan="3">
<table class="Results_t" border="0" cellpadding="0" cellspacing="0">
<thead>
<tr>
<td class="table_title"> 选择 </td>
<td class="table_title"> 序号 </td>
<td class="table_title"> 申请时间</td>
<td class="table_title"> 企业名称</td>
<td class="table_title"> 变更后企业名称 </td>
<td class="table_title"> 审核状态 </td>
</tr>
</thead>
<tbody id="curRasEntBaseListDiv">
</tbody>
</table>
</td>
</tr>
<tr>
<td colspan="6" align="center" class="center">
<img src="<c:url value='/entModAudit/style/images/modPass.jpg'/>" id="modAuditPassBtn" name="modAuditPass" onclick="auditModPass();"/>
<img src="<c:url value='/entModAudit/style/images/modUnPass.jpg'/>" id="modAuditUnPassBtn" name="modAuditUnPass" onclick="showNoPassDialog();"/>
</td>
</tr>
</table>
</form>
</div>
{% endcodeblock %}
### 2. 片段页面(_ent_info_query.jsp):
{% codeblock _ent_info_query.jsp lang:html %}
第<input type="text" style="width:36px;text-align:center" maxlength="5" value="<c:out value='${entModificationAuditViewModel.cp}'/>" alt="<c:out value='${entModificationAuditViewModel.tp}'/>"
onKeyUp="value=value.replace(/[^0-9]/g,'');if(event.keyCode==13)if(value<1){p(1)}else if(value>Number(alt)){p(alt)}else{p(value)}"
/>页 / 共<c:out value="${entModificationAuditViewModel.tp}"/>页
{% endcodeblock %}
### 3.结果 
页面_ent_info_query.jsp将在id="curRasEntBaseListDiv"的tbody中显示，也就是说要在表单entModForm中显示，故在分页时出现了以上问题。该例中我直接取消了表单entModFrom，然后问题就得到了解决。 
