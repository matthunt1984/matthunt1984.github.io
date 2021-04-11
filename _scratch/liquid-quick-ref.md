https://shopify.github.io/liquid/ 

https://jekyllrb.com/docs/variables/
https://jekyllrb.com/docs/liquid/filters/

{% assign image_files = site.pages | where: "name", "media-archive-tf.md" %}
{% for myimage in image_files %}
  {{ myimage.name }}
{% endfor %}

Find doesn't seem to work in the same way as where
{% assign image_files = (site.pages | find: "name", "media-archive-tf.md") %}