# Adapter le code à votre propre projet

Les deux notebooks ont été écrits pour le cas Mars (5 marques petcare, articles en anglais). Tous les réglages sont regroupés dans la **section 1. Paramètres** de chaque notebook, et chaque ligne y est commentée (rôle, effet d'un changement).

Ce guide liste **ce qu'il faut modifier, dans quel ordre, et où**.

> Repère : *§ n* = section n du notebook. Les noms entre `backticks` sont les variables Python.

---

## 1. `mars_carte_urls.ipynb` : d'une liste d'URLs à une carte

### Obligatoire

| Quoi | Où | Variable | À mettre |
|---|---|---|---|
| Votre liste d'URLs | § 1 | `FICHIER_URLS` | Nom de votre fichier : `.xlsx` (une feuille par marque ou par site, URLs en colonne A), `.csv` (colonne `url`) ou `.txt` (une URL par ligne) |
| … ou une liste collée | § 1 | `LISTE_URLS` | `["https://…", "https://…"]` (prioritaire sur le fichier) |
| Textes déjà récupérés (facultatif) | § 1 | `FICHIER_TEXTES` | CSV avec les colonnes `url, titre, texte` : ces URLs ne sont pas téléchargées. Mettre `None` si vous n'en avez pas |
| Dossiers | § 0 | `DATA_DIR`, `OUT_DIR`, `DOSSIERS_ENTREE` | Détection automatique Colab / local. En local : fichiers dans `data/`, résultats dans `outputs/` |

### Selon votre corpus

| Quoi | Variable (§ 1) | Conseil |
|---|---|---|
| Langues gardées | `LANGUES` | `["en"]`, `["fr"]`, ou `None` pour tout garder |
| Test rapide | `N_MAX_URLS`, `N_MAX_PAR_MARQUE` | Ex. `20` pour vérifier que tout marche avant le vrai calcul |
| Politesse envers les sites | `DELAI`, `PASSES`, `PAUSE_PASSES` | Ne pas descendre sous 0,5 s entre deux requêtes |
| Navigateur de secours | `UTILISER_NAVIGATEUR` | `False` si vous n'en avez pas besoin (installation lourde) |
| Vitesse de vectorisation | `MAX_TOKENS` | 8192 = article entier (GPU conseillé) ; 512 = beaucoup plus rapide sur CPU |
| Nombre de thèmes | `K` | `None` = automatique (racine de n/2, entre 3 et 20) ; sinon un entier |
| Mots à ignorer pour nommer les thèmes | `MOTS_VIDES_SUPPL` | Vos noms de marques, le vocabulaire du site |
| Mots trop généraux | `MOTS_GENERAUX` (§ 6) | Les mots omniprésents de votre secteur (ex. « banque », « client ») |

---

## 2. `mars_pipeline_v2.ipynb` : analyse complète et tableau de bord

### Obligatoire : les données d'entrée

| Quoi | Où | Variable |
|---|---|---|
| Dossiers où chercher les archives | § 0 | `DATA_DIR`, `OUT_DIR`, `DOSSIERS_ZIP` |
| Noms des archives | § 2 et § 8 | `ouvrir_zip("mars_step2_embeddings*.zip")`, `ouvrir_zip("mars_step1_data*.zip")`, `ouvrir_zip("mars_step3_d3_eval*.zip")` |

Le format attendu de chaque archive est décrit dans [`data/README.md`](../data/README.md).

### Obligatoire dès qu'on change de secteur

| Quoi | Où | Variable | Comment l'adapter |
|---|---|---|---|
| **Familles métier** | § 1 | `FAMILLES` | Une entrée par famille : `"Nom affiché": ("nom encodé", "description en une phrase")`. La description est lue par le modèle : listez-y les sous-sujets typiques. 5 à 10 familles conviennent |
| **Axes métier** | § 1 | `AXE_ALIMENT`, `AXE_STADE`, `AXE_SANTE`, puis `AXES_MARS` | Pour chaque axe : une étiquette par défaut (`"defaut"`) et, pour chaque étiquette, une liste de mots-clés et une description. Ex. pour une banque : produit (crédit, épargne…), cible (jeune, famille…), moment de vie |
| **Espèce** (propre au petcare) | § 1 et § 6 | `MOTS_CHIEN`, `MOTS_CHAT`, `SEUIL_ESPECE` | Hors petcare : remplacer par votre propre dimension, ou neutraliser l'étape § 6 |
| **Valeurs de la grille des lacunes** | § 16 | `FOODS`, `STAGES` | Elles sont **écrites en dur** : les remplacer par les étiquettes de vos axes |
| **Noms de marques à ignorer** | § 1 | `MOTS_VIDES` | Remplacer `iams, eukanuba, pedigree, greenies, cesar, mars` par vos marques |
| **Mots trop généraux** | § 1 | `MOTS_GENERAUX` | Les mots omniprésents de votre secteur |
| **Couleurs fixes de la carte** | Annexe | `COULEURS_FIXES` | Une couleur par valeur de vos axes |
| **Titre et noms de fichiers** | Annexes | `"Carte sémantique Mars"`, `mars_tableau_de_bord.html`, `mars_livraison.zip`, `LISEZ_MOI` | Remplacer « Mars » par votre projet |

### Réglages d'analyse

| Quoi | Variable (§ 1) | Effet |
|---|---|---|
| Nombre de thèmes | `K` | Fixé à 15 pour Mars. Plus grand = thèmes plus fins. Tester plusieurs valeurs (silhouette, stabilité) |
| Articles d'autres langues | `AUTRES_LANGUES` | `True` : les articles non anglais de l'étape 1 sont ajoutés |
| d3-turbo | `UTILISER_D3`, `MEILLEURE_SOURCE`, `NOM_D3` | `False` si vous n'avez pas d'étiquettes d3 : seule la méthode mots-clés + bge-m3 est utilisée |
| Règles d'étiquetage | `SEUIL_MOTS_FORT`, `SEUIL_MOTS_MIN`, `Z_CONFIRME`, `Z_SEUL` | Plus bas = plus d'étiquettes données |
| **Seuil de cannibalisation** | `SEUIL` | À recalibrer sur votre corpus : annotez 50 à 100 paires à la main et prenez le seuil qui maximise le F1 |
| Curseur du tableau de bord | `MIN_CURSEUR`, `MAX_CURSEUR` | Doit respecter `MIN_CANDIDAT ≤ MIN_CURSEUR ≤ SEUIL ≤ MAX_CURSEUR` |
| Règles des lacunes | `MIN_PERIMETRE`, `MIN_AUTRES_LACUNE` | Une marque « travaille » un sujet à partir de `MIN_PERIMETRE` articles |
| Carte UMAP | `UMAP_VOISINS`, `UMAP_MIN_DIST`, `GRAINE` | Garder `GRAINE` fixe pour des résultats reproductibles |

---

## 3. Ordre conseillé

1. Lancer `mars_carte_urls.ipynb` avec `N_MAX_URLS = 20` pour vérifier la collecte.
2. Adapter `MOTS_VIDES_SUPPL` et `MOTS_GENERAUX`, relancer sur tout le corpus.
3. Dans `mars_pipeline_v2.ipynb`, adapter `FAMILLES`, les axes, `FOODS` / `STAGES`, puis les titres.
4. Recalibrer `SEUIL` sur quelques paires annotées.
5. Lire les noms de thèmes et les lacunes avec un expert métier avant toute décision.

## 4. Pièges connus

- **Ne jamais trier ou filtrer les vecteurs sans leur index** : la ligne *i* de `embeddings.npy` correspond à la ligne *i* de `embeddings_index.csv`.
- Changer de modèle (`MODELE`) oblige à **tout revectoriser** : des vecteurs de deux modèles différents ne sont pas comparables.
- Une zone vide sur la carte 2D n'est pas une lacune : les lacunes viennent de la grille.
- Respectez les conditions d'utilisation des sites collectés.
