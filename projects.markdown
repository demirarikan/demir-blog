---
layout: page
title: Projects
permalink: /projects/
---
this is just a test page to see what happens

<ul>
  {% for project in site.projects %}
    <li>
      <a href="{{ project.url }}">{{ project.title }}</a>
      <span>{{ project.date | date: "%B %d, %Y" }}</span>
    </li>
  {% endfor %}
</ul>