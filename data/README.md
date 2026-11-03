# Données

**Les données du projet Mars ne sont pas publiées** (URLs, textes, vecteurs, résultats détaillés) : elles ont été fournies dans le cadre de l'École d'automne de l'IA d'Alsace Tech, sans autorisation de diffusion.

Placez vos propres fichiers dans ce dossier (en local) ou dans `/content` (sur Colab). Ce dossier est ignoré par Git, sauf ce README.

## Entrée de `mars_carte_urls.ipynb`

Au choix :

| Format | Contenu |
|---|---|
| `.xlsx` | Une feuille par marque ou par site (le nom de la feuille devient la marque), URLs en colonne A |
| `.csv` | Une colonne `url` ; ou bien `url, titre, texte` (+ `description`, `langue` facultatives) pour fournir les textes sans téléchargement |
| `.txt` | Une URL par ligne |

## Entrée de `mars_pipeline_v2.ipynb`

| Archive | Fichiers attendus | Colonnes principales |
|---|---|---|
| `mars_step1_data*.zip` | `all/corpus_clean.csv` | `url, brand, title, slug, description, text_clean, n_words_clean, lang, page_type` |
| `mars_step2_embeddings*.zip` | `all/embeddings.npy` (n × 1024, normalisés), `all/embeddings_index.csv`, `all/chunk_embeddings.npy`, `all/chunks.csv` | index : `row, url, brand, title, slug, n_words_clean` (ligne *i* = vecteur *i*) ; passages : `doc_row` = ligne de l'article |
| `mars_step3_d3_eval*.zip` (facultatif) | `d3_etiquettes.csv` | `url`, `d3_<étiquette>`, `d3_p_<étiquette>`, `d3_stade_choix` |

## Règles

- Ne jamais versionner de données clients ni de clés d'API.
- Respecter les conditions d'utilisation et le `robots.txt` des sites collectés.
