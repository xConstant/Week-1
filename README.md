# Traffic Violation Detection System

A computer vision-based system for detecting and flagging vehicles violating traffic rules such as running red lights, crossing lane boundaries, and exceeding speed limits.

## Features

- **Vehicle Detection & Tracking**: Detects vehicles in video frames and tracks them across frames using centroid-based tracking
- **Red Light Violation Detection**: Identifies vehicles that cross the stop line during a red signal
- **Lane Violation Detection**: Detects vehicles crossing lane boundaries
- **Speed Estimation**: Estimates vehicle speed and flags speeding violations
- **License Plate Recognition (OCR)**: Detects and reads license plates using Tesseract OCR
- **Real-time Processing**: Processes video feeds in real-time with visual annotations
- **Violation Logging**: Automatically saves snapshots of violations with timestamps

## Project Structure

```
traffic-violation-detection/
├── main.py                    # Main execution file
├── vehicle_detector.py        # Vehicle detection module
├── violation_monitor.py       # Violation detection logic
├── license_plate_ocr.py       # License plate OCR module
├── config.py                  # Configuration management
├── requirements.txt           # Python dependencies
├── README.md                  # This file
├── violations/                # Output directory (auto-created)
│   ├── violation_*.jpg        # Violation snapshots
│   └── output_*.avi          # Recorded videos
└── config.json               # Configuration file (optional)
```

## Installation

### Prerequisites

1. **Python 3.7+**
2. **Tesseract OCR** (for license plate recognition)
   - **Ubuntu/Debian**: `sudo apt-get install tesseract-ocr`
   - **macOS**: `brew install tesseract`
   - **Windows**: Download from [GitHub](https://github.com/UB-Mannheim/tesseract/wiki)

### Install Python Dependencies

```bash
pip install -r requirements.txt
```

### Optional: YOLO Weights (for YOLO-based detection)

If you want to use YOLO instead of background subtraction:

1. Download YOLOv3 weights and config:
```bash
wget https://pjreddie.com/media/files/yolov3.weights
wget https://raw.githubusercontent.com/pjreddie/darknet/master/cfg/yolov3.cfg
wget https://raw.githubusercontent.com/pjreddie/darknet/master/data/coco.names
```

2. Update `config.json` to use YOLO:
```json
{
    "detection_method": "yolo"
}
```

## Usage

### Basic Usage

Run with webcam (default):
```bash
python main.py
```

Run with video file:
```bash
python main.py --video path/to/video.mp4
```

Run with custom configuration:
```bash
python main.py --video traffic.mp4 --config config.json
```

### Keyboard Controls

- **q**: Quit the application
- **s**: Save current frame
- **r**: Toggle video recording

### Creating Configuration File

Generate a default configuration file:
```bash
python config.py
```

This creates `config.json` which you can edit to customize:

```json
{
    "detection_method": "background_subtraction",
    "min_contour_area": 500,
    "stop_line_y": null,
    "lane_boundaries": [200, 400, 600],
    "speed_limit_kmh": 60,
    "pixel_to_meter": 0.05,
    "fps": 30,
    "output_dir": "violations",
    "auto_record": false
}
```

### Testing License Plate OCR

Test OCR on a single image:
```bash
python license_plate_ocr.py path/to/image.jpg
```

## Configuration Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `detection_method` | Detection method: 'background_subtraction' or 'yolo' | 'background_subtraction' |
| `min_contour_area` | Minimum area for vehicle detection | 500 |
| `stop_line_y` | Y-coordinate of stop line (null for auto) | null |
| `lane_boundaries` | X-coordinates of lane dividers | null |
| `speed_limit_kmh` | Speed limit in km/h | 60 |
| `pixel_to_meter` | Calibration: pixels to meters ratio | 0.05 |
| `fps` | Video frame rate | 30 |
| `light_cycle_frames` | Frames per traffic light cycle (simulation) | 300 |
| `output_dir` | Directory for saving violations | 'violations' |
| `auto_record` | Auto-record processed video | false |

## Calibration

### Speed Estimation Calibration

To accurately estimate speed, calibrate the `pixel_to_meter` ratio:

1. Measure a known distance in the video frame (e.g., 5 meters)
2. Count the pixels covering that distance
3. Calculate: `pixel_to_meter = distance_meters / distance_pixels`
4. Update in `config.json`

### Stop Line Configuration

1. Run the system and observe the frame
2. Note the Y-coordinate where vehicles should stop
3. Set `stop_line_y` in `config.json`

### Lane Boundaries

1. Identify X-coordinates of lane dividing lines
2. Set as array in `config.json`: `"lane_boundaries": [200, 400, 600]`

## Detection Methods

### Background Subtraction (Default)
- No external files needed
- Works well with static cameras
- Fast and efficient
- Best for controlled environments

### YOLO (Optional)
- Requires model weights (~250MB)
- More accurate object detection
- Better for varying conditions
- Slower processing speed

## Output

### Violation Snapshots
Saved to `violations/` directory with format:
```
violation_[type]_[timestamp]_frame[number].jpg
```

### Video Recording
Optional video recording with format:
```
output_[timestamp].avi
```

### Console Output
Real-time violation summary displayed in terminal.

## System Architecture

```
┌─────────────────────┐
│   Video Input       │
│  (Camera/File)      │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│  Vehicle Detector   │
│  - Background Sub   │
│  - YOLO (optional)  │
│  - Object Tracking  │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│ Violation Monitor   │
│  - Red Light        │
│  - Lane Crossing    │
│  - Speed Check      │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│ License Plate OCR   │
│  (Optional)         │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│  Output             │
│  - Visual Overlay   │
│  - Snapshots        │
│  - Video Recording  │
└─────────────────────┘
```

## Limitations

- Speed estimation requires proper calibration
- OCR accuracy depends on image quality and plate format
- Background subtraction works best with static cameras
- YOLO requires significant computational resources

## Future Enhancements

- [ ] Deep learning-based license plate detection
- [ ] Multi-camera support
- [ ] Database integration for violation records
- [ ] Web interface for monitoring
- [ ] Advanced tracking algorithms (DeepSORT)
- [ ] Night vision support
- [ ] Weather condition adaptation

## Troubleshooting

### Tesseract Not Found
```bash
# Set tesseract path in config.json
"tesseract_cmd": "/usr/bin/tesseract"  # Linux
"tesseract_cmd": "C:\\Program Files\\Tesseract-OCR\\tesseract.exe"  # Windows
```

### Poor Detection Quality
- Adjust `min_contour_area` for background subtraction
- Ensure proper lighting conditions
- Consider using YOLO for better accuracy

### Incorrect Speed Readings
- Calibrate `pixel_to_meter` ratio
- Verify `fps` matches actual video frame rate

## License

MIT License - Feel free to use and modify for your projects.

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues.

## Acknowledgments

- OpenCV for computer vision capabilities
- YOLO (You Only Look Once) for object detection
- Tesseract OCR for text recognition

---

**Note**: This system is for educational and research purposes. For production deployment in real traffic scenarios, additional testing, validation, and compliance with local regulations are required.
