---
layout: default
title: 标签
permalink: /tags/
---

# 📌 所有标签

{% assign tags = site.tags | sort %}

<ul>
  {% for tag in tags %}
    <li>
      <strong>{{ tag[0] }}</strong> （{{ tag[1].size }} 篇文章）
      <ul>
        {% for post in tag[1] %}
          <li>
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
            <span style="color:#666; font-size:0.8rem;">（{{ post.date | date: "%Y-%m-%d" }}）</span>
          </li>
        {% endfor %}
      </ul>
    </li>
  {% endfor %}
</ul>