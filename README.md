# MOV to MP4 Video Converter with Audio Muting

A Python-based batch video conversion utility that converts `.MOV` files into `.MP4` format using **FFmpeg**, while automatically removing the audio track. The notebook also detects NVIDIA GPU/NVENC support and uses hardware-accelerated H.264 encoding when available.

## Features

* 🎥 Converts `.MOV` videos to `.MP4`
* 🔇 Automatically mutes/removes audio from converted videos
* 📁 Supports batch conversion of multiple video files
* 🔍 Searches recursively through input directories
* 🔄 Supports both `.MOV` and `.mov` extensions
* ⚡ Automatically detects NVIDIA GPU availability
* 🚀 Uses **NVIDIA NVENC** when supported
* 💻 Falls back to **libx264 CPU encoding** when GPU acceleration is unavailable
* 📂 Preserves the input folder structure in the output directory
* 📊 Displays conversion progress using `tqdm`
* ☁️ Supports optional Google Drive mounting in Google Colab

## Technology Stack

| Component       | Purpose                            |
| --------------- | ---------------------------------- |
| Python          | Main programming language          |
| FFmpeg          | Video conversion and encoding      |
| H.264 / libx264 | CPU-based video encoding           |
| NVIDIA NVENC    | GPU-accelerated video encoding     |
| tqdm            | Conversion progress display        |
| Google Colab    | Optional execution environment     |
| pathlib         | File and directory handling        |
| subprocess      | Running FFmpeg and system commands |

## How It Works

The converter follows this workflow:

```text
Input Directory
      │
      ▼
Find .MOV / .mov Files
      │
      ▼
Detect NVIDIA GPU
      │
      ▼
Check FFmpeg NVENC Support
      │
      ├── GPU + NVENC Available
      │          │
      │          ▼
      │     H.264 NVENC
      │
      └── Otherwise
                 │
                 ▼
           H.264 libx264
      │
      ▼
Remove Audio (-an)
      │
      ▼
Save as .MP4
```

## Requirements

The notebook requires:

* Python 3
* FFmpeg
* `tqdm`

The provided notebook installs FFmpeg and `tqdm` using:

```bash
apt-get install ffmpeg
pip install tqdm
```

## Running in Google Colab

Open the notebook in Google Colab and execute the cells sequentially.

If your videos are stored in Google Drive, the notebook provides an optional Google Drive mounting step:

```python
from google.colab import drive
drive.mount('/content/drive')
```

After mounting, provide the appropriate input and output folder paths.

## Usage

Run the main section:

```python
if __name__ == "__main__":
    input_folder = input("Enter input folder path: ").strip()
    output_folder = input("Enter output folder path: ").strip()
    convert_folder(input_folder, output_folder, recursive=True)
```

You will be prompted for:

```text
Enter input folder path:
Enter output folder path:
```

### Example

```text
Enter input folder path: /content/input_videos
Enter output folder path: /content/output_videos
```

The converter then searches the input directory recursively for `.MOV` and `.mov` files.

## Audio Muting

The converter intentionally removes the audio stream during conversion.

The FFmpeg command includes:

```bash
-an
```

This disables audio output, resulting in an MP4 video without an audio track.

## GPU Acceleration

Before conversion, the notebook checks:

1. Whether an NVIDIA GPU is available using `nvidia-smi`.
2. Whether the installed FFmpeg build supports NVIDIA NVENC.

If both are available, the converter uses:

```bash
-c:v h264_nvenc
```

with CUDA hardware acceleration.

Otherwise, it falls back to:

```bash
-c:v libx264
```

using CPU-based H.264 encoding.

## Encoding Modes

### NVIDIA GPU

When NVIDIA NVENC is available:

```bash
ffmpeg -y -hwaccel cuda -i input.MOV -an -c:v h264_nvenc -preset p7 output.mp4
```

### CPU

When GPU acceleration is unavailable:

```bash
ffmpeg -y -i input.MOV -an -c:v libx264 -preset fast -crf 23 output.mp4
```

## Folder Structure

The converter preserves the relative folder structure from the input directory.

For example:

```text
input/
├── video1.MOV
├── video2.mov
└── recordings/
    └── video3.MOV
```

Produces:

```text
output/
├── video1.mp4
├── video2.mp4
└── recordings/
    └── video3.mp4
```

## Progress Tracking

The notebook uses `tqdm` to display conversion progress:

```python
for src in tqdm(files, desc="Converting files"):
```

Each successfully converted file is reported with its output location.

If FFmpeg encounters an error, the notebook prints the failed source file and FFmpeg's output.

## Main Functions

### `run(cmd)`

Executes a shell command using Python's `subprocess` module and returns:

* Return code
* Command output

### `has_nvidia_gpu()`

Checks whether an NVIDIA GPU is available by executing:

```bash
nvidia-smi -L
```

### `ffmpeg_supports_nvenc()`

Checks FFmpeg encoder support for:

* `h264_nvenc`
* `hevc_nvenc`

### `build_command()`

Builds the appropriate FFmpeg command based on whether NVENC acceleration is enabled.

### `convert_folder()`

Handles the complete batch conversion process:

* Validates input directory
* Creates the output directory
* Detects GPU/NVENC support
* Finds `.MOV` files
* Creates corresponding `.mp4` output paths
* Converts videos
* Removes audio
* Reports success or failure

## Important Notes

* Only `.MOV` and `.mov` files are selected for conversion.
* Audio is intentionally removed from the output videos.
* Existing output files may be overwritten because FFmpeg is invoked with `-y`.
* NVIDIA GPU acceleration requires a compatible NVIDIA environment and an FFmpeg build with NVENC support.
* If no `.MOV` files are found, the notebook reports that there is nothing to convert.
* The notebook is configured for Python 3 and includes Google Colab metadata.

## Project Structure

```text
MOV-to-MP4-Converter/
│
├── _mov-to-mp4-converter-with-muting-audio-ability.ipynb
└── README.md
```

## Use Cases

This utility is useful for:

* Batch conversion of `.MOV` recordings
* Preparing videos for platforms requiring MP4
* Removing unwanted audio tracks
* Processing large video collections
* GPU-accelerated video conversion
* Automated video preprocessing pipelines
* Google Colab-based video processing

## License

No explicit software license is specified in the provided notebook. If this project is published on GitHub or GitLab, add an appropriate license file according to your intended usage and distribution terms.

## Author

**Research/Developer Knight**

---

### Disclaimer

This project is provided as a video-processing utility. Conversion speed and output characteristics may vary depending on the input video properties, FFmpeg version, CPU, GPU, storage system, and execution environment.
