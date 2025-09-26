# Le-on-sur-les-probes
Petit cour sur l'utilisation des probes dans radiant Modtools de Black Ops 3 basé sur la documentation officiel.

Cours complet — Probes (workflow d’éclairage et de réflexion)
1) Notions fondamentales

Objectif : couvrir pratiquement toutes les surfaces avec un type de probe pour piloter les réflexions et la lumière rebondie. 

Deux niveaux : Global probe (globale) et Local probes (locales). La globale sert de filet de sécurité là où aucune locale n’existe, mais son rendu est moins qualitatif à courte distance; privilégier des locales dès qu’il y a des surfaces brillantes. 

2) Global Probe — Rôle et création

Création automatique : la création d’un sun-volume génère une global probe au centre de ce volume. Toutes les réflexions et le bounce lighting proviennent alors de cette globale, sauf si une locale override la zone. 

Priorité : la globale a une priorité inférieure aux locales; pas de blend visible entre globale et locales hors bords de blend des locales. 

3) Positionnement de la Global Probe

On peut déplacer la globale en la ciblant sur un autre objet (ex. info_null). L’exemple du PDF montre, via une sphère chrome, l’impact majeur sur l’image réfléchie et la dominante de couleur de la scène. 

Exigence : placer la globale à un “moyen idéal” du volume solaire; éviter un point enfoui dans la géométrie ou trop haut, sinon le bounce devient non représentatif. 

4) Occlusion de la Global Probe — Grille 3D

La globale possède une texture 3D d’occlusion (pensez à une lightmap 3D / point-cloud interne au sun-volume). Sans cette grille, le volume recevrait un bounce quasi uniforme. La grille introduit ombres/occlusions dans le bounce et les réflexions. 

Densité de grille : visualisable dans Radiant via le debug “probe blends” (comparaison low vs high). La densité se règle sur le sun-volume. Aucun coût perf, mais consommation mémoire en hausse avec la densité. 

5) Local Probes — Usage tactique

Principe issu du document : les locales remplacent la globale dans leur zone, mieux adaptées aux environnements brillants et aux proximités d’objet. But : approcher fidèlement la réalité locale. 

6) Méthode robuste — Pas à pas
Phase A — Cadre global

Créer le sun-volume → valide la global probe automatique. 

Cibler la globale sur un info_null placé à un point médian fiable (ni enfoui, ni trop haut). Vérifier avec une sphère chrome placée à proximité. Ajuster jusqu’à obtenir un reflet “moyen” crédible. 

Régler la densité d’occlusion sur le sun-volume. Démarrer low, contrôler l’uniformité; augmenter si nécessaire pour affiner l’ombre/occlusion. Surveiller la mémoire si vous multipliez les volumes. 

Phase B — Raffinement local

Placer des local probes aux zones critiques : couloirs brillants, intérieurs restreints, surfaces métalliques, vitrages. Rappel : la locale override la zone et ne blend qu’à son bord. 

Échantillonner : déplacer temporairement une sphère chrome à l’intérieur de chaque locale pour comparer l’apport vs globale. Ré-ajuster volumes/positions. 

Phase C — Vérification

Activer le debug “probe blends” dans Radiant pour visualiser la densité et les zones de transition. Inspecter les raccords global/local, surtout aux bords. 

Contrôle d’uniformité du bounce : si une zone reçoit une dominante aberrante (trop ciel, trop sol), revoir ciblage de la globale ou densité de grille. 

7) Bonnes pratiques synthétiques

Maximiser la couverture en locales dans les zones proches du joueur; garder la globale comme moyenne de secours. 

Toujours vérifier l’image de réflexion via un chrome sphere check quand vous déplacez la globale (ciblage info_null). 

Éviter que la globale “regarde” des environnements non représentatifs (haut dans le ciel, au cœur d’un mesh). 

Densité d’occlusion : augmenter uniquement si nécessaire, car le coût mémoire croît; les perfs ne bougent pas selon le PDF. 

8) Checklist opérationnelle

 Sun-volume créé → global probe en place. 

 Ciblage de la globale vers info_null → reflet moyen validé par sphère chrome. 

 Densité d’occlusion définie sur le sun-volume; debug “probe blends” inspecté. 

 Locales posées sur zones brillantes/proches; transitions propres aux bords. 

 Passages contrôlés : pas de bounce farfelu, pas de dominante incohérente. 

9) Dépannage ciblé

Symptôme → Action

Réflexions “ciel only”, trop bleues → Descendre la globale ou recibler sur un info_null plus représentatif. 

Zones mates “lavées” → Ajouter une locale dédiée; la globale est un moyen, pas une solution de proximité. 

Bounce uniforme, sans modelé → Monter la densité d’occlusion dans le sun-volume; vérifier en probe blends. 

Artefacts aux raccords → Vérifier bord de blend des locales et l’ordre de priorité (global < local). 

Mémoire en hausse → Réduire densités inutiles ou fusionner des volumes trop granulaires.
