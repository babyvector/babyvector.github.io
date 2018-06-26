---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: categories
---
<div class="">
	<div class="blog_list">
	{% for cat in site.categories %}
	<a href="#{{ cat[0] }}" title="{{ cat[0] }}" rel="{{ cat[1].size }}">{{ cat[0] }}</a>
	{% endfor %}
	</div>
	<section class="inner">
	{% for cat in site.categories %}
	  <li class="category-listing-seperator" id="{{ cat[0] }}">{{ cat[0] }}（共{{ cat[1].size }}篇）</li>
		{% for post in cat[1] %}
		<div class = "post-link-in-posts">
		<a id = "uniqueid"  class="listing-item" href="{{ site.url }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}<time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time></a>
		</div>
		{% endfor %}
	{% endfor %}
	</section>
</div>