# TRACE-PAINTING-5MIN-SAMPLE

TRACE-PAINTING-5MIN-SAMPLE is an egocentric multimodal dataset offering 5 minutes and 37 seconds of painting tasks spanning 10 trajectories across 6 episodes.

You can preview the dataset in the Trace Web Explorer: [https://explorer.tracelabs.ai/d/trace-painting-5min-sample](https://explorer.tracelabs.ai/d/trace-painting-5min-sample).

Each episode includes a series of time-synced topics spanning sensor data, annotations, calibration intrinsics, and visualization overlays packaged into a single mcap file.

Sensors (9):

- Three world cameras (left/center/right monochrome, 1016x1016)
- Active infrared depth sensor (544x480, encoded as 32FC1 floats)
- Photo camera (rgb, 1280x720)
- Headset pose (6-DoF, ML2 inside-out tracking)
- Inertial measurement unit (3-axis accel + gyro)
- Eye gaze
- On-device microphone (mono PCM16, 16 kHz)

Annotations (4):

- Hand pose and joint positions (26-joint left/right hands per frame)
- Dual-track semantic narrations
    - Short-duration fine-grained action narrations, focused on movement and hand interactions
    - Long-duration activity narrations, focused on broader task context

Calibration & Transforms (6):

- Full kinematic tree that maps all 5 cameras, head pose, hand poses, and eye gaze to the world frame
- Camera calibration intrinsics for all 5 cameras

Visualization (20):

- 5 toggleable visualization overlays for each of the monochrome world cameras and the RGB photo camera. The overlays display one of the following: hand skeletons, eye gaze fixation, action narrations, hand palm axes, and future hand points.

## Directory structure

```
.
├── data                                         # Download from AWS S3
│   └── episode-000
│       └── episode-000.mcap
├── meta
│   ├── episodes.jsonl
│   ├── info.json
│   ├── ontology.json
│   └── trace-foxglove-layout-v2.json
└── README.md
```

- `data/`: Episode-level data, stored in AWS S3 (See download instructions below)
- `meta/episodes.jsonl`: Per episode metadata, local and remote file paths to the episode data, etc.
- `meta/info.json`: Dataset-level metadata
- `meta/ontology.json`: Ontology used by language annotations (e.g., actions, activities) with timestamps
- `meta/trace-foxglove-layout-v2.json`: Foxglove layout template for viewing mcap files in the dataset.
- `README.md`: This file

## Download

First, clone this repository.

```bash
git clone https://github.com/Trace-Datasets/trace-painting-5min-sample.git
cd trace-painting-5min-sample
```

You can use the AWS CLI to download the dataset. AWS keys should have been provided to you by Trace. Reach out to tech-support@tracelabs.ai if you have issues or need access.

```bash
export AWS_ACCESS_KEY_ID=<your-access-key-id>
export AWS_SECRET_ACCESS_KEY=<your-secret-access-key>

# Download the dataset to the `trace-clothesfolding-10min-sample/data` directory
aws s3 cp s3://trace-datasets/trace-painting-5min-sample/1.0/ ./data --recursive
```

The total size of the dataset is ~35GB.

Note that you can use the `meta/trace-foxglove-layout-v2.json` layout template for easy viewing of the mcap files in Foxglove Studio.

## Dataset structure

The mcap file for each episode is structured as follows:

```
# Hands
(1)  /ml2/hands/left                           : foxglove.PosesInFrame [protobuf]
(2)  /ml2/hands/right                          : foxglove.PosesInFrame [protobuf]

# Kinematic Tree
(3)  /tf                                       : foxglove.FrameTransforms [protobuf]

# Head Pose
(4)  /ml2/head/pose                            : foxglove.PoseInFrame [protobuf]

# IMU
(5)  /ml2/imu                                  : trace.imu.v1.Imu [protobuf]

# Eye Gaze
(6)  /ml2/eye/gaze                             : trace.eye.v1.Gaze [protobuf]

# Cameras
(7)  /ml2/mono/left                            : foxglove.RawImage [protobuf]
(8)  /ml2/mono/center                          : foxglove.RawImage [protobuf]
(9)  /ml2/mono/right                           : foxglove.RawImage [protobuf]
(10) /ml2/rgb/picture                          : foxglove.RawImage [protobuf]
(11) /ml2/depth                                : foxglove.RawImage [protobuf]

# Camera Intrinsics
(12) /ml2/mono/left/camera_info                : foxglove.CameraCalibration [protobuf]
(13) /ml2/mono/center/camera_info              : foxglove.CameraCalibration [protobuf]
(14) /ml2/mono/right/camera_info               : foxglove.CameraCalibration [protobuf]
(15) /ml2/rgb/picture/camera_info              : foxglove.CameraCalibration [protobuf]
(16) /ml2/depth/camera_info                    : foxglove.CameraCalibration [protobuf]

# Narrations
(17) /ml2/narrations/actions                   : trace.narrations.v1.Narrations [protobuf]
(18) /ml2/narrations/activities                : trace.narrations.v1.Narrations [protobuf]

# Audio
(19) /ml2/audio/mic                            : foxglove.RawAudio [protobuf]

# Mono Left Overlays
(20) /ml2/mono/left/overlay/hand_skeleton      : foxglove.ImageAnnotations [protobuf]
(21) /ml2/mono/left/overlay/future_hand_points : foxglove.ImageAnnotations [protobuf]
(22) /ml2/mono/left/overlay/palm_axis          : foxglove.ImageAnnotations [protobuf]
(23) /ml2/mono/left/overlay/eye_gaze           : foxglove.ImageAnnotations [protobuf]
(24) /ml2/mono/left/overlay/action_narrations  : foxglove.ImageAnnotations [protobuf]

# Mono Center Overlays
(25) /ml2/mono/center/overlay/hand_skeleton      : foxglove.ImageAnnotations [protobuf]
(26) /ml2/mono/center/overlay/future_hand_points : foxglove.ImageAnnotations [protobuf]
(27) /ml2/mono/center/overlay/palm_axis          : foxglove.ImageAnnotations [protobuf]
(28) /ml2/mono/center/overlay/eye_gaze           : foxglove.ImageAnnotations [protobuf]
(29) /ml2/mono/center/overlay/action_narrations  : foxglove.ImageAnnotations [protobuf]

# Mono Right Overlays
(30) /ml2/mono/right/overlay/hand_skeleton      : foxglove.ImageAnnotations [protobuf]
(31) /ml2/mono/right/overlay/future_hand_points : foxglove.ImageAnnotations [protobuf]
(32) /ml2/mono/right/overlay/palm_axis          : foxglove.ImageAnnotations [protobuf]
(33) /ml2/mono/right/overlay/eye_gaze           : foxglove.ImageAnnotations [protobuf]
(34) /ml2/mono/right/overlay/action_narrations  : foxglove.ImageAnnotations [protobuf]

# RGB Overlays
(35) /ml2/rgb/picture/overlay/hand_skeleton      : foxglove.ImageAnnotations [protobuf]
(36) /ml2/rgb/picture/overlay/future_hand_points : foxglove.ImageAnnotations [protobuf]
(37) /ml2/rgb/picture/overlay/palm_axis          : foxglove.ImageAnnotations [protobuf]
(38) /ml2/rgb/picture/overlay/eye_gaze           : foxglove.ImageAnnotations [protobuf]
(39) /ml2/rgb/picture/overlay/action_narrations  : foxglove.ImageAnnotations [protobuf]
```

### Language annotations

The `narrations` topics contain language annotations for the episode. `Action` narrations are short-duration fine-grained narrations focused on movement and hand interactions. `Activity` narrations are long-duration narrations that segment individual trajectories.

Each narration topics also includes the following structured metadata per annotation:
- `effector`: The hand or hands that performed the action. Either `left_hand`, `right_hand`, or `both_hands`.
- `noun`: The primary noun of the narration, generally the object being interacted with.
- `verb`: The primary verb of the narration, generally the action being performed.

The `meta/ontology.json` file enumerates the ontolog backing the two narration topics and is useful to filter for specific actions, trajectories, etc. The file tracks and assigns stable ids to all distinct nouns, verbs, and noun-verb pairs across the dataset, and supplies per-episode timestamp segments during which the corresponding narration occurred.
