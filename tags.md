---
layout: page
title: Tags
permalink: /tags/
---

<div class="posts">
<div class="post">
{% assign sorted_tags = site.tags | sort %}
{% for tag in sorted_tags %}
  {% assign t = tag | first %}
  <!-- {% assign posts = tag | last %} -->
  <span class="tag"><a href="{{ site.baseurl }}/tags#{{t | downcase | replace:" ","-" }}">{{t | downcase | replace:" ","-" }}</a><span>&nbsp;&times;&nbsp;{{ posts | size }}</span></span>
{% endfor %}

<br/>
<br/>

{% for tag in sorted_tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}
  {% assign tname = t | downcase %}
	<h3><a name="{{ tname }}"></a>{{ tname }}</h3>
	<ul style="list-style-type: none; padding-left: 0">
	{% for post in posts %}
  		{% if post.tags contains t %}
  		<li>
    		<a href="{{ post.url }}">{{ post.title }}</a>
    		<time datetime='{{ post.date | date: "%B %-d, %Y"  }}'>
    			{{ post.date | date: "%B %-d, %Y"  }}
      	</time>
  		</li>
  		{% endif %}
	{% endfor %}
	</ul>
{% endfor %}
</div>
</div>



