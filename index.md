---
layout: index
---

<title>{{ site.title_display }}</title>

<ul style="list-style-type:none">
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>


<center><i><br><br>I like to use sketches even though my handwriting is terrible. Also I stole the theme for this blog from Stanford.</i></center>