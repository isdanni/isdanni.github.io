---
layout: gallery
support: [jquery, gallery]
---

<h1>San francisco</h1>
At the end of our wonderful three week road trip at the West Coast of the US, we spent about four days in the wonderful city of San Francisco. The city's well known for the Golden Gate Bridge and its fog, but has so much more up its sleeve!

This is an example gallery. All images licensed under [CC-BY-NC-SA license][license]. Check the [Git Repo][repo] for a copy of this license.

[license]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[repo]: https://github.com/opieters/jekyll-gallery-example



{% assign count = 0 %}
{% assign align = "left" %}
{% for picture in site.data.galleries.san-francisco.pictures %}
{% if count == 0 %}<div class="row">{% endif %}
  <div class="half-width gallery-preview {{ align }}">
    <h6> </h6>
    <a href="{{ site.url }}{{ site.baseurl }}/assets/gallery/san-francisco/{{ picture.original }}">
      <img alt="{{ picture.title }}" src="{{ site.url }}{{ site.baseurl }}/assets/gallery/san-francisco/{% if picture %}{{ picture.original }}{% else %}{{ picture.directory }}{% endif %}/{{ picture.preview.thumbnail }}" />
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
