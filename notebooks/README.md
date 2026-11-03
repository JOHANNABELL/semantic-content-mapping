# Notebooks

Deux notebooks, commentés en français. Chaque section commence par **Ce que ça fait** et **Pourquoi**, et tous les réglages sont dans la section 1.

| Notebook | Rôle | Entrée | Sorties (`outputs/`) | Durée indicative |
|---|---|---|---|---|
| `mars_carte_urls.ipynb` | **POC autonome** : d'une liste d'URLs à une carte sémantique interactive | Excel d'URLs (une feuille par marque), CSV ou TXT ; ou CSV de textes `url, titre, texte` | `carte_urls/carte_urls.html`, `articles.csv`, `themes.csv`, `paires_proches.csv`, `pages_en_echec.csv` | Environ 30 min de téléchargement pour 700 URLs ; quelques minutes de calcul sur GPU |
| `mars_pipeline_v2.ipynb` | **Analyse complète** : thèmes, familles, axes métier, sous-thèmes santé, carte, cannibalisation, espaces vides, tableau de bord | Archives des étapes 1 et 2 (corpus nettoyé, vecteurs d'articles et de passages) ; archive d3-turbo facultative | `mars_tableau_de_bord.html`, `carte_interactive.html`, `rapport_cannibalisation.md`, CSV de résultats, `mars_livraison.zip` | Quelques minutes (les vecteurs sont déjà calculés) |

## Lancer

- **Colab** : téléverser le notebook et les fichiers d'entrée dans `/content` (ou `/content/data`), choisir un GPU T4, puis *Exécution → Tout exécuter*.
- **Local** : placer les fichiers dans `data/` à la racine du dépôt et lancer le notebook depuis `notebooks/` : les chemins sont détectés automatiquement.

## Plan de `mars_pipeline_v2.ipynb`

| § | Section |
|---|---|
| 0–1 | Installation, chemins, **paramètres** |
| 2 | Chargement des données (archives des étapes 1 et 2) et contrôles d'alignement |
| 3–5 | Thèmes K-means, noms des thèmes, familles |
| 6–9 | Espèce, axes métier, d3-turbo (facultatif), sous-thèmes santé |
| 10–11 | Carte UMAP, tableau final (un article par ligne) |
| 12–16 | Cannibalisation (paires, seuil, groupes) et espaces vides |
| 17 + annexes | Données et gabarit du tableau de bord, zip de livraison |

## À savoir

- `mars_pipeline_v2.ipynb` lit les résultats **déjà calculés** des étapes 1 et 2. Les notebooks qui les produisent ne sont pas encore dans ce dépôt (voir la *Roadmap* du README) ; leur format est décrit dans [`../data/README.md`](../data/README.md).
- Les sorties des cellules ont été effacées avant publication : aucune donnée Mars n'est incluse.
- Pour adapter les notebooks à un autre corpus : [`../docs/ADAPTER.md`](../docs/ADAPTER.md).
