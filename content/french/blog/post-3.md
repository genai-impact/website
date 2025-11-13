---
title: "Améliorations majeures de notre méthodologie"
meta_title: ""
description: ""
date: 2025-11-13T08:00:00Z
image: "/images/content/post_3/plot.png"
categories: ["Impacts environnementaux", "Développement GenAI Impact"]
author: "GenAI Impact"
tags: ["IA Générative", "EcoLogits"]
draft: false
---

# Annonce d’améliorations majeures de notre méthodologie

Nous sommes ravis de partager des mises à jour significatives de la méthodologie intégrée à EcoLogits pour évaluer l’empreinte environnementale des grands modèles de langage (LLMs). Il s’agit de notre première mise à jour méthodologique majeure depuis le lancement d’EcoLogits au début de l’année 2024. Grâce à ces changements, vous pouvez vous attendre à des estimations plus précises de la consommation d’énergie, ainsi qu’à de nouveaux critères d’impact liés à la consommation d’eau et bien plus encore.

Dans cet article, nous présentons les principaux changements méthodologiques sans entrer dans chaque détail. Nous comparons également notre approche avec les récentes déclarations de Google, Mistral AI et OpenAI.

## Quelles sont les nouveautés ?

### Nouveau benchmark énergétique et traitement en parallèle

