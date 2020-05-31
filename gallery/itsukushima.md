---
layout: gallery
title: Itsukushima Shrine
support: [jquery, gallery]
---

New Year's Eve 2019. All photos taken by me and licensed under [CC-BY-NC-SA license][license].

[license]: http://creativecommons.org/licenses/by-nc-sa/4.0/



{% assign count = 0 %}
{% assign align = "left" %}
{% for picture in site.data.galleries.itsukushima.pictures %}
{% if count == 0 %}<div class="row">{% endif %}
  <div class="half-width gallery-preview {{ align }}">
    <h6> </h6>
    <a href="{{ site.url }}{{ site.baseurl }}/assets/gallery/itsukushima/{{ picture.original }}">
      <img alt="{{ picture.title }}" src="{{ site.url }}{{ site.baseurl }}/assets/gallery/itsukushima/{% if picture %}{{ picture.original }}{% else %}{{ picture.directory }}{% endif %}/{{ picture.preview.thumbnail }}" />
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
