<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{[pst.url}}">{{ post.title}}</a>
    </li>
  {% endfor %}
</ul>

