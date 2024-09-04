# Addressing a Simpler Problem

During the development of the model for the dance scenario, challenges arose in reconstructing frames and predicting edges. These issues, along with extensive model iterations, data augmentations, and various experiments, led to questioning the architecture's ability to learn the desired dynamics, prompting further testing.

To better assess the model's capabilities, the decision was made to revisit the [original NRI paper](https://arxiv.org/abs/1802.04687) and use their charged particles dataset. The original problem was much simpler — featuring ten times fewer particles, hundreds of times fewer edges, and a greater availability of data — providing a clearer benchmark for evaluating the NRI variant's performance on a less complex task.

The experiment provided valuable insights. Even in a simplified version with significantly reduced hidden dimensions, the model showed the ability to produce reasonable reconstructions and edge predictions. Although the results are not perfect, the model's potential to address the graph dynamics problem became clear.

These findings have shifted the focus to refining the architecture and adjusting parameters within the simpler problem's framework. The goal is to identify a version that can be scaled up to effectively tackle the more complex dance setting. Simultaneously, the goal is to simplify the choreography dataset by selectively sampling dancers, aligning both experiments more closely and better exploiting the architecture.

## Generating Data

The data generation process involved using the `data/generate_dataset.py` script with the default settings from the [original project repository](https://github.com/ethanfetaya/NRI), but with the option to simulate charged particles in motion. This resulted in a training dataset of 50000 simulations, each simulation including 5 particles with 2D coordinates and 2D velocities, moving over 49 frames. The interactions between charges are defined by a random undirected graph, which controls the particles' accelerations, affecting their speed and position in each frame.

<div align="center">
    <img src="https://github.com/Luizerko/ai_choreo/blob/master/models/assets/skeleton_and_connections.png", width="550">
</div>
<div align='center'>
    <span>On the left, you can see a complete simulation, where the paths of different particles are shown in various colors, starting with lighter shades and ending with darker ones. On the right, you see the interaction graph between the particles, where the edges between vertices indicate bidirectional interactions between the respective particles.</span>
    <br><br>
</div>

To prepare the model input, the original sequence of 49 frames is first reduced to 48 frames to simplify testing different sequence splits. Subdivisions are then tested using sub-trajectories of 8, 12, 16, or 24 frames. To ensure the training process remains within a reasonable time frame (under 24 hours), non-overlapping sequences are used, meaning each simulation is divided into non-overlapping segments of $\frac{48}/{sequence_size}$. The next section provides additional visualizations and detailed information on the performance of the models tested for each sequence size.

## Testing Architecture

