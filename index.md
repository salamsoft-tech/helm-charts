---
layout: default
---

## Getting Started

{{ site.description }}

You can add this repository to your local helm configuration as follows :

```console
$ helm repo add {{ site.repo_name }} {{ site.url }}
$ helm repo update
```

## Charts

| Chart | Description |
|-------|-------------|
{% for helm_chart in site.data.index.entries %}
{%- assign title = helm_chart[0] -%}
{%- assign chart = helm_chart[1] | sort: "created" | last -%}
{%- assign description = chart.description -%}
| [{{ title }}](https://github.com/{{ site.repo_name }}/helm-charts/tree/main/charts/{{ chart.name }}) | {{ description }} |
{% endfor %}
