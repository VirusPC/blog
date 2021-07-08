{% for category in site.categories %}
  <h3>{{ category[0] }}({{category[1].size}})</h3>
  <ul>
    {% for post in category[1] %}
      <li>
        <a href="{{site.baseurl}}/{{post.url}}">{{ post.title}}</a>
      </li>
    {% endfor %}
  </ul>
{% endfor %}

---

> [旧笔记](https://github.com/VirusPC/old-notes)
