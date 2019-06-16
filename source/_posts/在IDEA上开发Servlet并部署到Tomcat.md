---
title: 在IDEA上开发Servlet并部署到Tomcat
date: 2018-10-23 15:59:46
tags: JetBrains
categories: Note
---

<ol>
    <li>New Project -> Java Enterprise -> 勾选Web Application -> Next。</li>
    <li>项目创建好了之后，将通用lib文件复制到 web/WEB-INF/lib。</li>
    <li>选择File -> Project Structure -> Libraries，将刚才的lib文件夹添加进去。</li>
    <li>继续选择Artifacts，同样将lib文件夹添加进去。在这里可以直接点选右下角的fix快速更改。其他的保持不变，选择OK应用更改。</li>
    <li>右上角选择服务器 -> Edit Configuration... -> Deployment，修改合适的Application context，其他的保持默认就行。</li>
    <li>最后，运行项目或者debug进行测试。</li>
</ol>
