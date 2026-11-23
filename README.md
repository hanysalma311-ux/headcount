# Head Counter (ESP32-CAM + YOLOv8)

Counts how many people are in a room from a 240x240 camera image, with a live web page.

## Files
| File | What it does |
|---|---|
| `web_count3.py` | Final live counter (web page at http://localhost:5000, CSV log) |
| `voc_to_yolo.py` | Converts SCUT-HEAD (Pascal VOC XML) labels to YOLO format |
| `test_image.py` | Test the model on one image: `python test_image.py image.jpg` |
| `cam_test.py` | Check that the laptop webcam works |
| `head240.pt` | Trained model (YOLOv8n, one class: head) |

## Run
```
pip install -r requirements.txt
python web_count3.py --always        # test any time of day
python web_count3.py                 # normal run (counts only 09:00-15:00)
```
Open http://localhost:5000. Green box = confirmed head, gray = candidate, yellow = briefly lost, red = ignored zone.

## Use the ESP32-CAM
In `web_count3.py` set `SOURCE = "http://<ESP32_IP>:81/stream"` (or `.../capture`).
On the ESP32 set the frame size to 240x240 (`FRAMESIZE_240X240`).

## Remove fixed false detections ("ghosts")
With the room EMPTY run once: `python web_count3.py --calibrate`.
It saves `ignore_zones.json`; those spots are ignored afterwards. Delete the file to reset.

## Training (Google Colab, T4 GPU)
1. `python voc_to_yolo.py` inside the SCUT-HEAD Part A folder, zip `yolo_data`.
2. Train YOLOv8n on the data, then fine-tune on images downscaled to 240 px (this raised mAP50 from 0.786 to 0.862 on 240 px images).
3. Best confidence for counting on the SCUT-HEAD test set was about 0.2 (count error about 2 heads out of 34 per image).

Dataset: SCUT-HEAD (academic research use only) https://github.com/HCIILAB/SCUT-HEAD-Dataset-Release .
/هىن
