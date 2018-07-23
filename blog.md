---
sitemap: true
---

# Blog

You can subscribe to our blog [using RSS](https://yggdrasil-network.github.io/feed.xml).

{% for post in site.posts %}
  <div class='blogpost'>
    <div id='date'>
      <div id='day'>{{ post.date | date: "%-d" }}</div>
      <div id='month'>{{ post.date | date: "%B %Y" }}</div>
    </div>
    <div id='overview'>
      <div id='title'><a href="{{ post.url }}">{{ post.title }}</a></div>
      <div id='excerpt'>{{ post.excerpt | strip_html }}</div>
    </div>
  </div>
{% endfor %}
