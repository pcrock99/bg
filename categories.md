---
layout: default
title: 分类
permalink: /categories/
---

# 📂 文章分类

{% assign categories = site.categories | sort %}

{% for category in categories %}
  <div style="margin-bottom: 30px;">
    <h2 id="{{ category[0] | slugify }}" style="border-left: 4px solid var(--link-color); padding-left: 12px;">
      {{ category[0] }} <span style="font-size: 0.8rem; color: #888;">({{ category[1].size }} 篇)</span>
    </h2>
    <ul style="list-style: none; padding-left: 16px;">
      {% for post in category[1] %}
        <li style="margin-bottom: 8px;">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          <span style="font-size: 0.8rem; color: #888;">({{ post.date | date: "%Y-%m-%d" }})</span>
        </li>
      {% endfor %}
    </ul>
  </div>
{% endfor %}

<hr style="border-color: var(--border-color); margin: 30px 0;">

<p style="text-align: center;">
  <a href="/bg/" style="color: var(--link-color);">← 返回首页</a>
</p>