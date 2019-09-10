---
layout: post
title: Bases de dados de Sensoriamento Remoto Disponíveis para o uso em Machine e Deep Learning
subtitle: Dados disponíveis e como utilizá-los  
tags: [AI, Datasets,]
---

O aprendizado de máquina, em especial o deep learning, é uma ferramenta com grande potêncial para auxiliar na resolução de problemas de sensoriamento remoto relacionados à classificação de imagens, segmentação de instâncias e semântica, detecção de objetos e classificação de cenas. Porém, em geral, os resultados só são bons quando se utilizam milhões de imagens pre classificadas ou segmentadas no treinamento destes modelos. Assim, com intuito de auxiliar e facilitar a vida de quem pretende utilizar o deep learning em sensoriamento remoto, elaborei esta lista que apresenta as principais fontes de imagens de sensoriamento remoto e os principais artigos que utilizaram estes dados.

**Classificação de Cenas**

| Nome | Fonte | Descrição | Resolução Espacial | 
| ------------- | ------------- | --------------------------- | ------------- |
| [UC Merced Land Use Dataset](http://weegee.vision.ucmerced.edu/datasets/landuse.html) |[Yang & Newsam 2010](https://www.researchgate.net/publication/221589425_Bag-of-visual-words_and_spatial_extensions_for_land-use_classification)  | 21 categorias de uso/ocupação do solo com 100 imagens (256x256px) por categoria. | 0.30m | 
| [SAT-4 and SAT-6 airborne datasets](https://csc.lsu.edu/~saikat/deepsat/) | [Basu et al. 2015](https://arxiv.org/abs/1509.03602)  | 6 categorias de uso/ocupação do solo com 400 mil imagens (28x28px) por categoria com 4 bandas (RGBNIR) extraídas de *National Agriculture Imagery Program (NAIP) em 2009*. | 1.0m | 
| [Brazilian Cerrado-Savanna Scenes Dataset](http://www.patreo.dcc.ufmg.br/2017/11/12/brazilian-cerrado-savanna-scenes-dataset/) | [K.Nogueira et al. 2016](https://homepages.dcc.ufmg.br/~keiller.nogueira/pdf/prrs2016.pdf)  | 4 categorias de vegetação presente na região da Serra do Cipó, MG com 1311 imagens (64x64px) por categoria com 3 bandas (RGNIR) obtidas pelo sensor RapidEye. | 5.0m |
| [EuroSat](http://madm.dfki.de/downloads) | [Helber et al. 2017](https://arxiv.org/abs/1709.00029)  | 10 categorias de uso/ocupação do solo com 27 mil imagens (64x64px) com 3 ou 16 bandas do satélite Sentinel 2. | 10m |
| [Brazilian Coffee Scene Dataset](http://www.patreo.dcc.ufmg.br/2017/11/12/brazilian-coffee-scenes-dataset/) | [O. A. B. Penatti et al. 2017](https://www.cv-foundation.org/openaccess/content_cvpr_workshops_2015/W13/papers/Penatti_Do_Deep_Features_2015_CVPR_paper.pdf)  | 2876 imagens (64x64px) de platações de café no estado de Minas Gerais obtidas pelo sensor SPOT em 2005. | 10m |
