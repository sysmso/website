---
title: "Conteneurs et runtime pour le HPC"
date: 2021-01-20T13:48:06+02:00
categories: ["presentations"]
tags: ["docker","ci/cd","kubernetes","gitlab","podman"]
language: fr
images: []
resources:
- name: "featured-image"
  src: "container.jpg"

lightgallery: true
---

Lors de l'ANF User Support Tools for HPC 2021 j'ai été sollicité pour présenter les technologies de conteneurs et de runtime pour le HPC.

<!--more-->

La vidéo de cette présentation est disponible ici : 

<iframe width="560" height="315" src="https://www.canal-u.tv/chaines/groupecalcul/embed/113917?t=0" allowfullscreen></iframe>

## Description

### Contexte

Les besoins en calcul numérique ne cessent de progresser. Le calcul intensif ne concerne plus seulement les applications traditionnelles (climat, astrophysique, mécanique des fluides, etc.) mais voit la montée en puissance d'autres disciplines (modélisation du vivant, nanotechnologies, etc.) et l'émergence de nouvelles communautés (santé, SHS). Compte tenu de l'évolution des technologies et de leur coût, la puissance de calcul déployée, en particulier au sein d'infrastructures mutualisées comme les mésocentres, est amenée à s'amplifier encore dans les années à venir. L'arrivée des accélérateurs complexifie les architectures en introduisant la notion de supercalcul hétérogène. Ainsi, les administrateurs et les utilisateurs des plateformes HPC sont confrontés à de nouvelles problématiques à la fois techniques, scientifiques et administratives. Cette évolution s'accompagne d'une multiplication des outils (middleware, compilateurs, applicatifs, etc.) qui rend encore plus complexe leur mise à disposition sur les machines de calcul pour les utilisateurs.

### Enjeux

Ces dernières années ont vu l'explosion de techniques destinées à faciliter le portage, la maintenance et les mises à jour des applications des utilisateurs. Ainsi, l'avènement de nouveaux types de systèmes de paquets ou des conteneurs d'application ouvrent des perspectives intéressantes pour le déploiement, mais aussi la reproductibilité des installations de logiciels. Ces nouveaux outils sont devenus incontournables pour l'administration système de machines de calcul. Ce sont aussi des avancées importantes pour les utilisateurs. D'une part, ils permettent de porter facilement les codes sur l'échelle de la pyramide du calcul : de l'ordinateur portable aux machines de niveau européen. D'autre part, ils fonctionnent particulièrement bien avec les systèmes d'intégration continue qui sont devenus incontournables en génie logiciel. Enfin, ils s'accompagnent d'avancées dans d'autres domaines connexes comme le stockage ou les ordonnanceurs de tâches.

### Objectif

L'objectif de cette action nationale de formation (ANF) est de poursuivre les actions d'exploration et de mise à jour des connaissances qui ont été initiées lors de la première session de UST4HPC en 2018. Cette année, le fil rouge sera le thème de la reproductibilité dans le contexte HPC. Nous visons en particulier l'utilisation des outils de packaging, des conteneurs et des notebooks Jupyter pour le HPC, les questions de sécurité et les systèmes d'intégration continue, le tout dans un contexte de calcul intensif. À noter que la participation à l'ANF 2018 n'est pas un pré-requis pour l'ANF 2021.

Les 4 grands axes du programme de formation sont :

- la reproductibilité, le packaging et les problématiques de sécurité associées
- les conteneurs et runtimes pour le HPC
- les notebooks jupyter dans un environnement HPC
- l'intégration continue dans le contexte HPC