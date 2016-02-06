---
layout:  page
title:   Documentation
---

Please select library part to show documentation for:

<ul>
  {% assign sorted_pages = site.pages | sort:"name" %}
  {% for node in sorted_pages %}
	{% if node.categories contains "docs" %}
	  <li class="sidebar-nav-item{% if page.url == node.url %} active{% endif %}">
		<a href="{{ node.url }}">{{ node.menu-title }}</a>
	  </li>
	{% endif %}
  {% endfor %}
</ul>

The new version of documentation is being created at [http://docs.qa-tools.io](http://docs.qa-tools.io) website.
