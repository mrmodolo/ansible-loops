# Ansible Jinja Warrior - Mastering "Loop Variable Scope"
https://www.arctiq.ca/our-blog/2017/2/16/ansible-jinja-warrior-loop-variable-scope/

## Manipulando templates Jinja com Ansible

## Mudanças no escopo para novas versões do Jinja
https://stackoverflow.com/questions/7537439/how-to-increment-a-variable-on-a-for-loop-in-jinja-template?rq=1

## Alterações nas configurações do Ansible

```ini
[defaults]
jinja2_extensions = jinja2.ext.do,jinja2.ext.i18n
```

## Solução original (não funciona mais)

```jinja
{% for colour in colours %}
  Colour number {{ loop.index }} is {{ colour.name }}.
{% set colour_count = 0 %}
{% for person in people if person.fav_colour == colour.name %}
{% set colour_count = colour_count + 1 %}
{% do colour.update({'people_count':colour_count}) %}
{% endfor %}
  Currently {{ colour.people_count }} people call {{ colour.name }} their favourite.
    And the following are examples of things that are {{ colour.name }}:
{% for item in colour.things %}
    - {{ item }}
{% endfor %}

{% endfor %}
```

## Solução usando namespaces

```jinja
{% for colour in colours %}
  Colour number {{ loop.index }} is {{ colour.name }}.
{% set colour_count = [] %}
{% for person in people if person.fav_colour == colour.name %}
{% do colour_count.append(1) %}
{% do colour.update({'people_count':colour_count|length}) %}
{% endfor %}
  Currently {{ colour.people_count }} people call {{ colour.name }} their favourite.
  And the following are examples of things that are {{ colour.name }}:
{% for item in colour.things %}
    - {{ item }}
{% endfor %}

{% endfor %}
```

## Solução usando um valor não scalar

```jinja
{% for colour in colours %}
  Colour number {{ loop.index }} is {{ colour.name }}.
{% set colour_count = namespace(i=0) %}
{% for person in people if person.fav_colour == colour.name %}
{% set colour_count.i = colour_count.i + 1 %}
{% do colour.update({'people_count':colour_count.i}) %}
{% endfor %}
  Currently {{ colour.people_count }} people call {{ colour.name }} their favourite.
  And the following are examples of things that are {{ colour.name }}:
{% for item in colour.things %}
    - {{ item }}
{% endfor %}

{% endfor %}
```
