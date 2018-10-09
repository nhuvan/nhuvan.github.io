---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---
If you like the format of the EarthArXiv preprints included here, see <u>[preprint-template.tex](https://github.com/brenhinkeller/preprint-template.tex)</u>

Most of these publications may also be found on my <u><a href="https://scholar.google.com/citations?user=NZVGPiwAAAAJ&hl=en&oi=ao">Google Scholar profile</a>.</u>
---
{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}

