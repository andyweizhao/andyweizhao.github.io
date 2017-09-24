# A Semi-Supervised Network Embedding Model for Protein Complexes Detection

## Abstract
Protein complex is a group of associated polypeptide chains which plays essential roles in biological process. Given a graph representing protein-protein interactions (PPI) network, it is
critical but non-trivial to detect protein complexes, the subsets of proteins that are closely coupled, from it. Network embedding is a method to learn low-dimensional 
representations of vertices in networks. It has been proved quite useful for community detection
in social networks in recent years. However, unlike social networks, PPI network does not contain rich metadata, so that existing network embedding methods
cannot fully capture the network structure to hugely improve the effect of protein complexes detection. In this paper, we propose a semi-supervised
network embedding model by adopting graph convolutional networks to effectively detect densely connected subgraphs. We
compare the performance of our model with state-of-the-art approaches on three popular PPI networks with various data sizes and 
densities. The experimental results show that our approach significantly outperforms other approaches on all three PPI networks.

## Introduction
Protein complex is a complex graph structure that is linked by protein-protein interactions (PPI) , which plays an essential role in biological process and drug discovery in pharmaceutical process. Therefore, correctly identifying protein complexes in PPI network is useful in the field of biomedical sciences. However, with the huge increase of PPI data, only a small amount of protein complexes are identified in vitro because of the bottleneck of experimental approaches that require a large amount of labor resource .

To overcome the technological limit of experimental approaches for protein complexes detection, computational approaches are used. The PPI network can be represented as an undirected and unweighted graph where proteins are represented as vertices and their interactions as edges. Each protein complex consists of two or more proteins that are shown as densely connected subgraphs, which indicates graph based clustering methods should be utilized to discover them.

For example, used clique finding algorithms to predict protein complexes from PPI network. They devised their own methods to merge overlapping cliques as protein complexes. Besides, introduced Markov Clustering (MCL) as graph partitioning method by simulating random walks, which used two operators called expansion and inflation to boost strong connections and demotes weak connections. Later, showed the robustness of MCL with comparison to three other clustering algorithms for protein complex detection. One of the recent emerging methods is to first identify cores of a protein complex, and then add attachments into these cores to form protein complexes . further evaluated the implementation of this method called COACH against other methods, and proved that COACH outperforms others on two PPI datasets. Though later some methods proposed by other researchers achieved better clustering performance but their methods require additional information to perform with many limitations.

Recently, network embedding has been widely studied and proved that it can further improve the performance of many graph clustering methods , which is also the main focus of this paper. Network embedding learns low-dimensional representations of vertexes in networks to capture and preserve the network structure. However, most of existing network embedding methods heavily relies on the attributes of each vertex in the network, which is not suitable for PPI network. As shown in Figure , there is no any metadata associated with each node except protein name. In other words, existing network embedding methods cannot fully capture PPI network structure because no sufficient information can be used to compute the first-order and the second-order proximity .

To overcome this issue, we propose a semi-supervised network embedding model by adopting graph convolutional networks (GCN) that can capture both local and global structure. Our contributions are two-fold. Firstly, we introduce a method to effectively exploit the first-order proximity even there is no any information associated with each vertex in PPI network. Secondly, we design a GCN based auto-encoder to preserve the second-order proximity. To prove that our model is robust and feasible, we evaluated our model on three PPI datasets with well known benchmark complexes.

![](fig1.png)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;Figure 1: Sample of PPI Network

## Proposed Model

PPI data come in the form of connections between proteins, which is easily described as a graph model. Proteins are represented as vertices and their interactions are represented as edges in the graph. Assume we have a graph *G* = (*V*, *E*), where *V* represents a set of vertices in the graph, *V* = *v*<sub>1</sub>, ..., *v*<sub>*n*</sub>. *E* represent a set of edges in the graph, *E* = *e*<sub>1</sub>, ..., *e*<sub>*n*</sub>. Each edge is associated with two vertices. For PPI network, there is no weight for edges.

As we mentioned earlier, our goal is to have a network embedding model that can effectively capture the local and global structure of PPI network, so the the clustering performance can be further improved. Therefore, we first define the first-order proximity, which can specifically characterize the local structure of PPI network as shown in Definition 1.

