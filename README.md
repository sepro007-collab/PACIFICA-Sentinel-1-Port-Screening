# PACIFICA Sentinel 1 Port Screening

PACIFICA is a GEOG 761 proof of concept for preparing water-masked Sentinel-1 radar patches around Pacific ports and screening them for unusual post-disaster change. The repository focuses on two linked components:

1. a reproducible training-data pipeline; and
2. Lucy's unsupervised convolutional autoencoder and anomaly-ranking workflow.

The outputs are inspection-priority signals. They are not engineering damage assessments or port-operability decisions.

## Repository structure

```text
notebooks/
  01_training_data_pipeline.ipynb
  02_lucy_unsupervised_autoencoder.ipynb
data/
  unsupervised_manifest.csv
  preprocessing_config.json
results/
  autoencoder_training_curve.png
  port_vila_training_pipeline_output.png
  apia_gita_2018_r3_c1_change_map.png
  unsupervised_patch_results.csv
  unsupervised_port_results.csv
  five port anomaly-grid PNGs
  model configuration JSON files
report/
  final report files
```

## Workflow

The data notebook downloads the World Port Index, filters Pacific ports, defines the pilot port-event cases, queries Sentinel-1 GRD in Google Earth Engine, matches orbit geometry across pre- and post-event windows, builds median VV/VH composites, removes persistent water with JRC Global Surface Water, and exports six-band 128 by 128 pixel GeoTIFF patches. A 3 by 3 grid with 640 m spacing produces nine overlapping patches per event.

The CNN notebook trains a convolutional autoencoder only on pre-event VV/VH imagery. Port-level train, validation, and test splits reduce leakage between overlapping patches. Reconstruction change, latent-feature change, unusually high-error pixel fraction, and direct radar change are combined and grouped with K-means. The high-change group supports manual review but does not supply a verified damage class.

## Reproduction

1. Open `notebooks/01_training_data_pipeline.ipynb` in Google Colab.
2. Set the Google Earth Engine project ID and Google Drive output directory.
3. Authenticate Earth Engine and run the notebook to export the 45 six-band patches and manifest.
4. Open `notebooks/02_lucy_unsupervised_autoencoder.ipynb`.
5. Update `PROJECT_DIR` so it points to the exported manifest and patch directory.
6. Run all cells to train the autoencoder and reproduce the result tables and figures.

The GeoTIFF patches are generated outputs and are excluded from Git by default. This keeps the repository small and avoids committing regenerable data. The manifest records the event windows, orbit selection, water threshold, projection, and image counts needed to audit the exports.

## Environment

The notebooks were developed for Google Colab with Python 3. Required packages are listed in `requirements.txt`. Google Earth Engine credentials and a Drive mount are required for the acquisition notebook.

## Main reported results

- 3,630 World Port Index records were reduced to 70 Pacific ports.
- Five port-event cases produced 45 patches.
- Autoencoder pre-event reconstruction MAE was 0.0970 for training, 0.0947 for validation, and 0.1033 for the held-out port.
- K-means separation of the five change features had a silhouette score of 0.569.
- Six high-change patches were identified, but every one contained less than 20 percent valid land. This is the central reliability limitation.

The `results` directory also includes the exact Port Vila six-band figure rendered by the training-data notebook and an exact Apia CNN error-map output from Lucy's workflow. These are retained as traceable report figures rather than redrawn illustrations.

## Limitations

Only five disasters were analysed, neighbouring patches overlap, and verified port-damage labels were not available across the sample. Radar backscatter also changes with moisture, vegetation, surface roughness, viewing geometry, and port activity. Results should be used to request closer imagery or inspection, not to clear or close a port.

## Code citation

Sepanta Salehi, Lucy Reith, Karthic, Stella, and Wanyuan. (2026). *PACIFICA Sentinel-1 port screening* [Computer software]. GEOG 761, University of Auckland.

Repository: **https://github.com/sepro007-collab/PACIFICA-Sentinel-1-Port-Screening**

