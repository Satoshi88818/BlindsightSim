
# Blindsight Simulator v1.2

## Overview

**Blindsight Simulator** is a research-grade Python-based tool designed to simulate visual experience through **cortical** and **retinal** visual prostheses. It models phosphene-based vision with a high degree of physiological and perceptual realism, including:

- Eccentricity-dependent phosphene size and crowding
- Radial elongation (common in cortical implants)
- Temporal scintillation & multiplicative noise
- Electrode dropout, random failure, crosstalk
- Filling-in phenomena
- Persistence, adaptation, fatigue
- Motion blur from head movement & microsaccades
- Center-surround antagonism
- Monocular depth modulation (via optical flow proxy)
- Optional brightness-to-pitch sonification

The simulator supports **real-time webcam input** or static images, and produces side-by-side visualization: original scene vs. simulated phosphene percept.

It is intended for researchers, educators, neuroscientists, bioengineers, and anyone interested in understanding the perceptual limitations and possibilities of current and next-generation visual prostheses (Argus II, Orion, PRIMA, etc.).

## Features

- Cortical (log-polar magnification) vs. Retinal (uniform) mapping
- Multiple patient profiles: naive, Argus veteran, optimistic next-gen, Orion-like, training progression
- Configurable feature extractors: DoG, Canny, high-pass/Laplacian
- Realistic noise & artifact modeling (dropout, jitter, crosstalk, fatigue)
- Dynamic effects: filling-in, persistence, adaptation, motion blur, microsaccades
- Optional sonification (brightness → tone frequency + eccentricity/depth modulation)
- Visual field overlay with eccentricity rings and meridians
- Interactive controls (saccades, extractor cycling, reset)
- GIF export capability
- Training mode: simulates perceptual learning by gradually reducing noise/dropout/fatigue

## Requirements

### System
- Python 3.8 – 3.12
- Webcam (optional – for live mode)
- Decent CPU (GPU acceleration possible via PyTorch)

### Python Dependencies
```text
numpy==1.26.4
opencv-python==4.9.0.80
matplotlib==3.8.3
scipy==1.12.0
pygame==2.5.2          # sonification
torch==2.2.1           # differentiable retinotopy
Pillow==10.2.0         # GIF writer
```

Install with:
```bash
pip install numpy opencv-python matplotlib scipy pygame torch Pillow
```

(For GPU support: install appropriate `torch` version with CUDA.)

## Installation

```bash
# Clone or download the repository
git clone https://github.com/your-repo/blindsight-simulator.git
cd blindsight-simulator

# Install dependencies
pip install -r requirements.txt
```

(If you don't have a `requirements.txt` yet, create one with the list above.)

## Usage

### Basic run (webcam, default cortical, Argus-veteran profile)
```bash
python blindsightv12.py
```

### Common examples

```bash
# Retinal implant, optimistic next-gen profile
python blindsightv12.py --implant_type retinal --patient_profile optimistic_next_gen_retinal

# Static image instead of webcam
python blindsightv12.py --no-use_webcam --image_path face.jpg

# Enable sonification (use headphones)
python blindsightv12.py --enable_sonification

# Save 10-second GIF
python blindsightv12.py --no-use_webcam --save_gif --gif_duration 10.0

# High-resolution grid, early cortical-like (Orion style)
python blindsightv12.py --grid_size 48 --patient_profile early_cortical_orion_like
```

### Interactive keys (while running)
- `s` → simulate large saccade
- `q` / `w` → previous / next feature extractor
- `r` → reset view shift

Full options:
```bash
python blindsightv12.py --help
```

## Architecture (High-Level)

1. **Configuration** → Argparse + patient profile presets
2. **Retinotopic grid** → Torch-based log-polar warp + empirical noise
3. **Input → Preprocessing** → Webcam / image → grayscale → feature extraction
4. **Warping & Downsampling** → to electrode grid
5. **Temporal & Perceptual Effects** → persistence, adaptation, noise, dropout, fatigue
6. **Center-Surround** → DoG contrast enhancement
7. **Depth proxy** → optical flow magnitude → nearer = brighter / less crowded
8. **Phosphene Rendering** → per-electrode Gaussian kernels with:
   - eccentricity scaling
   - radial ellipticity
   - shape variation (Gaussian / streak / comet)
   - current spread
   - filling-in
   - crosstalk (suppressive + facilitatory)
   - position jitter
9. **Motion blur** → directional kernel based on head/microsaccade velocity
10. **Sonification** → optional per-phosphene tone (brightness + ecc + depth)
11. **Visualization** → Matplotlib FuncAnimation + VF overlay

## Suggested Improvements

### Short-term / Bug fixes
- Better error handling for webcam failure
- Prevent occasional NaN / division-by-zero crashes
- Smoother training mode parameter interpolation
- Fix sonification threading/overlap (current version can sound chaotic)

### Medium-term
- Replace optical-flow depth proxy with lightweight monocular depth model (MiDaS, DepthAnything, etc.)
- Vectorize phosphene rendering loop (NumPy / Torch) for 64×64+ grids
- Add basic GUI (Tkinter / DearPyGui) for parameter tuning
- Export phosphene movie / per-frame data for psychophysics experiments

### Long-term / Research extensions
- Binocular / stereo simulation
- Simulate field defects (glaucoma, RP scotomas)
- Integrate simple CNN-based object recognition confidence → phosphene emphasis
- Add simulated training / rehabilitation tasks (object localization, maze, reading)
- Support hexagonal electrode layouts
- VR / AR headset output mode

## License

MIT (or choose your preferred open-source license)

## Acknowledgments

Built with inspiration from real patient reports (Argus II, Orion), cortical magnification literature (Horton-Hoyt, Sereno), and phosphene psychophysics studies.

Happy hacking — and may your simulated phosphenes one day become useful vision.
