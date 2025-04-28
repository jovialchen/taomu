---
title: Yo Yo Ho
layout: page
---
## 🌟 Welcome 欢迎🌟


<table>
<tr>
  <td>
    <p>这个博客是我的数字花园，会记录一些我在生活中的小发现。</p>
    <p>快乐的花花草草，快乐的我~</p>
  </td>
</tr>
<tr>
  <td>
    <p>This blog is my digital garden, where I record my eureka moments in my life.</p>
    <p>Happy plants and happy minds~  </p>
  </td>
</tr>
</table>





---

<ul>
  {% for post in site.posts %}
    <li>
      <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a>- {{ post.date | date: "%Y-%m-%d" }}</h2>
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>



✨ Let's connect and explore the beautiful chaos of technology, life, and a bit of fun together!  
✨ 卷起来~~~🔄


<script src="https://utteranc.es/client.js"
        repo="jovialchen/jovialchen.github.io"
        issue-term="pathname"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>