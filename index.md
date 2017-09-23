## A Semi-Supervised Network Embedding Model for Protein Complexes
Detection
Sep 23, 2017

Protein complex is a group of associated polypeptide chains which plays essential roles in biological process. Given a graph representing protein-protein interactions (PPI) network, it is
critical but non-trivial to detect protein complexes, the subsets of proteins that are closely coupled, from it. Network embedding is a method to learn low-dimensional 
representations of vertices in networks. It has been proved quite useful for community detection
in social networks in recent years. However, unlike social networks, PPI network does not contain rich metadata, so that existing network embedding methods
cannot fully capture the network structure to hugely improve the effect of protein complexes detection. In this paper, we propose a semi-supervised
network embedding model by adopting graph convolutional networks to effectively detect densely connected subgraphs. We
compare the performance of our model with state-of-the-art approaches on three popular PPI networks with various data sizes and 
densities. The experimental results show that our approach significantly outperforms other approaches on all three PPI networks.

#### Introduction
Protein complex is a complex graph structure that is linked by protein-protein interactions (PPI) , which plays an essential role in biological process and drug discovery in pharmaceutical process. Therefore, correctly identifying protein complexes in PPI network is useful in the field of biomedical sciences. However, with the huge increase of PPI data, only a small amount of protein complexes are identified in vitro because of the bottleneck of experimental approaches that require a large amount of labor resource .

Protein complex is a complex graph structure that is linked by protein-protein interactions (PPI) , which plays an essential role in biological process and drug discovery in pharmaceutical process. Therefore, correctly identifying protein complexes in PPI network is useful in the field of biomedical sciences. However, with the huge increase of PPI data, only a small amount of protein complexes are identified in vitro because of the bottleneck of experimental approaches that require a large amount of labor resource .

To overcome the technological limit of experimental approaches for protein complexes detection, computational approaches are used. The PPI network can be represented as an undirected and unweighted graph where proteins are represented as vertices and their interactions as edges. Each protein complex consists of two or more proteins that are shown as densely connected subgraphs, which indicates graph based clustering methods should be utilized to discover them.

For example, used clique finding algorithms to predict protein complexes from PPI network. They devised their own methods to merge overlapping cliques as protein complexes. Besides, introduced Markov Clustering (MCL) as graph partitioning method by simulating random walks, which used two operators called expansion and inflation to boost strong connections and demotes weak connections. Later, showed the robustness of MCL with comparison to three other clustering algorithms for protein complex detection. One of the recent emerging methods is to first identify cores of a protein complex, and then add attachments into these cores to form protein complexes . further evaluated the implementation of this method called COACH against other methods, and proved that COACH outperforms others on two PPI datasets. Though later some methods proposed by other researchers achieved better clustering performance but their methods require additional information to perform with many limitations.

Recently, network embedding has been widely studied and proved that it can further improve the performance of many graph clustering methods , which is also the main focus of this paper. Network embedding learns low-dimensional representations of vertexes in networks to capture and preserve the network structure. However, most of existing network embedding methods heavily relies on the attributes of each vertex in the network, which is not suitable for PPI network. As shown in Figure , there is no any metadata associated with each node except protein name. In other words, existing network embedding methods cannot fully capture PPI network structure because no sufficient information can be used to compute the first-order and the second-order proximity .

To overcome this issue, we propose a semi-supervised network embedding model by adopting graph convolutional networks (GCN) that can capture both local and global structure. Our contributions are two-fold. Firstly, we introduce a method to effectively exploit the first-order proximity even there is no any information associated with each vertex in PPI network. Secondly, we design a GCN based auto-encoder to preserve the second-order proximity. To prove that our model is robust and feasible, we evaluated our model on three PPI datasets with well known benchmark complexes.

![](fig1.png)



![](cnntsne.jpg)

Proposed Model
==============

PPI data come in the form of connections between proteins, which is easily described as a graph model. Proteins are represented as vertices and their interactions are represented as edges in the graph. Assume we have a graph *G* = (*V*, *E*), where *V* represents a set of vertices in the graph, *V* = *v*<sub>1</sub>, ..., *v*<sub>*n*</sub>. *E* represent a set of edges in the graph, *E* = *e*<sub>1</sub>, ..., *e*<sub>*n*</sub>. Each edge is associated with two vertices. For PPI network, there is no weight for edges.

As we mentioned earlier, our goal is to have a network embedding model that can effectively capture the local and global structure of PPI network, so the the clustering performance can be further improved. Therefore, we first define the first-order proximity, which can specifically characterize the local structure of PPI network as shown in Definition 1.

*H*<sup>(*l* + 1)</sup> = *f*(*H*<sup>(*l*)</sup>, *A*),