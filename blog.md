# Blog

{% for post in site.posts %}
  <div class='blogpost'>
    <div id='date'>
      <div id='day'>{{ post.date | date: "%-d" }}</div>
      <div id='month'>{{ post.date | date: "%B %Y" }}</div>
    </div>
    <div id='overview'>
      <div id='title'><a href="{{ post.url }}">{{ post.title }}</a></div>
      <div id='excerpt'>{{ post.excerpt }}</div>
    </div>
  </div>
{% endfor %}
