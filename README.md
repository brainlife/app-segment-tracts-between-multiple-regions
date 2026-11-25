[![Abcdspec-compliant](https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg)](https://github.com/soichih/abcd-spec)
[![Run on Brainlife.io](https://img.shields.io/badge/Brainlife-bl.app.191-blue.svg)](https://doi.org/10.25663/brainlife.app.191)

# app-roi2roitracking

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

**Segments a tractogram into tracts by keeping streamlines that connect user-specified pairs of ROIs from an ROI directory, using compiled MATLAB wma_tools, and outputs a white matter classification.**

Runs the compiled wma_tools function bsc_SegROIfromPairStringList_v3 on the tractogram and the ROI directory: for each line of roiPairs it combines the listed ROIs into two endpoint regions (optionally inflated) and assigns to a tract the streamlines that connect them. Use it to extract specific connections from a whole-brain tractogram after generating ROIs. Needs a tractogram and ROIs in the same space. The surface-generation step in main reads an 'atlas' config key that this App does not provide.

Run it on brainlife.io: [https://doi.org/10.25663/brainlife.app.191](https://doi.org/10.25663/brainlife.app.191)

| Input | Datatype | Required | Description |
|---|---|---|---|
| `track` | `neuro/track/tck` | yes | Whole-brain tractogram (.tck) to segment. |
| `ROI` | `neuro/rois` | yes | Directory of ROI NIfTI files whose numbers are referenced in roiPairs. Must be in the same space as the tractogram. |

| Output | Datatype | Description |
|---|---|---|
| `classification` | `neuro/wmc` | White matter classification (classification.mat) with one tract per ROI pair. |

| Parameter | Type | Default | Description |
|---|---|---|---|
| `roiPairs` | string |  | Pairs of ROI sets to segment between: each line is one tract, with the two endpoint sets separated by ';' and each set listing atlas/ROI indices separated by spaces. |
| `smoothKernel` | number | `1` | Odd whole-number kernel size used to smooth (inflate) each ROI with MATLAB smooth3 before segmentation; values above 1 inflate the ROI and 0 disables inflation. |

Runtime: container `brainlife/mcr:neurodebian1604-r2017a`; 8 CPUs; 18 h walltime.

<!-- brainlife-app:end -->

This app will perform ensemble tracking between 2 or more cortical regions of interest (ROIs) from either a freesurfer parcellation or an atlas parcellation. First, the ROIs are registered to diffusion space using Freesurfer's mri_label2vol, and a white matter mask is generated in diffusion space, by running the create_wm_mask script. Then, tracking will be performed using mrtrix/0.2.12 by running the trackROI2ROI script. Finally, a classification structure will be generated using Vistasoft's bsc_mergeFGandClass and bsc_makeFGsFromClassification functions by running the classificationGenerator script.

### Authors
- Dan Bullock (dnbulloc@iu.edu)
- Brad Caron (bacaron@iu.edu)

### Contributors
- Soichi Hayashi (hayashi@iu.edu)
- Franco Pestilli (franpest@indiana.edu)

### Funding
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)

## Running the App 

### On Brainlife.io

You can submit this App online at [https://doi.org/10.25663/brainlife.app.191](https://doi.org/10.25663/brainlife.app.191) via the "Execute" tab.

### Running Locally (on your machine)

1. git clone this repo.
2. Inside the cloned directory, create `config.json` with something like the following content with paths to your input files.

```json
{
	"roiPairs":	"23 95 2 8\n1 2 12 34\n8 3 5\n4 5",
	"track":  "path/to/tck.tck",
	"atlas":  "path/to/atlas.nii.gz",
   	"smoothKernel": 0
}
```

### Sample Datasets

You can download sample datasets from Brainlife using [Brainlife CLI](https://github.com/brain-life/cli).

```
npm install -g brainlife
bl login
mkdir input

roi & atlas sample needed

```


3. Launch the App by executing `main`

```bash
./main
```

## Output

The main outputs of this App is a 'track.tck' file, a folder called 'tracts' containing .json files for each tract, an 'classification.mat' containing the classification structure, and a text file called 'output_fibercounts.txt' which contains information regarding the number of streamlines in each tract.

#### Product.json
The secondary output of this app is `product.json`. This file allows web interfaces, DB and API calls on the results of the processing. 

### Dependencies

This App requires the following libraries when run locally.

  - singularity: https://singularity.lbl.gov/
  - VISTASOFT: https://github.com/vistalab/vistasoft/
  - ENCODE: https://github.com/brain-life/encode
  - SPM 8 or 12: https://www.fil.ion.ucl.ac.uk/spm/software/spm8/
  - WMA: https://github.com/brain-life/wma
  - Freesurfer: https://hub.docker.com/r/brainlife/freesurfer/tags/6.0.0
  - mrtrix: https://hub.docker.com/r/brainlife/mrtrix_on_mcr/tags/1.0
  - jsonlab: https://github.com/fangq/jsonlab.git
