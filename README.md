<div align="center">

# TV-AudioRemover: Joint Text‑Visual Guided Sound Removal with Multi‑Task Hard‑Mixture Curriculum

<p align="center">
  <img src="https://img.shields.io/badge/arXiv-Coming%20Soon-b31b1b.svg?logo=arxiv&logoColor=white" alt="arXiv Coming Soon"/>
  &nbsp;
  <a href="https://yjx-research.github.io/TV-AudioRemover/" style="text-decoration:none"><img src="https://img.shields.io/badge/Demo%20Page-Explore-4285f4.svg?logo=googlechrome&logoColor=white" alt="Demo Page"/></a>
  &nbsp;
  <a href="https://github.com/PixCtrol/TV-AudioRemover" style="text-decoration:none"><img src="https://img.shields.io/badge/GitHub-Code-blue.svg?logo=github&logoColor=white" alt="GitHub Repository"/></a>
  &nbsp;
  <img src="https://img.shields.io/badge/Models-Coming%20Soon-ffd21e.svg?logo=huggingface&logoColor=black" alt="Hugging Face Models Coming Soon"/>
</p>

</div>

<p align="center">
If you find this project useful, please consider giving a star ⭐️~
</p>

## 📣 **Updates**

- [x] **Technical report** — Released with the full method, data construction pipeline, evaluation benchmark, and experimental results.
- [x] **[Demo Page](https://yjx-research.github.io/TV-AudioRemover/)** — Available with intuitive side-by-side target sound removal comparisons between TV-AudioRemover and all evaluated baseline models.
- [ ] **Inference code** — Coming soon.
- [ ] **Model weights** — Coming soon.
- [ ] **AV-Remove-Bench** — Coming soon, including the benchmark samples and evaluation protocol.


<div align="center">

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

### 👥 **Authors**

<div>
    <!-- Row 1: 6 authors -->
    <div style="margin-bottom: 2px;">
        Xinyue Guo<sup>*</sup>,&nbsp;
        Jianxuan Yang<sup>*†</sup>,&nbsp;
        Daiguo Zhou<sup>*</sup>
    </div>
    <!-- Row 2: 7 authors -->
    <div>
        Jiagao Hu,&nbsp;
        Yuxuan Chen,&nbsp;
        Fei Wang,&nbsp;
        Jian Luan
    </div>
</div>
<!-- Affiliations -->
<div>
    MiLM Plus, Xiaomi Inc.
    <br>
    *Equal contribution &nbsp;&nbsp; †Corresponding author
</div>
</div>

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 🎧 **Overview**

**TV-AudioRemover** is a target sound removal system for selectively eliminating unwanted sounds while preserving the rest of an audio mixture. Its primary use case is soundtrack cleanup after visual object removal: given a visually edited video, the original mixed audio, and a natural-language instruction, it suppresses the sound associated with the removed visual object. Built on a multimodal diffusion Transformer (MM-DiT) with flow matching, TV-AudioRemover generates cleaned audio at 44.1 kHz.

Beyond visually edited videos, the same selective sound-removal capability opens up broader application scenarios, including removing off-screen voice-overs or narration, text-guided editing of audio-only content, and soundtrack-only removal of an in-frame sound from an otherwise unedited video. These use cases enable flexible cleanup of speech, music, and sound effects without muting or regenerating the entire soundtrack.

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 🎨 **Teaser Figure**

<div align="center">
    <img src="assets/teaser.png" width="60%">
    <p style="margin-top: 8px; text-align: center; font-style: italic;">
        Overview of the Visual-Text Guided Sound Removal framework TV-AudioRemover.
    </p>
</div>

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 🚀 **Key Features**

- **Visual-Text Guided Sound Removal** — Remove the sound of a visually deleted object using both the edited video and a text instruction for precise multimodal control.
- **Flexible Application Scenarios** — Extend selective sound removal to off-screen voice-over or narration cleanup, text-guided audio-only editing, and in-frame sound removal from videos whose visuals remain unchanged.
- **Multi-Task Capability** — Supports extraction (keep target), deletion (remove target), and joint preserve-remove modes via generalized instruction modeling.
- **Million-Scale Data Construction Pipeline** — A scalable, quality-controlled pipeline for constructing high-quality single-object audio-visual aligned samples and synthesizing task-specific mixture-target pairs across speech, music, and sound effects.
- **AV-Remove-Bench** — The first audio-visual target removal benchmark with comprehensive scene diversity and broad acoustic coverage, equipped with two removal-specific objective metrics introduced in this work—Target Source Suppression Ratio (TSSR) and Preserved Source Fidelity (PSF)—along with an MLLM-based evaluation protocol.

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 📦 **Data Construction Pipeline**

<div align="center">
    <img src="assets/pipeline.png" width="90%">
    <p style="margin-top: 8px; text-align: center; font-style: italic;">
        Data processing pipeline for constructing single-object audio-visual aligned samples.
    </p>
</div>

To acquire high-quality training data, we devise a pipeline to construct a million-scale dataset of single-object audio-visual aligned samples, from which we synthesize mixture-target pairs customized for model training.

**Stage 1 — MLLM Tagging**: Qwen3-Omni annotates each candidate with structured information (scene type, source multiplicity, sound category, main sounding-object label) and checks audio-visual correspondence. Mismatched samples are rejected.

**Stage 2 — Source Classification**: Aligned samples are divided into single-source and multi-source cases. Multi-source samples are processed by SAM Audio to separate the source corresponding to the main visual object.

**Stage 3 — Quality Filtering**: A proxy hard filter (CLAP × PC score) and a perceptual filter (SAJ score) ensure both semantic alignment and audio quality. Category balancing over speech, music, and sound effects yields the final clean manifest.

**Stage 4 — Mixture Synthesis**: For each clean sample A, semantically different interference clips B and optionally C are mixed with A to form two-source (A+B) and three-source (A+B+C) mixtures, generating three edit modes: *extract* (mixture = A+B(+C), output = A), *delete* (mixture = A+B+C, output = A+C, i.e., remove B and preserve the rest), and *both* (mixture = A+B, output = A).

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 📊 **AV-Remove-Bench**

We present **AV-Remove-Bench**, the first audio-visual target removal benchmark with comprehensive scene diversity and broad acoustic coverage. It contains 77 audio-visual clips (~6 seconds each) covering speech, music, and sound effects.

**Data Sources:**
- **Public datasets** — MUSIC-AVQA (music), AVSpeech (speech), Condensed Movies / AVSBench / VGGSound-test (sound effects)
- **Generated samples** — Seedance 2.0 with manually designed prompts spanning all three audio categories
- **Real recordings** — Daily real-world scenes focusing on sound-effect-centric object removal

**Scenario Types:**
- Multi-sounding-object scenes (two or more clearly distinguishable sounding objects)
- Foreground-target-plus-stable-background scenes (foreground target sound mixed with stable background ambience)

**Evaluation Protocol:**
- *Objective metrics*: Target Source Suppression Ratio (TSSR), Preserved Source Fidelity (PSF), IS, SAJ Overall, IB-AV, DeSync
- *MLLM-based judging*: Gemini 2.5 Pro scores four aspects (visual target removal, video fidelity, audio target removal, preserved audio fidelity) on a 1–5 scale

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 🧠 **Method Highlights**

<div align="center">
    <img src="assets/tv-audioremover.png" width="90%">
    <p style="margin-top: 8px; text-align: center; font-style: italic;">
        Architecture of the TV-AudioRemover multimodal diffusion Transformer.
    </p>
</div>

- **Task Tokens & Generalized Instructions**: Learnable task embeddings (extract / delete / both) combined with diverse natural-language templates improve instruction following and reduce prompt overfitting.
- **Modality-Specific Global Guidance**: Dedicated global conditions for visual, textual, and audio branches — the audio branch is modulated by the visual condition (positive cue about retained scene) rather than text.
- **Multi-Task Learning**: Joint training on extraction, deletion, and preserve-remove tasks enhances target-source localization and prevents over-removal.
- **Hard-Mixture Curriculum**: Fine-tuning with acoustically confusing mixtures (e.g., male vs. female voices, timbre-similar instruments) sharpens fine-grained discrimination.

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 📈 **Performance**

TV-AudioRemover achieves state-of-the-art performance on both objective and subjective metrics across audio-visual joint editing models and audio-editing models on AV-Remove Bench.

### Metric Guide

- **Audio quality and separation:** **IS** measures overall audio quality and diversity, while **SAJ Overall** measures perceptual source-separation quality.
- **Target removal and source preservation:** **TSSR** measures how effectively the target sound is suppressed, while **PSF** measures how faithfully non-target sounds are preserved.
- **Instruction following and audio fidelity:** **Instr. Comp.<sub>a</sub>** measures whether the requested target sound is removed, while **Fidelity<sub>a</sub>** evaluates the naturalness and preservation of the remaining audio.
- **Audio-visual consistency:** **IB-AV** measures semantic correspondence between the edited audio and video, while **DeSync** estimates their temporal offset. Higher is better for all metrics except **DeSync**, where lower is better.

### Objective Evaluation

<table>
  <thead>
    <tr>
      <th rowspan="2" align="center">Group</th>
      <th rowspan="2" align="left">Method</th>
      <th colspan="2" align="center">Audio Quality &amp; Separation</th>
      <th colspan="2" align="center">Target Removal &amp; Preservation</th>
      <th colspan="2" align="center">Instruction Following &amp; Audio Fidelity</th>
      <th colspan="2" align="center">Audio-Visual Consistency</th>
    </tr>
    <tr>
      <th align="center">IS ↑</th>
      <th align="center">SAJ ↑</th>
      <th align="center">TSSR ↑</th>
      <th align="center">PSF ↑</th>
      <th align="center">Instr. Comp.<sub>a</sub> ↑</th>
      <th align="center">Fidelity<sub>a</sub> ↑</th>
      <th align="center">IB-AV ↑</th>
      <th align="center">DeSync ↓</th>
    </tr>
  </thead>
  <tbody>
    <tr><td align="center">T-AV</td><td>AVI-Edit</td><td align="center">3.16</td><td align="center">2.24</td><td align="center">0.260</td><td align="center">0.958</td><td align="center">3.56</td><td align="center">2.57</td><td align="center">19.22</td><td align="center">0.77</td></tr>
    <tr><td align="center">T-AV</td><td>InstructAV2AV</td><td align="center">3.45</td><td align="center">2.60</td><td align="center">-0.288</td><td align="center">0.986</td><td align="center">2.65</td><td align="center">3.04</td><td align="center">14.81</td><td align="center">0.76</td></tr>
    <tr><td align="center">T-A</td><td>ZETA</td><td align="center">3.27</td><td align="center">2.95</td><td align="center">0.571</td><td align="center">1.054</td><td align="center">3.69</td><td align="center">3.22</td><td align="center">18.71</td><td align="center">0.75</td></tr>
    <tr><td align="center">T-A</td><td>Audio-Omni</td><td align="center">2.15</td><td align="center">2.20</td><td align="center">0.496</td><td align="center">0.901</td><td align="center">4.39</td><td align="center">1.68</td><td align="center">8.23</td><td align="center">1.13</td></tr>
    <tr><td align="center">T-A</td><td>UNISON</td><td align="center"><strong>3.63</strong></td><td align="center">2.77</td><td align="center">-0.239</td><td align="center">0.999</td><td align="center">4.27</td><td align="center">3.44</td><td align="center">18.00</td><td align="center">0.78</td></tr>
    <tr><td align="center">VT-A</td><td>SAM Audio</td><td align="center">3.23</td><td align="center">2.42</td><td align="center">0.354</td><td align="center">1.007</td><td align="center">3.70</td><td align="center">3.90</td><td align="center">17.89</td><td align="center">0.93</td></tr>
    <tr><td align="center">VT-A</td><td><strong>TV-AudioRemover (Ours)</strong></td><td align="center">3.55</td><td align="center"><strong>3.46</strong></td><td align="center"><strong>0.635</strong></td><td align="center"><strong>1.079</strong></td><td align="center"><strong>4.79</strong></td><td align="center"><strong>3.96</strong></td><td align="center"><strong>27.32</strong></td><td align="center"><strong>0.60</strong></td></tr>
  </tbody>
</table>

> Group: **T-AV** = audio-visual joint removal; **T-A** = audio-only editing (no visual condition); **VT-A** = visual-text conditioned audio-only editing.

### Human Subjective Evaluation

| Method | TRC ↑ | BP ↑ | TN ↑ | OQ ↑ | AC ↑ | ASR ↑ |
|:-------|:-----:|:----:|:----:|:----:|:----:|:-----:|
| AVI-Edit | 0.51 | 0.32 | 0.35 | 0.35 | 39.37 | 21.43% |
| InstructAV2AV | 0.38 | 0.43 | 0.28 | 0.31 | 37.20 | 18.86% |
| ZETA | 0.42 | 0.63 | 0.55 | 0.55 | 53.36 | 36.69% |
| Audio-Omni | 0.48 | 0.24 | 0.20 | 0.19 | 30.75 | 11.69% |
| UNISON | 0.65 | 0.60 | 0.60 | 0.62 | 62.00 | 49.35% |
| SAM Audio | 0.86 | 0.83 | 0.89 | 0.88 | 85.72 | 81.58% |
| **TV-AudioRemover (Ours)** | **0.98** | **0.89** | **0.94** | **0.95** | **93.80** | **97.40%** |

> **Human evaluation metrics:** TRC measures target removal completeness; BP measures preservation of non-target background audio; TN measures temporal smoothness and naturalness; OQ measures overall listening quality. AC is their weighted composite score, and ASR is the percentage of samples that succeed across all four dimensions.

### Comparison with Commercial Audio-Visual Editing Models

We further compare TV-AudioRemover with commercial audio-visual editing systems on AV-Remove-Bench. TV-AudioRemover achieves the strongest target sound suppression, preserved-source fidelity, and audio instruction compliance, while remaining competitive in audio-visual consistency.

<table>
  <thead>
    <tr>
      <th rowspan="2" align="left">Method</th>
      <th colspan="2" align="center">Audio Quality &amp; Separation</th>
      <th colspan="2" align="center">Target Removal &amp; Preservation</th>
      <th colspan="2" align="center">Instruction Following &amp; Audio Fidelity</th>
      <th colspan="2" align="center">Audio-Visual Consistency</th>
    </tr>
    <tr>
      <th align="center">IS ↑</th>
      <th align="center">SAJ ↑</th>
      <th align="center">TSSR ↑</th>
      <th align="center">PSF ↑</th>
      <th align="center">Instr. Comp.<sub>a</sub> ↑</th>
      <th align="center">Fidelity<sub>a</sub> ↑</th>
      <th align="center">IB-AV ↑</th>
      <th align="center">DeSync ↓</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>Seedance 2.0</td><td align="center">3.36</td><td align="center">2.50</td><td align="center">-0.563</td><td align="center">0.970</td><td align="center">3.17</td><td align="center">3.83</td><td align="center">23.71</td><td align="center">0.58</td></tr>
    <tr><td>Seedance 2.5</td><td align="center">2.85</td><td align="center">2.93</td><td align="center">0.363</td><td align="center">1.0588</td><td align="center">4.36</td><td align="center">4.11</td><td align="center"><strong>30.66</strong></td><td align="center"><strong>0.49</strong></td></tr>
    <tr><td>MiniMax H3</td><td align="center">3.30</td><td align="center">2.25</td><td align="center">0.076</td><td align="center">1.0588</td><td align="center">3.41</td><td align="center"><strong>4.30</strong></td><td align="center">23.52</td><td align="center">0.64</td></tr>
    <tr><td><strong>TV-AudioRemover (Ours)</strong></td><td align="center"><strong>3.55</strong></td><td align="center"><strong>3.46</strong></td><td align="center"><strong>0.635</strong></td><td align="center"><strong>1.079</strong></td><td align="center"><strong>4.79</strong></td><td align="center">3.96</td><td align="center">27.32</td><td align="center">0.60</td></tr>
  </tbody>
</table>

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 📝 **Citation**

If you find this repository useful, please consider citing our paper:

```bibtex
@misc{xxx,
  title={Cxxx}, 
  author={xxx},
  year={xxx},
  eprint={xxx},
  archivePrefix={arXiv},
  primaryClass={xxx},
  url={xxx}, 
}
```

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 🙏 **Acknowledgments**

This project uses the following datasets:<br>
VGGSound (<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" style="color:#007bff; text-decoration:none;">CC BY 4.0</a>), AudioSet (<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" style="color:#007bff; text-decoration:none;">CC BY 4.0</a>), AVSpeech (<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" style="color:#007bff; text-decoration:none;">CC BY 4.0</a>), Condensed Movies (<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" style="color:#007bff; text-decoration:none;">CC BY 4.0</a>), WavCaps (research-only), ACAVCaps (<a href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank" style="color:#dc3545; text-decoration:none;">CC BY-NC 4.0</a>), MUSIC-AVQA (<a href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank" style="color:#dc3545; text-decoration:none;">CC BY-NC 4.0</a>), and AVSBench (<a href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank" style="color:#dc3545; text-decoration:none;">CC BY-NC 4.0</a>).<br>
All resources are used for <strong>academic and non-commercial demonstration purposes only</strong>.

This project is inspired by the following works:<br>
[stable-audio-tools](https://github.com/Stability-AI/stable-audio-tools), [MMAudio](https://github.com/hkchengrex/MMAudio), [MMAudioSep](https://github.com/sony/mmaudiosep), [Make-An-Audio 2](https://github.com/bytedance/Make-An-Audio-2), [Synchformer](https://github.com/v-iashin/Synchformer), and [BigVGAN](https://github.com/NVIDIA/BigVGAN).<br>
Thanks for their contributions.

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

## 📞 **Contact**

If you have any questions or suggestions, please feel free to contact us at yangjianxuan@xiaomi.com.

<hr style="border: none; border-top: 3px solid #333; margin: 16px 0;">

<div align="center">

2026 TV-AudioRemover Project. All Rights Reserved.

</div>
