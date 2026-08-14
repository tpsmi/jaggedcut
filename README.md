# A Jagged Cut

## Reading Visual Archives at Multiple Resolutions with HDBSCAN

---

## Overview

Clustering multimodal embeddings is a standard way of finding patterns in
digitised visual collections, but results are almost always reported as a flat
list of groups. HDBSCAN builds a hierarchy internally and then reports a jagged
cut across it; this repository contains the code for reading the whole tree
instead.

The analysis recovers the structure above and below the selected clusters from
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
└── notebooks/
    ├── 01_DownloadImages.ipynb      # metadata and images from Europeana
    └── 02_JaggedCut.ipynb           # pipeline, measures, and all figures
```

---

## Data

Two inputs, neither of them in this repository.

| Input | Where to get it |
| --- | --- |
| `massafont_clip_features_vitl14P_l2b.npy` | Zenodo: <https://doi.org/10.5281/zenodo.20847435> |
| The 9,021 JPEG images and their metadata | Europeana, via notebook 01 |

The images are published under CC BY-NC-ND 4.0. Notebook 01 downloads them and
their metadata records through the Europeana API; a free API key is required
(<https://apis.europeana.eu/api/key-manager>) and the download takes two to three
hours. Everything else the analysis needs is derived from the embeddings.

Place the files as follows, or edit the paths at the top of notebook 02:

```
data/
├── images/                                      # 9,021 JPEGs
├── massafont_clip_features_vitl14P_l2b.npy
└── massafont_metadata.csv
```

---

## Running the analysis

```
pip install -r requirements.txt
jupyter lab notebooks/
```

Run notebook 01 once to fetch the images and metadata, then notebook 02. If you
want only the measures and the dendrogram rather than the figures containing
photographs, the embeddings alone are enough and notebook 01 can be skipped.

Cells in notebook 02 run top to bottom and follow the order of the article:
embed, reduce, cluster, recover the hierarchy, compute the measures, then draw
the figures. The parameter sweep is off by default (`RUN_SWEEP = False`) since
the chosen configuration is hard-coded; set it to `True` to reproduce the DBCV
grid.

**Runtime:** about 10 minutes for UMAP and clustering on a laptop; the sweep
takes considerably longer, and the image download two to three hours.

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

The file is self-contained — all thumbnails are embedded, so it needs no network
connection and no server. Download it from the Zenodo record above and open it in
a browser. It is about 32 MB, which is why it is deposited there rather than kept
in this repository.

Notebook 02 regenerates it; reduce `N_THUMBS` and `THUMB_SIZE` in the explorer
cells for a smaller file.

---

## Companion repository

The same collection is analysed at a different resolution in a companion
repository cited in the article, which holds the embedding extraction and a
10-dimensional reduction; this one starts from the embeddings and reads the
hierarchy.

---

## A note on AI-assisted development

A significant portion of the code in these notebooks was developed with the
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
