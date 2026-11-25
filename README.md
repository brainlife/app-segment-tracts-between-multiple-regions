<h1 align="center">WMA tract segmentation between regions</h1>

<p align="center">
  A white matter classification with one tract per pair of regions, from a whole-brain tractogram and a set of ROIs or an atlas.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.191"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.191-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-lightgrey.svg" alt="MIT license"></a>
</p>

## 🧠 At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Segments a tractogram into tracts by keeping streamlines that connect user-specified pairs of ROIs from an ROI directory, using compiled MATLAB wma_tools, and outputs a white matter classification.**

Runs the compiled wma_tools function bsc_SegROIfromPairStringList_v3 on the tractogram and the ROI directory: for each line of roiPairs it combines the listed ROIs into two endpoint regions (optionally inflated) and assigns to a tract the streamlines that connect them. Use it to extract specific connections from a whole-brain tractogram after generating ROIs. Needs a tractogram and ROIs in the same space. The surface-generation step in main reads an 'atlas' config key that this App does not provide.

| | |
|---|---|
| 📥 **Inputs** | `neuro/track/tck` · `neuro/rois` |
| 📤 **Outputs** | `neuro/wmc` |
| 🐳 **Container** | `brainlife/mcr:neurodebian1604-r2017a` |
| ⚙️ **Resources** | 8 CPUs · 18 h walltime |
| 🔗 **Run it** | [doi.org/10.25663/brainlife.app.191](https://doi.org/10.25663/brainlife.app.191) |

#### 📥 Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `track` | `neuro/track/tck` | yes | Whole-brain tractogram (.tck) to segment. |
| `ROI` | `neuro/rois` | yes | Directory of ROI NIfTI files whose numbers are referenced in roiPairs. Must be in the same space as the tractogram. |

#### 📤 Outputs

| Output | Datatype | Description |
|---|---|---|
| `classification` | `neuro/wmc` | White matter classification (classification.mat) with one tract per ROI pair. |

<details>
<summary><b>⚙️ Parameters</b> (2)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `roiPairs` | string |  | Pairs of ROI sets to segment between: each line is one tract, with the two endpoint sets separated by ';' and each set listing atlas/ROI indices separated by spaces. |
| `smoothKernel` | number | `1` | Odd whole-number kernel size used to smooth (inflate) each ROI with MATLAB smooth3 before segmentation; values above 1 inflate the ROI and 0 disables inflation. |

</details>

<!-- brainlife-app:end -->

## 🏷️ Versions on brainlife.io

Each App is built from its own branch of this repository.

| App | Regions from | Branch | Run it | Status |
|---|---|---|---|---|
| WMA Tract Segmentation with ROIs | a `neuro/rois` directory | [`master`](https://github.com/brainlife/app-segment-tracts-between-multiple-regions/tree/master) | [doi:10.25663/brainlife.app.191](https://doi.org/10.25663/brainlife.app.191) | current |
| WMA Tract Segmentation with Atlas | a `neuro/parcellation/volume` atlas | [`1.0`](https://github.com/brainlife/app-segment-tracts-between-multiple-regions/tree/1.0) | [doi:10.25663/brainlife.app.190](https://doi.org/10.25663/brainlife.app.190) | available |

## 🚀 Running the App

### ☁️ On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.191](https://doi.org/10.25663/brainlife.app.191), choose a whole-brain `neuro/track/tck` tractogram and a `neuro/rois` dataset in the same space, enter the region pairs and press **Execute**. The App runs the compiled [wma_tools](https://github.com/DanNBullock/wma_tools) function `bsc_SegROIfromPairStringList`: for each line of `roiPairs` it combines the listed ROIs into two endpoint regions and assigns to a tract the streamlines that connect them. The result is a `neuro/wmc` classification with one tract per line. Use it to extract specific connections from a whole-brain tractogram, for example after generating ROIs with the ROI Generator Apps.

- `roiPairs`: one tract per line; the two endpoint sets are separated by `;`, and each set lists ROI (or atlas) indices separated by spaces.
- `smoothKernel` (default 1): odd whole-number kernel size used to smooth (inflate) each ROI with MATLAB `smooth3`; values above 1 inflate the ROI and 0 disables inflation.

The *Atlas* variant ([doi:10.25663/brainlife.app.190](https://doi.org/10.25663/brainlife.app.190)) takes the regions from the integer indices of a volumetric atlas and also generates VTK surfaces of the atlas regions for the viewer. Converting the atlas to ROIs and using the *ROIs* variant is recommended.

### 💻 Locally

You need [Singularity/Apptainer](https://apptainer.org/) and [`jq`](https://jqlang.github.io/jq/). The compiled MATLAB code runs inside the MATLAB Runtime R2017a container, and the surface step in a Python VTK container.

1. Clone this repository (`master` is the ROIs App; `git checkout 1.0` for the Atlas App).
2. Create `config.json` with the paths to your inputs and the region pairs:

   ```json
   {
       "track": "/input/track/track.tck",
       "ROI": "/input/rois/rois",
       "roiPairs": "<ROIs of end 1>;<ROIs of end 2>\n<next tract>",
       "smoothKernel": 1
   }
   ```

   For the Atlas App, replace `ROI` with `"atlas": "/input/parcellation/parc.nii.gz"`.

3. Run the App:

   ```bash
   ./main
   ```

## 📦 Outputs

```text
classification.mat   white matter classification: one tract per line of roiPairs
```

The Atlas App also writes VTK surfaces of the atlas regions for visualisation.

## 🩺 Troubleshooting

- **Surface step with the ROIs App**: after segmentation, `main` generates surfaces from an `atlas` config key, which the ROIs App does not provide. If this step fails, check whether `classification.mat` was already produced by the segmentation step.

## 📚 Citing

> [!TIP]
> If you use this App in your work, please cite the App, the software it builds on and brainlife.io (below). GitHub's **Cite this repository** button gives the App citation in APA and BibTeX.

- WMA Tract Segmentation with ROIs. brainlife.io App. [doi:10.25663/brainlife.app.191](https://doi.org/10.25663/brainlife.app.191)
- Bullock et al. Associative white matter connecting the dorsal and ventral posterior human cortex. *Brain Structure and Function* 224:2631-2660 (2019). [doi:10.1007/s00429-019-01907-8](https://doi.org/10.1007/s00429-019-01907-8)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## 👥 Authors

- Dan Bullock
- Brad Caron

**Contributors:** Soichi Hayashi, Franco Pestilli, David Hunt

The App builds on [vistasoft](https://github.com/vistalab/vistasoft), [encode](https://github.com/brain-life/encode), [WMA](https://github.com/brain-life/wma) and [jsonlab](https://github.com/fangq/jsonlab).

## 💰 Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io is publicly funded, and acknowledging that support helps keep it running. This App was developed with support from Wellcome, the U.S. National Institutes of Health (including the BRAIN Initiative BRAIN CONNECTS program: The Axonal Projectome EXchange, U24NS140384, and the Center for Mesoscale Connectomics, UM1NS132207) and the U.S. National Science Foundation.</sub>

## ⚖️ License

[MIT](LICENSE).
