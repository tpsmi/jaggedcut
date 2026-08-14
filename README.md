# A Jagged Cut

## Reading Visual Archives at Multiple Resolutions with HDBSCAN

---

## Overview

Clustering multimodal embeddings is a standard way of finding patterns in
digitised visual collections, but results are almost always reported as a flat
list of groups. HDBSCAN builds a hierarchy internally and then reports a jagged
cut across it; this repository contains the code for reading the whole tree
instead.

The notebook recovers the structure above and below the selected clusters from
HDBSCAN's condensed tree, draws it as a legible dendrogram, and computes three
measures:

| Measure | What it records |
| --- | --- |
| **Specificity** | The density at which a cluster becomes distinguishable from what surrounds it |
| **Load-bearing splits** | The nodes at which the collection divides into substantial parts |
| **Attachment** | Where each unclustered photograph left the tree, and so how close it came to belonging |

Applied to the 9,021 digitised photographs of the Catalan commercial street
photographer Martí Massafont (1918–2012), held by the Centre for Image Research
and Dissemination (CRDI) of Girona City Council and made available through
Europeana.

---

## Repository structure

```
jagged-cut/
├── README.md
├── requirements.txt
├── LICENSE
├── hdbscan_tree_explorer.html       # interactive dendrogram (32 MB)
└── notebooks/
    └── JaggedCut_Massafont.ipynb    # pipeline, measures, and all figures
```

---

## Data

Three inputs are needed, none of them in this repository.

| File | Where to get it |
| --- | --- |
| `massafont_clip_features_vitl14P_l2b.npy` | Zenodo: <https://doi.org/10.5281/zenodo.20847435> |
| `massafont_metadata_matched.csv` | Zenodo, as above |
| The 9,021 JPEG images | Europeana — see below |

The images are published under CC BY-NC-ND 4.0 and can be downloaded with
the Europeana API. A free API key is required; the download and embedding
extraction are documented in the companion repository cited in the article.

Place the downloaded files as follows, or edit the paths at the top of the
notebook:

```
data/
├── images/                                      # 9,021 JPEGs
├── massafont_clip_features_vitl14P_l2b.npy
└── massafont_metadata_matched.csv
```

---

## Running the notebook

```
pip install -r requirements.txt
jupyter lab notebooks/JaggedCut_Massafont.ipynb
```

Cells run top to bottom and follow the order of the article: embed, reduce,
cluster, recover the hierarchy, compute the measures, then draw the figures.
The parameter sweep is off by default (`RUN_SWEEP = False`) since the chosen
configuration is hard-coded; set it to `True` to reproduce the DBCV grid.

**Runtime:** about 10 minutes for UMAP and clustering on a laptop; the sweep
takes considerably longer.

**Configuration:** OpenCLIP ViT-L/14 (LAION-2B) embeddings reduced to 25
dimensions with a neighbourhood size of 30, `min_cluster_size = min_samples =
20`. Produces 72 clusters and classifies 27% of the photographs as noise.

---

## Interactive explorer

`hdbscan_tree_explorer.html` is the dendrogram as a browsable figure. Every
element can be clicked to see a sample of the photographs beneath it: the
selected clusters, the merge points above the cut, the groupings HDBSCAN found
inside a cluster but did not report, and the junctions where photographs left the
tree without ever reaching a cluster. Each shows the number of images it
represents and the density at which it forms.

The file is self-contained — all thumbnails are embedded, so it needs no
network connection and no server. Download it and open it in a browser. It is
about 32 MB, so GitHub will show a plain download button rather than a preview.

The notebook regenerates it; reduce `N_THUMBS` and `THUMB_SIZE` in the explorer
cells for a smaller file.

---

## Companion repository

The same collection is analysed at a different resolution in a companion
repository cited in the article, which holds the Europeana download, the
embedding extraction, and a 10-dimensional reduction; this one starts from the
embeddings and reads the hierarchy.

---

## A note on AI-assisted development

A significant portion of the code in this notebook was developed with the
assistance of Claude. Claude was used as a collaborative coding partner
throughout the project: suggesting implementations, debugging errors, refining
visualisations, and helping translate analytical intentions into working code.
All code was reviewed, tested, and adapted by the author.

---

## Licence

Code in this repository is released under the MIT licence. The photographs and
metadata from the Fons Martí Massafont Costals collection are available under
CC BY-NC-ND 4.0 via Europeana and the CRDI Girona. The derived data files on
Zenodo are released under CC BY 4.0.

*Author and citation details are omitted from this version for anonymous
review.*

---
