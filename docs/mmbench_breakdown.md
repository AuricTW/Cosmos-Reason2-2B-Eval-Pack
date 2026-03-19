# MMBench Breakdown

## Overview

This document summarizes the category-level `MMBench` performance observed for `nvidia/Cosmos-Reason2-2B` on `mmbench_en_dev`.

The overall score was:

- `gpt_eval_score = 74.65635738831615`
- table value: `74.7`

## Category-level accuracy

| Category | Score |
| --- | ---: |
| action_recognition | `94.444` |
| attribute_comparison | `59.091` |
| attribute_recognition | `90.541` |
| celebrity_recognition | `88.889` |
| function_reasoning | `83.544` |
| future_prediction | `40.000` |
| identity_reasoning | `97.778` |
| image_emotion | `88.000` |
| image_quality | `54.717` |
| image_scene | `97.115` |
| image_style | `88.679` |
| image_topic | `88.889` |
| nature_relation | `52.083` |
| object_localization | `60.494` |
| ocr | `71.795` |
| physical_property_reasoning | `60.000` |
| physical_relation | `62.500` |
| social_relation | `86.047` |
| spatial_relationship | `26.667` |
| structuralized_imagetext_understanding | `60.256` |

## L2-category accuracy

| L2 category | Score |
| --- | ---: |
| attribute_reasoning | `77.889` |
| coarse_perception | `85.473` |
| finegrained_perception (cross-instance) | `62.238` |
| finegrained_perception (instance-level) | `79.181` |
| logic_reasoning | `53.390` |
| relation_reasoning | `66.957` |

## Strongest categories

The strongest `MMBench` categories in this run were:

- `identity_reasoning`: `97.778`
- `image_scene`: `97.115`
- `action_recognition`: `94.444`
- `attribute_recognition`: `90.541`

These results suggest the model is particularly effective on recognition-heavy perception tasks, scene understanding, and identity-focused reasoning.

## Weakest categories

The weakest `MMBench` categories in this run were:

- `spatial_relationship`: `26.667`
- `future_prediction`: `40.000`
- `nature_relation`: `52.083`
- `image_quality`: `54.717`

These weaker areas suggest that the model is less reliable on spatial reasoning, forward prediction, and some fine-grained relational or perceptual judgments.

## Interpretation

The `MMBench` profile is not flat. Instead, it shows a clear capability shape:

- strong on recognition and scene-level understanding
- solid on OCR and social/identity-related categories
- comparatively weaker on spatial, predictive, and relation-heavy categories

This makes the overall `74.7` easier to interpret. The score does not come from uniform competence across all multimodal reasoning types; it comes from a mixture of very strong perception-oriented subskills and noticeably weaker spatial and predictive subskills.
