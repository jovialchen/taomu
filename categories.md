---
permalink: /categories/
title: Categories
layout: page
---


<div id="archives">
---

### 🏷️ Blog Categories 博客分类

---

<!-- Tech & Coding Section with Image -->
<table>
<tr>
  <td>
    <img src="assets/images/478c44ef-b8bc-46dd-80d8-e13077cca1d3.jfif" alt="Tech Image" width="150">
  </td>
  <td>
    <h2><a href="{{ site.url }}/taomu/categories/tech_coding/">📐 Tech & Coding 技术与编程</a></h2>
    <blockquote>"Code the Future, One Byte at a Time."</blockquote>
  </td>
</tr>
<tr>
  <td colspan="2">
    👩‍💻上班用C和C++, 见过很多C和C++的高手. 这两门语言实在过于难。一个不小心就Leak了。一直在做通信产品。从18年开始陆陆续续地看机器学习。
  </td>
</tr>
</table>

<!-- Life System Section with Image -->
<table>
<tr>
  <td>
    <h2><a href="{{ site.url }}/taomu/categories/life_canvas">🎨 Life Canvas 生活画布</a></h2>
    <blockquote>"Life is a blank canvas, paint it beautifully."</blockquote>
  </td>
  <td>
    <img src="assets/images/a2fdbdbe-7127-4c1b-99f8-90f9075587cf.jfif" alt="Life System Image" width="150">
  </td>
</tr>
<tr>
  <td colspan="2">
   👩‍🎨关于如何驾驭这个名为生活的疯狂冒险的见解和策略。时间管理技巧、个人成长感悟等等。
  </td>
</tr>
</table>

<!-- Just Fun Section with Image -->
<table>
<tr>
  <td>
    <img src="assets/images/df47d168-7b2a-4925-b276-8a49f84c1956.jfif" alt="Just Fun Image" width="150">
  </td>
  <td>
    <h2><a href="{{ site.url }}/taomu/categories/just_fun">🎉 Just Fun 轻松一刻</a></h2>
    <blockquote>"Fun is the best!" </blockquote>
  </td>
</tr>
<tr>
  <td colspan="2">
   😄轻松的随笔、游戏评测，以及让生活变得更加明亮的小事。
  </td>
</tr>
</table>

---

{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>

    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>
    <ul>
    {% for post in site.categories[category_name] %}
    <article class="archive-item">
      <li><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a>- {{ post.date | date: "%Y-%m-%d" }}</li>
    </article>
    {% endfor %}
    </ul>
  </div>
{% endfor %}
</div>