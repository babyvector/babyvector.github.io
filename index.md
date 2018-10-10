---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---
<div class="">
	<ul>
	  {% for post in site.posts %}
		<div class="li_container">
			<div class="li_container_date">{{ post.date | date: "%Y" }}年{{ post.date | date: "%m" }}月{{ post.date | date: "%d" }}</div>
			<li>
			  
			 <a style="color:black;" href="{{ post.url }}">{{ post.title }}</a>
			 
			</li>
			
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