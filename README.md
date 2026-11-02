# Learn YOLO

A small workspace for learning and experimenting with [Ultralytics YOLO](https://docs.ultralytics.com/). It currently includes the pretrained `yolo26n.pt` object-detection checkpoint and a saved video prediction under `runs/`.

## Setup

Python 3.12 is used in the existing environment.

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install ultralytics
```

Confirm that YOLO and its dependencies are available:

```bash
yolo checks
```

## Run inference

Replace the source with an image, a video, a directory, a URL, or a webcam index. See [Choose a camera on Linux](#choose-a-camera-on-linux) to find the correct index.

```bash
# Image or video
yolo predict model=yolo26n.pt source=path/to/input.mp4

# Webcam (replace 0 with your camera's index)
yolo predict model=yolo26n.pt source=0 show=True
```

Predictions are written to `runs/detect/predict/` by default. Ultralytics creates incremented directories such as `predict2/` when the default output already exists.

The same workflow can be used from Python:

```python
from ultralytics import YOLO

model = YOLO("yolo26n.pt")
results = model.predict(source="path/to/input.jpg", save=True)
```

### Choose a camera on Linux

List connected cameras and their device paths:

```bash
v4l2-ctl --list-devices
```

For example, the Astra Pro HD camera was listed on this machine as:

```text
Astra Pro HD Camera:
    /dev/video48
    /dev/video49
```

The number after `/dev/video` is the YOLO `source` number. A camera can expose multiple devices, including a video stream and a metadata device. Inspect a device with:

```bash
v4l2-ctl -d /dev/video48 --info
```

Under **Device Caps**, look for **Video Capture**. A device showing only **Metadata Capture** cannot provide images. For the Astra camera above, `/dev/video48` provides video and `/dev/video49` provides metadata, so run:

```bash
yolo predict model=yolo26n.pt source=48 show=True
```

Device numbers can change after reconnecting a camera or rebooting. Run the listing again when switching cameras, then use the number of the video capture device as `source`.

## Train on a custom dataset

Prepare a dataset configuration file in the [Ultralytics YOLO format](https://docs.ultralytics.com/datasets/detect/), then run:

```bash
yolo detect train model=yolo26n.pt data=path/to/data.yaml epochs=100 imgsz=640
```

Training outputs, metrics, and checkpoints are saved beneath `runs/detect/train/`.

## Export a model

For example, export the checkpoint to ONNX:

```bash
yolo export model=yolo26n.pt format=onnx
```

## Workspace layout

```text
.
├── yolo26n.pt                 # pretrained YOLO26 Nano checkpoint
├── runs/detect/predict/0.avi # saved prediction output
├── .venv/                    # local Python environment
└── .vscode/                  # editor settings
```

Run `yolo help` for more commands and `yolo cfg` to inspect configurable options.
