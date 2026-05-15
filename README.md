[README.md](https://github.com/user-attachments/files/27811858/README.md)
# Lightsheet Pipeline

A Jupyter notebook for end-to-end processing of LifeCanvas SmartSPIM lightsheet microscopy data.

## What it does

| Step | Description |
|------|-------------|
| 0 | Install dependencies |
| 1 | Dataset exploration — folder scan, TIFF inspection, channel detection |
| 2 | Lazy loading with Dask (memory-safe for >1 TB volumes) |
| 3 | Preprocessing — background subtraction, normalization |
| 4 | Save to Zarr (chunked, compressed, multiscale) |
| 5 | Registration / stitching via phase-correlation |
| 6 | 3D cell segmentation with Cellpose |
| 7 | Cell count & morphology table |
| 8 | Fluorescence intensity analysis & colocalization |
| 9 | 3D visualization with Napari |
| 10 | Summary report (JSON) |

## Supported channels

CFP · GFP · tdTomato · CFP-boost (auto-detected from folder names matching `Ex_XXX_Em_XXX`)

## Requirements

- Python 3.9+
- CUDA GPU recommended for Cellpose segmentation (step 6); CPU fallback available

Install all dependencies by running **Section 0** of the notebook:

```bash
pip install tifffile zarr "dask[array]" "napari[all]" \
    scikit-image scipy pandas matplotlib seaborn \
    cellpose ome-zarr aicsimageio fsspec
```

## Quick start

1. Clone this repo
2. Open `lightsheet_pipeline.ipynb` in Jupyter
3. In **Section 1**, set `ROOT` to your dataset folder:
   ```python
   ROOT = Path(r"path/to/your/dataset")
   ```
4. Run cells **in order** — each section saves intermediate results to `ROOT/_pipeline_output/`

## Outputs

All outputs are written to `<ROOT>/_pipeline_output/`:

| File | Description |
|------|-------------|
| `explore_report.json` | Dataset inventory |
| `preprocessed.zarr` | Background-subtracted, normalized volume |
| `labels.zarr` | Cellpose segmentation labels |
| `cell_table.csv` | Per-cell morphology and intensity measurements |
| `colocalization.csv` | Per-chunk Pearson correlation between channels |
| `fluorescence_plots.png` | Intensity distribution and Z-profile plots |
| `pipeline_summary.json` | Final summary statistics |

## Notes

- **Voxel size**: Update `VOXEL_SCALE` in Section 9 to match your acquisition parameters (default: `2.0, 1.8, 1.8` µm for SmartSPIM)
- **Segmentation model**: Change `MODEL_TYPE` in Section 6 (`cyto3` for cell bodies, `nuclei` for DAPI-stained nuclei)
- **GPU**: Set `USE_GPU = False` in Section 6 if running on CPU
- **Large tile stitching**: For full multi-tile grids, [BigStitcher (Fiji)](https://imagej.net/plugins/bigstitcher/) or [`stitchwell`](https://pypi.org/project/stitchwell/) are recommended over the built-in phase-correlation approach

## License

MIT
