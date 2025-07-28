---
permalink: /tags/
layout: page
---

<ul class="tag-cloud">
{% for tag in site.tags %}
  <li style="font-size: {{ tag | last | size | times: 100 | divided_by: site.tags.size | plus: 70  }}%">
    <a href="#{{ tag | first | slugize }}">
      {{ tag | first }}
    </a>
  </li>
{% endfor %}
</ul>

<pre class="mermaid">
mindmap
  root((我的标签说明))
    tech_coding
      computer_science
      telecom
        computer_network
        codec
      machine_learning
        LLM
          Tranformers
          RAG
            GraphRAG
            model_context_protocol
            dify
        reinforcement_learning
      programming_language
        python
    life_canvas
      记笔记
      育儿
    just_fun
      读书
      电影
      哲学
      看世界
      我的日常
      一点想法
      看画展
</pre>

<div id="archives">
{% for tag in site.tags %}
  <div class="archive-group">
    {% capture tag_name %}{{ tag | first }}{% endcapture %}
    <h3 id="#{{ tag_name | slugize }}">{{ tag_name }} ({{ site.tags[tag_name] | size }})</h3>
    <a name="{{ tag_name | slugize }}"></a>
    <ul>
    {% for post in site.tags[tag_name] %}
    <article class="archive-item">
      <li><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a> - {{ post.date | date: "%Y-%m-%d" }}</li>
    </article>
    {% endfor %}
    </ul>
  </div>
{% endfor %}
</div>