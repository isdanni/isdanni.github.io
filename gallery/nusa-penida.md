---
layout: gallery
title: Nusa Penida
support: [jquery, gallery]
---

Summer 2018. All photos taken by me and licensed under [CC-BY-NC-SA license][license].

[license]: http://creativecommons.org/licenses/by-nc-sa/4.0/



{% assign count = 0 %}
{% assign align = "left" %}
{% for picture in site.data.galleries.nusa-penida.pictures %}
{% if count == 0 %}<div class="row">{% endif %}
  <div class="half-width gallery-preview {{ align }}">
    <h6> </h6>
    <a href="{{ site.url }}{{ site.baseurl }}/assets/gallery/nusa-penida/{{ picture.original }}">
      <img alt="{{ picture.title }}" src="{{ site.url }}{{ site.baseurl }}/assets/gallery/nusa-penida/{% if picture %}{{ picture.original }}{% else %}{{ picture.directory }}{% endif %}/{{ picture.preview.thumbnail }}" />
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
{% endif %}