Une mise à jour majeure de cette version est l’adoption du [ML.ENERGY Leaderboard](https://ml.energy/leaderboard/?__theme=light) ([Chung et al., 2025](https://arxiv.org/abs/2505.06371)) comme benchmark énergétique, remplaçant le [LLM-Perf Leaderboard](https://huggingface.co/spaces/optimum/llm-perf-leaderboard) de Hugging Face. Développé par des chercheurs de l’Université du Michigan, ce benchmark mesure la consommation d’énergie des modèles d’IA dans des environnements similaires à la production, en alignement étroit avec la méthodologie d’EcoLogits, qui se concentre sur les services d’API des fournisseurs d’IA.

Ce nouveau benchmark introduit plusieurs améliorations :

- Le **traitement en parallèle des requêtes** prend désormais en compte le traitement parallèle, ce qui n’était pas abordé auparavant.
- Un **moteur d’inférence moderne** utilisant vLLM, optimisé pour l’inférence par rapport au backend PyTorch.
- Des **GPU mis à jour** avec des H100, plus efficaces que les A100.

Le résultat est une réduction substantielle des estimations de consommation d’énergie. Par exemple, la modélisation d’une requête avec le modèle Mistral Small 3.2 générant 400 tokens de sortie montre désormais une estimation de consommation d’énergie du GPU de 0,022 Wh, contre 1,43 Wh auparavant (une diminution de 65 fois). Cette baisse est entièrement justifiée par les avancées en matière d’efficacité énergétique que tous les fournisseurs d’IA poursuivent.

Dans la section suivante, nous comparons EcoLogits avec les récentes déclarations des fournisseurs d’IA, démontrant comment ce nouveau benchmark produit des résultats plus cohérents.

### Empreinte de consommation d’eau

Notre approche a toujours mis l’accent sur les impacts environnementaux multicritères, car les préoccupations environnementales vont au-delà des émissions de gaz à effet de serre. Le secteur du numérique, en particulier, a des impacts significatifs liés à la toxicité écologique et humaine, ainsi qu’à la consommation de ressources. Nous avons déjà trois critères d’impact :

- **Potentiel de Réchauffement Global (GWP)** – également appelé émissions de GES ou émissions de carbone.
- **Potentiel de Déplétion Abiotique pour les Éléments (ADPe)** – lié à la consommation de minéraux et de métaux.
- **Énergie Primaire (PE)** – consommation d’énergie provenant de sources primaires comme les combustibles fossiles.

Dans cette version, nous introduisons un nouveau critère : **l’empreinte de consommation d’eau (WCF)**. Pour les centres de données et les centrales électriques, l’eau consommée fait référence à l’eau prélevée d’une source et non restituée. Par exemple, l’eau évaporée dans les tours de refroidissement est considérée comme consommée, tandis que l’eau prélevée dans une rivière et ensuite restituée ne l’est pas. Pour plus d’explications, nous recommandons l’[épisode #66 du podcast Green IO](https://podcast.green.io.tech/e/183m79v8-66-66-data-centers-and-water-in-the-us-a-complex-story-with-mohammad-islam-and-shaolei-ren) avec Mohammad Islam et Shaolei Ren.

Notre implémentation est basée sur la modélisation de [Li et al., 2025, « Making AI Less ‘Thirsty’ »](https://dl.acm.org/doi/10.1145/3724499). Nous prenons en compte l’eau consommée à la fois dans les centres de données pour le refroidissement et lors de la production d’électricité. Cela repose sur la métrique **Water Usage Effectiveness (WUE)**, qui quantifie l’eau consommée par unité d’électricité. Nous définissons un **on-site WUE** (à partir des déclarations des fournisseurs) et un **off-site WUE** (à partir du [World Resources Institute](https://www.wri.org/)), qui agrège les données de consommation d’eau par pays liées à la génération d’électricité.

Actuellement, cet indicateur **n’est pas** calculé pour la fabrication des serveurs et des GPU. Cela signifie que la consommation d’eau pour la phase de fabrication n’est pas encore disponible en raison de données limitées, mais nous prévoyons de l’inclure dans les futures mises à jour.

### Configuration des fournisseurs

La méthodologie d’EcoLogits est conçue pour être personnalisable, permettant une modélisation détaillée des charges de calcul réelles de l’IA. Dans cette version, nous tirons parti de cette flexibilité pour intégrer les configurations spécifiques des centres de données des fournisseurs. Nous proposons désormais des valeurs par défaut améliorées pour la géolocalisation des centres de données, les métriques **Power Usage Effectiveness (PUE)** et **WUE**, issues des déclarations des fournisseurs de cloud et d’IA.

Voici un extrait des valeurs actuelles utilisées pour les services d’API, qui peuvent différer des services Web comme ChatGPT, Claude ou Le Chat :

| Fournisseur d’IA | Fournisseur de Cloud | Localisation | PUE | WUE (L/kWh) |
| --- | --- | --- | --- | --- |
| Anthropic | AWS, Google | USA | 1,09 - 1,14 | 0,13 - 0,99 |
| Cohere | Google | USA | 1,09 | 0,99 |
| Google | Google | USA | 1,09 | 0,99 |
| HuggingFace Hub | AWS, Google | USA | 1,09 - 1,14 | 0,13 - 0,99 |
| Mistral AI | Microsoft | SWE | 1,16 | 0,09 |
| OpenAI | Microsoft | USA | 1,20 | 0,569 |
| Azure OpenAI | Microsoft | USA | 1,20 | 0,569 |

Nous prévoyons de donner aux utilisateurs d’EcoLogits un contrôle total sur ces paramètres à l’avenir. Pour l’instant, nous nous appuyons sur des « bonnes valeurs par défaut » basées sur les données disponibles des fournisseurs. Toutes les sources sont documentées dans le [supplemental material](https://docs.google.com/spreadsheets/d/1XkPTkrGxpwWpIVIxpVvgRJuInSZsqbndTQbFGcHhdd0/).

### Autres changements mineurs

**Nouvelle source de données pour l’intensité carbone**

Nous avons remplacé la source de données ADEME Base Empreinte pour les intensités carbone de l’électricité par une base de données d’Our World in Data. Cette mise à jour fournit des chiffres d’intensité carbone plus récents, améliorant la précision des estimations de GWP. Nous continuons à utiliser ADEME Base Empreinte pour l’ADPe et la PE en raison du manque d’alternatives ouvertes et à jour.

**Estimation du nombre de GPU**

Auparavant, nous estimions le nombre minimum de GPU nécessaires pour héberger un modèle en fonction de sa taille, de son niveau de quantification et d’un facteur de surcharge de 1,2. Nous arrondissons désormais cette valeur au multiple de deux le plus proche pour mieux refléter les déploiements réels, où les serveurs utilisent généralement 1, 2, 4 ou 8 GPUs. Nous avons également mis à jour la quantification par défaut à 16 bits pour nous aligner sur les exigences matérielles de notre nouveau benchmark énergétique.

**Impacts incorporés et durée de vie du matériel**

Pour nous aligner sur le ML.ENERGY Leaderboard, nous avons mis à jour le matériel par défaut avec des GPU H100 et des instances `p5.48xlarge`. Pour le GWP, nous utilisons l’[empreinte carbone officielle du produit NVIDIA pour le HGX H100](https://images.nvidia.com/aem-dam/Solutions/documents/HGX-H100-PCF-Summary.pdf), tandis que nous nous appuyons sur la [méthodologie de Boavizta](https://doc.api.boavizta.org/) pour les autres composants du serveur.
Nous avons également réduit la durée de vie du matériel de 5 à 3 ans, reflétant les [rapports récents](https://www.tomshardware.com/pc-components/gpus/datacenter-gpu-service-life-can-be-surprisingly-short-only-one-to-three-years-is-expected-according-to-unnamed-google-architect) sur la courte durée de vie des GPU dans des contextes d'utilisation IA.

**Meilleures estimations de latence dans le calculateur EcoLogits**

Nous avons amélioré les calculs de latence dans le [EcoLogits Calculator](https://huggingface.co/spaces/genai-impact/ecologits-calculator) en intégrant les données de débit de tokens des modèles et des fournisseurs provenant d’[OpenRouter](https://openrouter.ai/). Cela permet une estimation plus précise de la durée d’exécution et, par conséquent, des impacts environnementaux. Notez que ce changement n’affecte pas la bibliothèque Python EcoLogits, qui repose sur des mesures de latence directes.

## Comparaison avec d’autres déclarations des fournisseurs d’IA

Au cours de l’été 2025, Google, Mistral AI et OpenAI ont publié de nouvelles informations sur les impacts environnementaux. Bien que ces déclarations manquent d'une transparence totale, elles ont permis de valider notre orientation méthodologique et ont confirmé que notre approche précédente était obsolète pour ces fournisseurs.

### Comparaison avec Google et OpenAI

En juin et août 2025, OpenAI et Google ont publié des données sur les impacts environnementaux de leurs services d’IA. Sam Altman, PDG d’OpenAI, a donné la consommation d’énergie et d’eau d’une « requête moyenne ChatGPT », bien que les détails sur la méthodologie aient été totalement absents. Le rapport de Google incluait l’énergie, l’eau et les émissions de GES pour une « requête médiane de texte Gemini Apps ». Les deux fournisseurs ont rapporté des chiffres similaires : une consommation d’énergie de 0,24-0,34 Wh (équivalente à deux minutes d’une ampoule LED) et une consommation d’eau de 0,28-0,32 mL (moins qu’une cuillère à café).

Comparer notre méthodologie à ces résultats est difficile en raison du manque de détails. Par exemple, nous manquons d’informations sur le nombre de tokens générés par requête, le modèle spécifique utilisé, ou si nous avons un mélange de modèles et de cas d’usage. Pour faciliter la comparaison, nous avons supposé qu’une requête moyenne génère 250 tokens, en alignement avec les longueurs de réponse typiques sur LLM Arena [[Chiang et al., 2024](https://arxiv.org/abs/2403.04132)]. Nous avons également considéré que le modèle de l’étude correspondait à GPT-4o pour OpenAI et Gemini 2.0 Flash pour Google. Nous ignorons également les modalités non textuelles comme la génération d’images, qui ne sont pas non plus incluses dans l’étude de Google.

![plot.png](/images/content/post_3/plot.png)

Notre méthodologie mise à jour produit des estimations du même ordre de grandeur pour la consommation d’énergie, les émissions de GES et la consommation d’eau. Nos résultats sont légèrement plus élevés, ce qui pourrait provenir de deux facteurs : une surestimation de la taille et de l’architecture du modèle, ou le benchmark [ML.ENERGY](http://ml.energy/) ne capturant pas pleinement les optimisations que les fournisseurs mettent en œuvre en production.

Nous estimons les tailles et architectures des modèles à partir de données fuitées, de résultats de benchmarks et d’informations tarifaires, comme détaillé sur notre [page des modèles propriétaires](https://ecologits.ai/latest/methodology/proprietary_models/). Nous supposons que GPT-4o et Gemini 2.0 Flash sont équivalents à 440 milliards de paramètres avec une architecture Mixture of Experts et 10-30% de paramètres actifs. Les tailles réelles pourraient être plus petites.

Bien que le benchmark [ML.ENERGY](http://ml.energy/) reproduit les déploiements de production, les fournisseurs peuvent employer des optimisations supplémentaires qui réduisent davantage la consommation d’énergie. Sans une plus grande transparence, déterminer si notre méthodologie surestime les impacts reste une question ouverte.

Cependant, la publication par Google de résultats intermédiaires nous a permis de recalculer certains impacts en utilisant une méthodologie inspirée des études d’analyse du cycle de vie. En utilisant l’intensité carbone basée sur la localisation et en incluant la consommation d’eau liée à la prodution d'électricité, nous avons réduit l’écart entre nos estimations et les données publiées.

### Comparaison avec Mistral AI

En juillet 2025, Mistral AI a publié une étude d’Analyse du Cycle de Vie (ACV) réalisée par Carbone 4 et l’ADEME sur leur modèle Mistral Large 2. L’étude a évalué les impacts environnementaux d’une requête générant une page de texte (400 tokens de sortie), couvrant la conception du modèle, l’entraînement, la fabrication du matériel, des centres de données, l’inférence, le réseau et les équipements utilisateur. Elle s’est concentrée sur trois critères également soutenus par EcoLogits, rapportant des impacts marginaux de 1,14 gCO2e (GWP), 45 mL (WCF) et 0,16 mgSbeq (ADPe).

Bien qu’une ACV évaluée par des pairs soit la référence pour évaluer les impacts environnementaux, le manque de transparence dans l’étude de Mistral AI, tel que l’absence d’hypothèses, de détails sur le périmètre et de résultats intermédiaires, **rend toute comparaison directe impossible**. Plus précisément, nous manquons de données sur la part des impacts attribuables uniquement à la phase d’inférence de l’IA.

|  | Mistral AI | EcoLogits (Mistral Large 2 sur 400 tokens de sortie) |
| --- | --- | --- |
| Consommation d’énergie | NA | 0,23 Wh |
| Émissions de GES | 1,14 gCO2e | 0,013 gCO2e |
| Consommation d’eau | 45 mL | 1,63 mL |
| Consommation de matériaux | 0,16 mgSbeq | <0,001 mgSbeq |

## Appel à contributions

Cette nouvelle version représente une avancée majeure dans la modélisation des impacts environnementaux de l’IA générative lors de l’inférence. Nous avons considérablement amélioré les estimations de consommation d’énergie, en nous alignant étroitement sur les déclarations des fournisseurs, et avons introduit la consommation d’eau comme nouveau critère d’impact. Notre mission chez **GenAI Impact** est de maintenir et de mettre à jour nos outils en fonction des dernières recherches. Nous invitons les laboratoires de recherche et les organisations à contribuer ouvertement à l’avancement du domaine de l’IA durable.

Nous avons encore des améliorations futures à réaliser, comme la prise en compte des tokens d’entrée, qui peuvent affecter significativement la consommation d’énergie, en particulier dans des cas d’usage comme le RAG ou la programmation. Par exemple, [Epoch AI](https://epoch.ai/gradient-updates/how-much-energy-does-chatgpt-use) rapporte qu’augmenter les tokens d’entrée à 10k ou 100k peut multiplier la consommation d’énergie par 8 ou 133, respectivement, par rapport à une requête basique. Pour intégrer cela, nous avons besoin de benchmarks axés sur la consommation d’énergie de la phase de *prefill* à des longueurs de sortie fixes dans des environnements similaires à la production.

Nous accueillons avec enthousiasme les contributions des instituts de recherche et des organisations pour explorer davantage la modélisation des impacts de l’IA générative. Si vous êtes intéressé(e) par une collaboration pour améliorer la méthodologie, n’hésitez pas à [nous contacter](https://genai-impact.org/contact/).
