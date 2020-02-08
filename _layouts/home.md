---
layout: default
---

{%- if page.title -%}
  <h1>{{ page.title }}</h1>
{%- endif -%}

{{ content }}

{% if site.paginate %}
  {% assign posts = paginator.posts %}
{% else %}
  {% assign posts = site.posts %}
{% endif %}

{%- if posts.size > 0 -%}
  {%- if page.list_title -%}
    <h2>{{ page.list_title }}</h2>
  {%- endif -%}
  <ul>
    {%- assign date_format = "%b %-d, %Y" -%}
    {%- for post in posts -%}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
        ({{ post.date | date: date_format }})
      </li>
    {%- endfor -%}
  </ul>

  {% if site.paginate %}
    <ul class="pagination">
      <li class="previous-page">
        {%- if paginator.previous_page %}
          <a href="{{ paginator.previous_page_path | relative_url }}">
            {{ paginator.previous_page }}
          </a>
        {%- else %}
          •
        {%- endif %}
      </li>
      <li class="current-page">{{ paginator.page }}</li>
      <li class="next-page">
        {%- if paginator.next_page %}
          <a href="{{ paginator.next_page_path | relative_url }}">
            {{ paginator.next_page }}
          </a>
        {%- else %}
          •
        {%- endif %}
      </li>
    </ul>
  {%- endif %}
{%- endif -%}