**Definition 1** *The first-order proximity describes the pairwise similarity between vertices. For any pair of vertices, if there is an edge between *v*<sub>*i*</sub> and *v*<sub>*j*</sub>, there is a positive first-order proximity between *v*<sub>*i*</sub> and *v*<sub>*j*</sub>. Otherwise, the first-order proximity between *v*<sub>*i*</sub> and *v*<sub>*j*</sub> is 0.*

From this definition, we can easily know that the computation of the pairwise similarity
between two vertices is the key to exploit the first-order proximity. Unlike
social networks, we need to design a method to perform the computation because there is
no attributes attached to each vertex in PPI network.

On the other hand, we also need to define the second-order proximity, which can
specifically characterize the global structure of PPI network as shown in
Definition 2.

**Definition 2** *The second-order proximity describes the pairwise similarity between vertices’ neighborhood structure. Let *N*<sub>*i*</sub> and *N*<sub>*j*</sub> denote the set of neighbor vertices of *v*<sub>*i*</sub> and *v*<sub>*j*</sub>, then the second-order proximity is determined by the similarity of *N*<sub>*i*</sub> and *N*<sub>*j*</sub>.*

From this definition, we can infer that the second-order proximity between two vertices is high if two vertices share many common neighbors. The second-order proximity has been demonstrated to be a good metric to define the similarity of a pair of vertices, even if they are not linked by an edge, and thus can highly enrich the relationship of vertices .

On the basis of above discussion, we wrap all up into a semi-supervised network embedding model as shown in Figure in order to preserve both the local and global structure. The component for the first-order proximity is supervised and designed for pairwise similarity calculation between two vertices based on DeepWalk . The component for the second-order proximity is unsupervised and designed for pairwise similarity calculation between two vertices’ neighborhood using a structure reconstruction method based on GCN. We will introduce these two components in the following sections.

![](fig2.png)

&emsp;&emsp;&emsp;&emsp;Figure 2: Proposed Semi-Supervised Network Embedding Model

### Component for the First-Order Proximity

As we described earlier, there is no attributes attached to each vertex in PPI network. Therefore, to compute the pairwise similarity between vertices, we need to create a set of attributes for each vertex. Considering the definition of protein complex, we may set the important neighbor vertices of each vertex as its attributes because these neighbor vertices have higher probability to be grouped together as a protein complex. We propose a method to select vertices from the neighbor vertices of a vertex that have higher tightness to this vertex based on vectors generated by DeepWalk . DeepWalk has been proved successfully in social networks and graph analysis. It learns the latent representations by modeling a stream of short random walk, and then encodes it in a continuous vector space with low dimensions. In our purposed method, we first apply DeepWalk to the graph to get a 64-dimensions vector for each vertex. Let *G* = (*V*, *E*) to be the graph, *v* ∈ *V*, which represents a protein. *H* is the set of neighbor vertices of *v*, *h*<sub>*i*</sub> ∈ *H*, *n* is the number of the neighbor vertices of *v*. We then use Euclidean metric to compute the tightness score *S**c**o**r**e*<sub>*v*, *h*<sub>*i*</sub></sub> between *v* and each *h*<sub>*i*</sub>. Lastly, we keep those neighbor vertices that have tightness score higher than average tightness score of neighbor vertices as attributes of *v*. The neighbor vertices selection process is described in Algorithm 1.

Once we have the attributes for each vertex, we then can use the attributes as supervised information to exploit the first-order proximity and refine the representations in the latent space to constrain the similarity of a pair of vertexes.

![](algorithm1.png)

### Component for the Second-Order proximity

The second-order proximity refers to how similar the neighborhood structure of a pair of vertexes is. Thus, to model the second-order proximity, it is required to model the neighborhood of each vertex. Assume we have a graph *G* = (*V*, *E*), we can obtain its adjacency matrix *M*, which contains *n* instances *m*<sub>1</sub>, ..., *m*<sub>*n*</sub>. For each instance *m*<sub>*i*</sub> = *m*<sub>*i*, *j*</sub><sub>*j* = 1</sub><sup>*n*</sup>, *m*<sub>*i*, *j*</sub> &gt; 0 if and only if there exists a link between *v*<sub>*i*</sub> and *v*<sub>*j*</sub>. The *m*<sub>*i*</sub> describes the neighborhood structure of the vertex *v*<sub>*i*</sub> and *M* provides the information of the neighborhood structure of each vertex. Therefore, we design a GCN based auto-encoder to preserve the second-order proximity of *G*.

