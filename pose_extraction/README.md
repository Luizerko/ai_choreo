# Pose Extraction Pipeline

A key part of any artificial intelligence project is gathering data. That's why we spent a few weeks testing different open-source pose extraction tools to find the best one for our needs. This section highlights the main tools we tested, with descriptions and comparisons, leading to our final choice. It also includes step-by-step instructions on how to install and use the selected tool, so you can easily set it up yourself.

Before diving into the technical details, we want to extend special thanks to [Ilya Vidrin](https://www.ilyavidrin.com/) and his dance team for providing the choreography used in this project. The images shown here come directly from their performances.

## Model Exploration

The first models we explored were for 2D pose extraction. We primarily used [OpenPose](https://github.com/CMU-Perceptual-Computing-Lab/openpose) and [AlphaPose](https://github.com/MVIG-SJTU/AlphaPose/tree/master). OpenPose had many installation and setup issues, so we chose AlphaPose instead. AlphaPose is also a newer, with more modern models and better pose estimation results.

We tested all the models within [AlphaPose model zoo](https://github.com/MVIG-SJTU/AlphaPose/blob/master/docs/MODEL_ZOO.md) and narrowed it down to four final options, as shown in the figure below. These models represent some of the best average results from the repository and pretty much cover all the different possible outputs one can get. From left to right, the models are: Fast Pose (DCN) from the MSCOCO dataset, Fast Pose (DCN) Combined Loss from the COCO WholeBody dataset, Fast Pose from the Halpe dataset with 26 keypoints, and Fast Pose (DCN) Combined 10 Hand Weight Loss from the Halpe dataset with 136 keypoints.

<br>

<div align="center">
  <table>
    <tr>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/model_coverage.png" alt="Visualizing pose extractions coming from different models from AlphaPose." width="800" /><br>
        <span>Visualizing pose extractions coming from different models from AlphaPose. From left to right, the models are: Fast Pose (DCN) from the MSCOCO dataset, Fast Pose (DCN) Combined Loss from the COCO WholeBody dataset, Fast Pose from the Halpe dataset with 26 keypoints, and Fast Pose (DCN) Combined 10 Hand Weight Loss from the Halpe dataset with 136 keypoints.</span>
      </td>
    </tr>
  </table>
</div>

<br>

To more accurately represent the body, having clear markers for the pelvis and torso, we decided to use the Halpe models. To evaluate their quality without any preprocessing, we created the animations below.

<br>

<div align="center">
  <table>
    <tr>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/2d_halpe26_joints.gif" alt="Visualizing Halpe 26 keypoints poses" width="400" /><br>
        <span>Visualizing poses extracted using the model trained on Halpe dataset with 26 keypoints.</span>
      </td>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/2d_halpe136_joints.gif" alt="Visualizing Halpe 136 keypoints poses" width="400"/><br>
        <span>Visualizing poses extracted using the model trained on Halpe dataset with 136 keypoints.</span>
      </td>
    </tr>
  </table>
</div>

<br>

However, it is clear that both models require further processing for final use. This is because they struggle with some frames, losing track of one or both dancers, or repeating poses and placing the dancers in the same position. Additionally, the joints appear unstable, showing significant local vibration and resulting in very shaky poses. While the models promise better results when a tracking pipeline is added, we found the improvements to be minimal, as shown in the GIFs below. Interpolation between frames would still be needed to achieve more stable pose extraction.

<br>

<div align="center">
  <p><strong>Studying tracking improvements for Halpe 26 keypoints.</strong></p>
  <table>
    <tr>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/halpe_26_no_tracking.gif" alt="Visualizing Halpe 26 no tracking" width="400" /><br>
        <span>Model with no tracking.</span>
      </td>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/halpe_26_tracking.gif" alt="Visualizing Halpe 26 with tracking" width="400"/><br>
        <span>Model with tracking.</span>
      </td>
    </tr>
  </table>
</div>

<br>

<div align="center">
  <p><strong>Studying tracking improvements for Halpe 136 keypoints.</strong></p>
  <table>
    <tr>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/halpe_26_no_tracking.gif" alt="Visualizing Halpe 136 no tracking" width="400" /><br>
        <span>Model with no tracking.</span>
      </td>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/halpe_26_tracking.gif" alt="Visualizing Halpe 136 with tracking" width="400"/><br>
        <span>Model with tracking.</span>
      </td>
    </tr>
  </table>
</div>

<br>

The 2D processing pipelines though were not fully implemented and won't be discussed here because we decided to use 3D models instead. 3D pose extraction adds crucial information for generating choreography, especially for pairs of dancers. It captures the richness of each dancer's movements and the subtleties of their interactions in the full space, which would be lost if we reduced the dimensionality.

With this in mind, we explored two main 3D pose extraction models: [VIBE](https://github.com/mkocabas/VIBE) and [HybrIK](https://github.com/Jeff-sjtu/HybrIK) (the latter being part of the AlphaPose pipeline). The GIFs below show the pose extraction and mesh reconstructions performed by each model.

<br>

<div align="center">
  <p><strong>VIBE 3D pose extraction.</strong></p>
  <table>
    <tr>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/3d_vibe_joints.gif" alt="Joints for VIBE 3D pose extraction" width="400" /><br>
        <span>Joints from 3D pose extraction coming from VIBE.</span>
      </td>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/3d_vibe_video.gif" alt="Mesh for VIBE 3D pose extraction" width="500"/><br>
        <span>Mesh reconstruction from 3D pose extraction coming from VIBE.</span>
      </td>
    </tr>
  </table>
</div>

<br>

<div align="center">
  <p><strong>HybrIk 3D pose extraction.</strong></p>
  <table>
    <tr>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/3d_hybrik_joints.gif" alt="Joints for HybrIk 3D pose extraction" width="400" /><br>
        <span>Joints from 3D pose extraction coming from HybrIk.</span>
      </td>
      <td align="center">
        <img src="https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/assets/3d_hybrik_video.gif" alt="Mesh for HybrIk 3D pose extraction" width="500"/><br>
        <span>Mesh reconstruction from 3D pose extraction coming from HybrIk.</span>
      </td>
    </tr>
  </table>
</div>

<br>

It became clear to us, including from the GIFs above, that the model integrated with AlphaPose performs much better than VIBE. It excels both in identifying instances and in extracting the poses accurately.

Therefore, the rest of this document will focus on using AlphaPose for 3D pose extraction. We will provide a step-by-step guide for installation and setup. The examples shown will better represent the actual scenario, as AlphaPose supports multi-instance pose extraction, allowing us to extract poses for two dancers simultaneously. This is an improvement over HybrIK, which only handles single-person pose extraction, but was used in this section for easier visualization and pose extraction capabilities assessment.

## Set Up

Refer to the [installation document](https://github.com/Luizerko/ai_choreo/blob/master/pose_extraction/INSTALL.md) to set up your own version of the project.

## Extracting Poses