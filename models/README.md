# Model

After extensive data processing, attention was turned to developing the model itself. The use of AI in dance allows for a wide range of creative possibilities. Many different tasks can be explored, but one stands out in this project: dance interpretability to help in the generation of new phrases.

The task involves studying the hidden movements of a duet, extracting information that is not visually evident. For example, it might not be immediately clear that the movement of one dancer's right foot is directly connected to the movement of the other dancer's hip or left hand. The objective thus is to model and tackle a **Graph Structure Learning** problem, uncovering the connections (or different types of connections) between the dancers' joints. More specific technical details are described in a dedicated section below.

<!-- The second task is a natural continuation of the first. Using the connections learned from the first model or maybe even a graph structure defined by a user, the goal is to **Generate New Dance Sequences** guided by these connections. More clearly, this pipeline aims to create new movements that follow a suggested line. The technical details of this model are also provided in its respective section below, although it remains in the conceptual stage at this point in the project. -->

## Set Up

Refer to the [installation document](https://github.com/Luizerko/ai_choreo/blob/master/models/INSTALL.md) to set up your own version of the project.

## Loading Data

The next step in the project involves loading the dance data for AI modeling. First, the preprocessed data is read in an interleaved manner to separately extract data from both dancers. Adjacencies are then created by initializing a default skeleton with 29 joints for each dancer and connecting every joint of one dancer to all joints of the other.

<div align="center">
    <img src="https://github.com/Luizerko/ai_choreo/blob/master/models/assets/skeleton_and_connections.png", width="550">
</div>
<div align='center'>
    <span>Plot of the two dancers with only their skeletons (left) and fully connected to their partner (right).</span>
    <br><br>
</div>

The purpose of mapping both dancers' skeletons in this way is to ensure the model focuses on the connections between them, rather than the connections within each individual dancer. While a dancer's joint movements can often be predicted by analyzing their other joints, the aim here is to highlight the interactions between the dancers, identifying which joints of one dancer influence the other and vice versa. Additionally, both directions of the edges are considered, allowing the model to assess the direction of influence between each joint of the two dancers.

These connections are what the graph structure learning model will classify categorically by the degree of influence in the movement, ranging from non-existing to core connections. The data is then prepared for model training by creating batches with PyTorch tensors. The tensors are structured with dimensions representing the total number of sequences, the sequence length, the number of joints from both dancers, and 3D coordinates + 3D velocity estimates. Finally, a training-validation split is created to allow for proper model hyperparameter tuning.

To include data augmentation and improve model generalization, the training pipeline incorporates a data processing step that involves rotating batches of data. Each batch is rotated along the Z-axis by a randomly selected angle while maintaining the original X and Y-axis orientations for physical consistency. This approach helps prevent the model from overfitting to the dancers' absolute positions.

## Neural Relational Inference Variant

As the title suggests, this model is a variant of the [Neural Relational Inference (NRI)](https://arxiv.org/abs/1802.04687) model, which itself is an extension of the traditional [Variational Autoencoder (VAE)](https://arxiv.org/abs/1312.6114). The primary objective of the original model is to study particles moving together in a system without prior knowledge of their underlying relationships. By analyzing their movement (position and velocity), the model estimates a graph structure that connects these particles, aiming to reveal which particles exert force on the others. For a dedicated study on the architecture's efficacy, refer to the [architecture experiment document](https://github.com/Luizerko/ai_choreo/blob/master/models/ARCH_EXPERIMENT.md), which discusses results based on a charged particles simulation dataset from the original paper.

In the context of this project, the particles are represented by the joints of dancers. While the physical connections between joints within a dancer's body are known, this information alone is insufficient to understand the partnering relationships between two dancers.

Since a target graph structure correctly identifying which joints are virtually connected during a dance performance is unavailable, and considering that this graph can change over time even within a performance, self-supervising techniques are employed - one of the reasons for choosing the VAE framework.

The model consists of an encoder and a decoder, both playing around with transforming node representations into edge representations and vice versa. This approach emphasizes the dynamics of movements rather than fixed node embeddings. Not only that, but the encoder specifically outputs edges, sampling these from the generated latent space, making it essential to switch between representations.

<div align="center">
    <img src="https://github.com/Luizerko/ai_choreo/blob/master/models/assets/final_arch.png", width="650">
</div>
<div align="center">
    <span>Image adapted from the <a href='https://arxiv.org/abs/1802.04687'>original NRI paper</a> showing a schematic overview of the final model architecture, including the GCN nodes and the GRNN adapatation.</span>
    <br><br>
</div>

This project's implementation, even though very similar to the NRI MLP-Encoder MLP-Decoder model, includes a few important modifications:

- **Graph Convolutional Network (GCN):** Some MLP layers are replaced with GCN layers to leverage the graph structure, improving the model's ability to capture relationships between joints. This change focuses on a subset of edges connecting both dancers rather than studying all particle relationships as in the original implementation. Additionally, GCNs provide local feature aggregation and parameter sharing, important inductive biases for this context, resulting in enhanced generalization in a scenario with dynamic (and unknown) graph structures.

- **Graph Recurrent Neural Network (GRNN) Decoder:** To make better use of sequential information and achieve a more suitable final embedding for predicting (or reconstructing) the next frame, beyond just spatial information from the graphs, it is essential to use a recurrent network. The decoder is therefore implemented with LSTM nodes in the original sequence, while also using the graph structure sampled from the latent space generated by the encoder.

- **Custom GCN-LSTM Cells:** To utilize the recurrent structure crucial for sequence processing while maintaining graph information and GNN architecture, the classic LSTM cell has been reimplemented with GCN nodes. Currently only the decoder incorporates the recurrent component, which generates a final sequence embedding that the model uses to reconstruct the next frame.

By incorporating these modifications, the model maintains the core principles of the original NRI model while theoretically enhancing its ability to generalize and adapt to the dynamic nature of dance performances.

### Final Architecture

**Encoder:**

- The encoder begins with a GCN layer, which transforms node representations into edge representations.
  
- This is followed by an MLP layer, batch normalization, and dropout.

- Next, the edge representations are converted back into nodes, and another GCN layer is applied.

- The nodes are then transformed back into edges, followed by another MLP layer with a skip connection from the previous dropout layer.

- Finally, an MLP layer generates logits that represent edge types, ranging from non-existent edges to those most critical for the movement being analyzed.

**Decoder:**

- The decoder starts by sampling a Gumbel-Softmax distribution using the logits generated by the encoder. This approach approximates sampling in a continuous distribution and employs Softmax to handle the reparameterization trick, ensuring the pipeline remains fully differentiable.

- With the newly sampled edge index, the decoder processes the data through a GRNN composed of modified LSTM nodes with GCN layers, followed by a transformation of the final sequence embedding into edge representations.

- This is followed by an MLP layer, batch normalization, and dropout.

- Finally, the edge representations are converted back into nodes, and a GCN layer is applied to predict (or reconstruct) the next frame.