---
layout: page
title: "🏷️ Tags"
permalink: /tags/
---
## Browse by Topic

{% assign sorted_tags = site.tags | sort %}

{% for tag in sorted_tags %}
### {{ tag[0] }}

<ul>
  {% for post in tag[1] %}
    <li>
      <a href="{{ post.url | relative_url }}">
        {{ post.title }}
      </a>
      <small>— {{ post.date | date: "%Y-%m-%d" }}</small>
    </li>
  {% endfor %}
</ul>

{% endfor %}


