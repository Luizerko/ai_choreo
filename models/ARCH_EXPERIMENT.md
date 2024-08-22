# Addressing a Simpler Problem

During the development of the model for the dance scenario, challenges arose in reconstructing frames and predicting edges. These issues, along with extensive model iterations, data augmentations, and various experiments, led to questioning the architecture's ability to learn the desired dynamics, prompting further testing.

To better assess the model's capabilities, the decision was made to revisit the [original NRI paper](https://arxiv.org/abs/1802.04687) and use their charged particles dataset. The original problem was much simpler — featuring ten times fewer particles, hundreds of times fewer edges, and a greater availability of data — providing a clearer benchmark for evaluating the NRI variant's performance on a less complex task.

The experiment provided valuable insights. Even in a simplified version with significantly reduced hidden dimensions, the model showed the ability to produce reasonable reconstructions and edge predictions. Although the results are not yet perfect, the model's potential to address the graph dynamics problem became clear.

These findings have shifted the focus to refining the architecture and adjusting parameters within the simpler problem's framework. The goal is to identify a version that can be scaled up to effectively tackle the more complex dance setting.

## Generating Data

## Testing Architecture