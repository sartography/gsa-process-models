{% if api_keys | length > 0 %}
| Name | Created at |  |
| ---- | ---------- | ---- |
{% for item in api_keys %}
| {{ item["name"] }} | {{ item["created_at"] }} | [View](/extensions/api-keys:show?name={{ item["name"] }}) |
{% endfor %}
{% endif %}

<hr />
