---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---
<div class="">
	<ul>
	  {% for post in site.posts %}
		<div class="li_container">

<!--
			<div class="li_container_date">{{ post.date | date: "%Y" }}年{{ post.date | date: "%m" }}月{{ post.date | date: "%d" }}</div>
			<li>
			  
			 <a style="color:black;" href="{{ post.url }}">{{ post.title }}</a>
			 
			</li>
-->		


		<div class = "post-link-in-posts">
		<a id = "uniqueid"  class="listing-item" href="{{ site.url }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}<time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time></a>
		</div>
		

			<!--
			<hr style=" height:2px;border:none;border-top:1px dotted #9dacad;" />
			-->
			<!--
			<hr style='background-color:blue;'>
			-->
		</div>
	{% endfor %}
	</ul>
</div>