# Méthodologie

Chaque choix technique est justifié par ce qu'il apporte à l'analyse.

## 1. Collecte et nettoyage

- **Téléchargement en cascade** (`requests`, puis d'autres méthodes, en plusieurs passes avec pause, cache) : les sites ne répondent pas tous du premier coup.
- **Extraction du texte principal** avec trafilatura (BeautifulSoup en secours) : on ne garde que l'article, sans menus ni publicités.
- **Suppression du texte répété** : une ligne présente sur au moins 3 pages d'une même marque (5 pour une puce de liste) est un bloc commun (menu, promotion, appel à l'action). Sans ce nettoyage, tous les articles d'une marque se ressembleraient artificiellement.
- **Contrôles** : langue, type de page, doublons (empreinte du texte ou URL canonique), articles trop courts.

## 2. Vectorisation

- **Modèle `BAAI/bge-m3`** : multilingue, 1 024 dimensions, jusqu'à 8 192 tokens. Il lit l'article entier sans le couper.
- **Texte encodé** : titre + description + texte.
- **Passages d'environ 200 mots**, vectorisés eux aussi : ils servent à mesurer si deux articles partagent les mêmes contenus, pas seulement le même sujet.
- **Vecteurs normalisés** : le K-means euclidien équivaut alors à un regroupement par similarité cosinus, sans mise à l'échelle supplémentaire.

## 3. Classification et carte

- **Thèmes : K-means** (20 initialisations, graine fixe). Le nombre de thèmes a été choisi en comparant WCSS, silhouette, stabilité (ARI entre graines) et taille du plus petit thème. Aucun k n'atteignant la stabilité stricte (ARI ≥ 0,80), k = 15 a été fixé comme compromis lisible.
- **Noms des thèmes par le sens** : expressions candidates par c-TF-IDF, puis choix par similarité bge-m3 avec le centre du thème, pénalité si l'expression ressemble à un thème voisin, diversité des deux mots (MMR).
- **Familles métier** : chaque thème est rattaché à la famille dont la description est la plus proche de son centre. Familles définies par l'équipe à partir des informations fournies par Mars.
- **Axes métier** (espèce, type d'aliment, stade de vie, besoin santé) : comptage de mots pour l'espèce ; pour les autres étiquettes, mots-clés + sens bge-m3, comparés à un modèle de décision (d3-turbo) sur 80 articles annotés. La meilleure méthode est retenue étiquette par étiquette (critère F1).
- **Carte 2D : UMAP** (distance cosinus), fidélité mesurée par la *trustworthiness*. Les distances sont déformées : une zone vide n'est pas une lacune.

## 4. Cannibalisation et lacunes

- **Paires d'articles de marques différentes** au-dessus d'un seuil de similarité. Le seuil se calibre sur des paires annotées à la main (F1 maximal) ; un seuil fixe comme 0,50 garderait 85 % des paires, car tous les articles d'un même secteur se ressemblent un peu.
- **Groupes à lien complet** : dans un groupe, tous les articles sont proches deux à deux (pas de chaînes A–B–C).
- **Recouvrement des paragraphes** : preuve complémentaire de contenu dupliqué.
- **Lacunes** : grille espèce × aliment × stade, par marque. Règle de périmètre : un zéro n'est une lacune que si la marque publie déjà sur cette espèce ou ce type d'aliment.

## 5. Restitution

Une page HTML unique et autonome (graphiques en SVG, sans dépendance), en français et en anglais : vue d'ensemble, carte, cannibalisation, espaces vides, curseur de seuil qui recalcule tout en direct.