GCN can make use of latent variables and is capable of learning interpretable latent representations for undirected, unweighted graphs, which is very suitable for PPI network. As shown in Figure , we use the attributes from each vertex as input channels of the GCN, and then after encoding of *l* convolutional layers, we can get a representation that is learned from the original graph. For the decoding part, we simply use an inner product decoder.

![](fig3.png)

&emsp;&emsp;&emsp;&emsp;Figure 3: Sample of GCN

In our proposed model, we can naturally incorporate vertices' attributes to simultaneously optimize the first-order and second-order proximity referring to the following definition:

**Definition 3** *Given an undirected, unweighted graph *G* = (*V*, *E*) with *N* = |*V*| vertices. We have an adjacency matrix *A* of *G* and an *N* × *D* matrix *X* as input. With a stochastic latent variables *z*<sub>*i*</sub>, we can summarize an *N* × *F* output matrix *Z*. where *F* is the number of output attributes.*

In this definition, *D* is the number of attributes per vertex. As the attributes are generated based on the selected neighbor vertices. In other words, the number of attributes for each vertex is different. Therefore, we set *N* to be the value of *D* initially. When constructing *X*, we set relevant elements to 0 if the vertex does not has these attributes. Every network layer can then be written as a non-linear function:

&emsp;&emsp;&emsp;&emsp;H^<span>(l+1)</span> = f(H^<span>(l)</span>, A),&emsp;&emsp;&emsp;&emsp;(1)

where *H*<sup>(0)</sup> = *X* and *H*<sup>(*L*)</sup> = *Z*, *L* is the number of layers. We then set the following propagation rule:

&emsp;&emsp;&emsp;&emsp;f(H^{(l)}, A) = ReLU(AH^{(l)}W^{(l)}),&emsp;&emsp;&emsp;&emsp;(2)

where *W*<sup>(*l*)</sup> is a weight matrix for the *l*-th network layer and ReLU is the activation function. Note that the multiplication with *A* only sums up all attributes of all neighbor vertices not the vertex itself. Therefore, we need to add an identity matrix *I* to *A*. Then the Equation (2) becomes:

&emsp;&emsp;&emsp;&emsp;f(H^{(l)}, A) = ReLU(\hat{D}^{-\frac{1}{2}}\hat{A}\hat{D}^{-\frac{1}{2}}H^{(l)}W^{(l)}),&emsp;&emsp;&emsp;&emsp;(3)

where $\\hat{A} = A + I$ and $\\hat{D}$ is the diagonal vertex degree matrix of $\\hat{A}$. We set the *L* = 3, which means the network has three convolutional layers to reconstruct the structure of *A* to get *Z*. Assume we decide to keep half number of attributes from previous layer in every layer in this work, we have $F = \\frac{D}{2^L}= \\frac{D}{8}$ after three layers.

### Model Optimization

As described earlier, our model needs to simultaneously preserve the local and
global structure. In other words, a jointly optimization mechanism is needed for
the first-order and the second-order proximity.

For our model, we use a common graph Laplacian regularization term loss
function to optimize:

&emsp;&emsp;&emsp;&emsp;L = L_{first} + \lambda L_{second},&emsp;&emsp;&emsp;&emsp;(4)

where *L*<sub>*f**i**r**s**t*</sub> denotes the supervised loss of the first-order proximity, the labeled part of the graph. *L*<sub>*s**e**c**o**n**d*</sub> denotes the unsupervised loss of the second-order proximity, and *λ* is a trade-off factor between *L*<sub>*f**i**r**s**t*</sub> and *L*<sub>*s**e**c**o**n**d*</sub>.

For *L*<sub>*f**i**r**s**t*</sub>, we can simply define it as:

&emsp;&emsp;&emsp;&emsp;L\_<span>first</span> = , v\_i v\_j,&emsp;&emsp;&emsp;&emsp;(5)

where *v*<sub>*i*</sub> and *v*<sub>*j*</sub> are two vertices linked by an edge. *y*<sub>*i*</sub> and *y*<sub>*j*</sub> are two matrices constructed based on selected neighbor vertices and a 64-dimensions vector represents each vertex generated by DeepWalk. Since the number of neighbor vertices for each vertex may be different, we use zero elements to fill up the smaller matrix to make sure both matrices have the same size in order to perform this computation.

For the *L*<sub>*s**e**c**o**n**d*</sub>, we define it as:



