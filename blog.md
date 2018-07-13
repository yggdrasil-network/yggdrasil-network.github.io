# Blog

{% for post in site.posts %}
  <div class='blogpost'>
    <div id='date'>
      <div id='day'>{{ page.date | date: "%-d" }}</div>
      <div id='month'>{{ page.date | date: "%B %Y" }}</div>
    </div>
    <div id='overview'>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </div>
  </div>
{% endfor %}
