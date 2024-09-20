# AI-Generated Choreography - from Solos to Duets

This repository is dedicated to the development of the project [AI-Generated Choreography - from Solos to Duets](https://humanai.foundation/gsoc/2024/proposal_ChoreoAI1.html). Here, you will find all the documentation and code for implementing my pipelines.

Special thanks to my supervisors, [Mariel Pettee](https://marielpettee.com/) and [Ilya Vidrin](https://www.ilyavidrin.com/), for all their guidance, and to my work partner, Zixuan Wang, for developing [her pipeline](https://github.com/wang-zixuan/AI-Choreo-Duets/tree/main) alongside mine.

## Duet ChorAIgraphy: Using GNNs to Study Duets

<div align="center">
    <img src="https://github.com/Luizerko/ai_choreo/blob/master/assets/duet_choraigraphy_logo.png", width="450">
</div>

Duet ChorAIgraphy aims to implement a pipeline using Graph Neural Networks (GNNs) to study dance duets. The project focuses on **Interpretability of Movements:** a pipeline that learns about the connection between the dancers' bodies in different dance sequences.

<!-- 2. **Generation of New Sequences:** A pipeline that uses these learned connections to generate new dance sequences. -->

The pipeline is discussed in more detail below, along with a presentation of key results. For a more comprehensive explanation of the model, refer to the `model` directory, which contains its implementations as well as complete documentation on it.

## Repository Hierarchy

- The root of the repository naturally provides an overview of the project and showcases sample generations from the developed model.

- The `evaluation_test` folder contains the application for the contributor position. It includes a `README` file with detailed information on the implementations for the selection process test, along with the results obtained. The folder also contains the notebook with the source code for the developed components. The `README` was later expanded as the project development continued.

- The `pose_extraction` folder contains details about the pose extraction pipeline used to create the project dataset. It includes an installation process guide, the raw data used, and information on the development and execution of the idea.

- The `model` folder contains the project's core, including installation instructions, pipelines designs, and running guidelines for the interpretability of movements model. It also has detailed results for a thorough evaluation on the quality of the final agent.

## Sample Generations

The project has produced a wide range of results. Its creative and subjective nature offers many opportunities for exploration, even in the most unexpected outputs. However, while this subjectivity allows for different perspectives, it also creates challenges in getting a more precise evaluation of the model. Below are examples of connections (both undirected and directed) computed by the model in various scenarios. For more examples and a deeper study about what the model is doing, please check the [documentation under `model`](https://github.com/Luizerko/ai_choreo/blob/master/models/README.md).

