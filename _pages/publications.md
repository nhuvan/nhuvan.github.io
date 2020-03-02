---
layout: archive
title: "Recherche & Publications"
permalink: /publications/
author_profile: true
---

## Recherche

Mes recherches se focalisent autour de deux thématiques :

### La fouille multimodale de bases d'images et de vidéos
Au cours de ma thèse, j'ai pu approfondir mes connaissances dans le domaine de l'indexation et de la recherche interactive d'images par le contenu au travers du projet IDEA (avec les professeurs Jean-Marc Ogier, Salvatore Tabbone et Alain Boucherde l’AUF). Pendant cette période, j'ai réalisé plusieurs recherches: 1) L'extraction des caractéristiques visuelles utilisé dans le domaine de la recherche par le contenu (signatures globales, signatures locales) ; 2) le développement de méthodes de recherche interactive d'images à court-terme ; 3) le développement de méthodes d'indexation et de recherche interactive d'images à long-terme.

Par la suite, dans le  cadre  de  mon  premier  contrat  d’ingénieur  de  recherche (avec le prof. Jean-Marc Ogier), je me suis impliqué dans le projet PEDIVHANDI. Dans le carde de ce projet, mon objectif était d'explorer des méthodes d'indexation et de recherche multimodale de vidéos de cours. J'ai travaillé également avec différentes techniques  de traitement d'images : l'analyse de la mise en page, la séparation objets/fond, la détection de texte et de graphiques. 

### L'apprentissage automatique pour la reconnaissances de formes

<!-- Le laboratoire L3i, reconnu au niveau international sur l’analyse de document, a lancé en 2011 le projet e-bdthèque dont l’objectif est de valoriser les corpus de Bande Dessiné numérisées. J'ai commencé mon deuxieme postdoc dans l'équipe BD en 04/2016 (avec le prof. Jean-Christophe Burie, prof. Arnaud Revel et prof Karell Bertet). Dans ce contexte, j'ai travaillé principalement sur la détection automatique de cases de BD, de bulles, de dialogues, du texte, des personnages, des décors et l'analyse du contenu semantique. Je construis des algorithmes d’analyse d'images se basant sur l'apprentissage profond, en les combinant avec des algorithmes de traitement d'images classique, et priotiser le contexte semi-supervisé. -->

Depuis 04/2016, je suis intéréssé par la nouvelle tendance de l'apprentissage profond. J'ai participé au projet e-BDthèque du prof. Jean-Christophe Burie, je commence à me tourner vers ce nouvelle approche qui ouvrent de nouvelles opportunités pour l'analyse de images de Bande Dessiné numérisées : la détection automatique de cases de BD, de bulles, de dialogues, du texte, des personnages et des décors. 
En pratique, je construis des algorithmes d’analyse d'images de BD se basant sur l'apprentissage profond, en les combinant avec des algorithmes de traitement d'images classique, et priotiser le contexte semi-supervisé, faiblement supervisé.

Je participe également au groupe de recherche du prof. Antoine Doucet sur la détection et la correction de textes OCR (Optical Character Recognition). 

## Publications 

<!-- {% if author.googlescholar %} -->
La plupart de ces publications se trouvent également sur mon <u><a href="{{author.googlescholar}}">profil Google Scholar</a>.</u>
<!-- {% endif %} -->

<!-- <br>If you like the format of the preprints included here, see <u><a href="https://github.com/brenhinkeller/preprint-template.tex">preprint-template.tex</a></u> -->

---
{% include base_path %}
<b>Journals:</b>
<br/>
  <ol>
    {% for post in site.publications reversed %}
      {% if post.journal %}
        {% include archive-single-cv.html %}
      {% endif %}
    {% endfor %}
  </ol>
<br/>


---
<b>Conferences:</b>
<br/>
<ol>{% for post in site.publications reversed %}
      {% if post.journal%}
        
      {% else %}
        {% include archive-single-cv.html %}
      {% endif %}
    {% endfor %}</ol>
<br/>
