---
layout: default
---

<article class="tag">                                                                                                
  <header class="post-header">
    <h1 class="post-title">{{ page.title | escape }}</h1>

{% if page.parents %}
{% assign parents = '' | split: '' %}
{% for pslug in page.parents %}
    {% assign parent = site.tags | where: "slug", pslug | first %}
    {% unless parent %}{% continue %}{% endunless %}
    {% capture plink %}<a href="{{ parent.url }}">{{ parent.title }}</a>{% endcapture %}
    {% assign parents = parents | push: plink %}
{% endfor %}
{% if parents.size > 0 %}
<em>Supertopic{% if parents.size > 1 %}s{% endif %}: {{ parents | array_to_sentence_string }}</em>
{% endif %}{% endif %}

{% assign course = site.courses | where: "slug", page.slug | first %}
{% if course %}
<p><em>For a more structured walkthrough of this content, see <a href="{{ course.url }}">the "{{ course.title }}" course</a>.</em></p>
{% endif %}
    
<div class="tag_desc">{{ content }}</div>

{% capture cfilter %}t.parents contains "{{ page.slug }}"{% endcapture %}
{% assign childtags = site.tags | where_exp: "t", cfilter %}
{% assign children = '' | split: '' %}
{% for child in childtags %}
    {% capture clink %}<a href="{{ child.url }}">{{ child.title }}</a>{% endcapture %}
    {% assign children = children | push: clink %}
{% endfor %}
{% if children.size > 0 %}
{% capture subtopics %}<div class="subtopics">Subtopic{% if children.size > 1 %}s{% endif %}: {{ children | array_to_sentence_string }}</div>{% endcapture %}{{ subtopics }}
{% endif %}
  </header>

  <div class="post-content">
{% assign all_content = site.content | where_exp: "c", "c.status != 'rejected'" %}
{% assign categories = site.data.content_categories %}
{% capture filter_exp %}c.tags contains '{{ page.slug }}'{% endcapture %}
{% for catslug in categories %}
  {% capture cat_filter %}c.path == "content/{{ catslug }}.md"{% endcapture %}
  {% assign category = site.pages | where_exp: "c", cat_filter | first %}
  {% capture cat_filter %}c.path contains '/{{ catslug }}/'{% endcapture %}
  {% assign cat_content = all_content | where_exp: "c", cat_filter %}
  {% assign course_content = cat_content | where: "course", page.slug %}
  {% assign tag_content = cat_content | where_exp: "c", filter_exp | sort: "year" %}
  {% if course_content.size > 0 or tag_content.size > 0 %}
  <h3 id="{{ catslug }}">{% include content_icon.html category=cat_slug %} {{ category.title }}</h3>
  {% if course_content.size > 0 %}
    {% include featured_content_list.html contents=course_content %}
  {% endif %}{% if tag_content.size > 0 %}
  <div class="tagged_content">
  {% if course_content.size > 0 %}<h4>See also:</h4>{% endif %}
  {% for c in tag_content %}
    <div>{% include simple_content_title.html content=c %}</div>
  {% endfor %}
  </div>
  {% endif %}{% endif %}
{% endfor %}
  </div>
{{ subtopics }}
</article>
