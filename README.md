# Active Learning

In this work we review and implement the probcover agorithm introduced in the paper  ["Active Learning Through a Covering Lens"](https://https://arxiv.org/pdf/2205.11320)  by Ofer Yehuda et al, on three image datasets (STL10, CIFAR10, CIFAR100). We then introduce a new method we call "Varslc" and compare the two.
## ProbCover
The purpose of active learning is to find a way of choosing the 'best' data points for the machine to learn from, i.e. choosing the most efficient learning set. We show that probcover reaches higher accuracy than random sampling, focusing on low budget (about 10 - 500 labled datapoints).

 The **ProbCover algorithm** requires $L$ - labled data (may even be an empty set), $U$ - un-labled data, $\delta$ - ball size, $b$ - budget. First the algorithm requiers a represenation (latent space) of the data from an unsupervised algorithm (here we use Simclr as the unsupervised learning algorithm), than acting in this latent space, we construct an adjacency graph $G = (V, E)$, with $V= L\cup U$, and  $(x, x') \in E$ exists if $x'\in B_\delta(x)$, where distance is calculated using the cosine distance. Initially, for every $c \in L$, the algorithm iterates and removes all incoming edges from the graph that correspond to vertices already covered by these labeled samples $\{(x,x'):x\in B_\delta(c)\}$.

After updating the graph to exclude already covered points, the algorithm selects the vertex with the highest out-degree, $m$, representing the sample that covers the largest number of remaining uncovered points. Once a node is selected, the graph is further updated by removing all incoming edges associated with the selected node and its neighbors within the radius $\delta$, marking these points as covered $\{(x,x'):x\in B_\delta(m)\}$.
this process repeats iteratively for $b$ iterations, ensuring that each selected sample contributes maximally to the overall coverage. This careful sequence---removing edges, selecting a node, and updating coverage---ensures that the algorithm avoids redundancy and efficiently maximizes the coverage of the data distribution.

After getting the "active learning set" we train a small neuaral net on it, and calculate the acuuracy.


* We used online availble [implementation of Simclr](https://colab.research.google.com/github/phlippe/uvadlc_notebooks/blob/master/docs/tutorial_notebooks/tutorial17/SimCLR.ipynb#scrollTo=-mT1josKM6NC).


# VarSLC

The **motivation** for this algorithm can be summarized in two points: 

1. A simple 1-layer neural network first learns directions in the data corresponding to principal components.  These directions are ranked by their eigenvalues (EVs)—higher eigenvalues mean more variance in that direction, so the network learns those first (higher variance means larger gradients).

2. SimCLR, projects images into a latent space where similar images are closer together. The output distribution of this latent space often resembles a mixture of Gaussians. This is expected because SimCLR tends to cluster similar instances together while pushing apart dissimilar ones.

From the first two points we can strategize: Feeding high-variance clusters (gaussians) to the model first.  High-variance directions in the data align with the leading principal components, which neural networks learn faster. 

**Implemantaion:**
we divide the data into $I$ clusters, with information about the variance ($\text{Var}_i$) and number of examples in each cluster $n_i$. we then define $p_i = \frac{n_i}{N}$, where $n_i$ is the number of datapoints in cluster $i$ and $\lambda_i = \frac{\text{Var}_i}{\sum_j\text{Var}_j}$, the relative variance of cluster $i$. Assuming the budget is b, we iterate over the budget size and update the labled data point by randomly selecting a new labled point from cluster $i$ with probability $\frac{(p_i-\frac{l_i}{N})\cdot \lambda_i}{\sum_j(p_j-\frac{l_j}{N})\cdot \lambda_j}$ where $l_i$ is the number of already labled points in the cluster (either from the iteration or it was preiviously given).


# VarSLC  VS ProbCover Results



![WhatsApp Image 2025-03-06 at 7 27 20 PM](https://github.com/user-attachments/assets/7fa141b1-4917-487b-8454-c1bddce12078)
![WhatsApp Image 2025-03-06 at 7 27 21 PM](https://github.com/user-attachments/assets/e26f79b9-7b14-4fd5-b9a4-1b8239ae735d)
Histograms of label distribution over three satasets in both ProbCover and Varslc algorithms, the active set is of size is 100 for STL10 and CIFAR10, and 1000 for CIFAR100. We can see that the label distribution in the probCover algorithm is very imbalanced with noticeable gaps, while the VarSelection algorithm provides a more balanced distribution.



![WhatsApp Image 2025-03-06 at 6 57 49 PM](https://github.com/user-attachments/assets/96971d09-85fb-49d7-9800-b8e333dc7692)
![WhatsApp Image 2025-03-06 at 6 57 33 PM](https://github.com/user-attachments/assets/8d680871-762f-44f6-89b8-9371743226b9)
![WhatsApp Image 2025-03-06 at 7 50 46 PM](https://github.com/user-attachments/assets/02aa4f80-2dcc-4174-aa4f-a08ea1c57d30)

In these plots, the x-axis is the budget size and the y-axis is the accuracy. We compare the performence of the three models (random probcover and varslc). For smaller budget sizes, the ProbCover algorithm typically performs best. However, as the budget increases, the Varslc algorithm improves and eventually outperforms both ProbCover. In all datasets Varslc works better than random selection.
