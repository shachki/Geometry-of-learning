# VarSLC VS ProbCover

![WhatsApp Image 2025-03-06 at 7 27 20 PM](https://github.com/user-attachments/assets/7fa141b1-4917-487b-8454-c1bddce12078)
![WhatsApp Image 2025-03-06 at 7 27 21 PM](https://github.com/user-attachments/assets/e26f79b9-7b14-4fd5-b9a4-1b8239ae735d)
Histograms of label distribution over three satasets in both ProbCover and Varslc algorithms, the active set is of size is 100 for STL10 and CIFAR10, and 1000 for CIFAR100. We can see that the label distribution in the probCover algorithm is very imbalanced with noticeable gaps, while the VarSelection algorithm provides a more balanced distribution.



![WhatsApp Image 2025-03-06 at 6 57 49 PM](https://github.com/user-attachments/assets/96971d09-85fb-49d7-9800-b8e333dc7692)
![WhatsApp Image 2025-03-06 at 6 57 33 PM](https://github.com/user-attachments/assets/8d680871-762f-44f6-89b8-9371743226b9)
![WhatsApp Image 2025-03-06 at 7 50 46 PM](https://github.com/user-attachments/assets/02aa4f80-2dcc-4174-aa4f-a08ea1c57d30)

In these plots, the x-axis is the budget size and the y-axis is the accuracy. We compare the performence of the three models (random probcover and varslc). For smaller budget sizes, the ProbCover algorithm typically performs best. However, as the budget increases, the Varslc algorithm improves and eventually outperforms both ProbCover. In all datasets Varslc works better than random selection.
