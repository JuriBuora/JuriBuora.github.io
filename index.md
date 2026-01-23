---
layout: home
title: "my blog"
permalink: /
---

Welcome to my blog. This is your homepage where daily notes will appear. Below are your posts (Day 01, Day 02, ...).

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%d-%m-%Y" }}
    </li>
  {% endfor %}
</ul>

<!-- To add a daily note create a post file in _posts with the filename format: YYYY-MM-DD-day-XX.md and the title like: "Day 01 - 16-01-2025" -->
