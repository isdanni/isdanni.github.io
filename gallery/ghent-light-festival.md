---
layout: gallery
support: [jquery, gallery]
---

<h1>Ghent Light Festival</h1>
<!--
{% include gallery-layout.html gallery=site.data.galleries.ghent-light-festival-1 id_number=1 %}
-->

{% assign count = 0 %}
{% assign align = "left" %}
{% for picture in site.data.galleries.ghent-light-festival-1.pictures %}
{% if count == 0 %}<div class="row">{% endif %}
  <div class="half-width gallery-preview {{ align }}">
    <h6> </h6>
    <a href="{{ site.url }}{{ site.baseurl }}/assets/gallery/ghent-light-festival/part1/{{ picture.original }}">
      <img alt="{{ picture.title }}" src="{{ site.url }}{{ site.baseurl }}/assets/gallery/ghent-light-festival/part1/{% if picture %}{{ picture.original }}{% else %}{{ picture.directory }}{% endif %}/{{ picture.preview.thumbnail }}" />
    </a>
  </div>
{% if count == 1 %}</div>{% endif %}
{% assign count = count | plus: 1 %}
{% assign align = "right" %}
{% if count >= 2 %}
{% assign align = "left" %}
{% assign count = 0 %}
{% endif %}
{% endfor %}

{% if count != 1 %}
</div>
{% endif %}


<!--
The pictures from part two:

{% include gallery-layout.html gallery=site.data.galleries.ghent-light-festival-2 id_number=2 %}
-->

This is an example gallery. All images licensed under [CC-BY-NC-SA license][license]. Check the [Git Repo][repo] for a copy of this license.

[license]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[repo]: https://github.com/opieters/jekyll-gallery-example
