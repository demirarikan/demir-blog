---
layout: page
title: Projects
permalink: /projects/
---
Some of the academic, personal projects that I have written about.

<ul>
  {% for project in site.projects %}
    <li>
      <a href="{{ project.url }}">{{ project.title }}</a>
      <span>{{ project.date | date: "%B %d, %Y" }}</span>
    </li>
  {% endfor %}
</ul>