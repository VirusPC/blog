<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-DE96ZG1N0C"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-DE96ZG1N0C');
</script>

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
<br/>

> [语雀](https://www.yuque.com/pengcheng-fuigs)
<br/>
