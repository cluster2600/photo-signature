# photo-signature

A set of Python and AppleScript tools to watermark and sign photographs with a visible handwritten-style signature and/or EXIF metadata.

Designed for photographers who export images from **Apple Photos** and want to add a personal signature before sharing or printing.

---

## Features

- **Visual watermark** (`incrust.py`): Adds a white border frame around the image and overlays a handwritten-style signature (using `MarkerFelt` font) in the bottom-right corner.
- **EXIF metadata signing** (`import_subprocess.py`): Embeds Artist, Copyright, and UserComment fields into image EXIF data using `exiftool`.
- **Multi-format support**: JPG, JPEG, PNG, HEIC, and DNG (RAW) files.
- **DNG/RAW support**: Uses `rawpy` to decode RAW files, converts to RGB, then applies watermark.
- **AppleScript integration** (`get_photos_paths.applescript.scpt`): Retrieves file paths of RAW photos from a specific Apple Photos album.

---

## Requirements

### Python packages

```bash
pip install Pillow rawpy pillow-heif
```

| Package | Purpose |
|---|---|
| `Pillow` | Image processing, watermarking, border frame |
| `rawpy` | Opening DNG/RAW files |
| `pillow-heif` | HEIC/HEIF format support |

### System tools

- **`exiftool`** (for EXIF signing): `brew install exiftool`
- **macOS** with the `MarkerFelt.ttc` font (pre-installed on macOS)
- **Apple Photos** (for the AppleScript path helper, optional)

---

## Files

| File | Language | Purpose |
|---|---|---|
| `incrust.py` | Python | Adds white border + handwritten signature watermark to images |
| `import_subprocess.py` | Python | Writes signature into image EXIF metadata via `exiftool` |
| `get_photos_paths.applescript.scpt` | AppleScript | Gets file paths of RAW photos from a named Photos album |
| `path.scpt` | AppleScript | Simplified version for extracting photo paths from Photos |

---

## Usage

### 1. Add a visual watermark and border frame

```bash
python incrust.py
```

Edit the bottom of `incrust.py` to configure:

```python
export_dir = "/Volumes/backup"   # Folder containing your exported images
signature  = "Maxime Grenu"      # Your signature text
process_images(export_dir, signature)
```

What it does:
- Opens each JPG, JPEG, PNG, HEIC file in the directory
- Adds a **2 cm white border** around the image (calculated at 300 DPI)
- Overlays the signature in **black MarkerFelt font** in the bottom-right corner
- Saves the result back to the same file (in-place)
- For **DNG/RAW** files: converts to JPEG and saves as `<original>_watermarked.jpg`

### 2. Write signature into EXIF metadata

```bash
python import_subprocess.py
```

Edit the bottom of `import_subprocess.py` to configure:

```python
export_dir = "/Volumes/backup"   # Folder containing your images
signature  = "Maxime Grenu"      # Your name / copyright text
process_exif(export_dir, signature)
```

What it does:
- Runs `exiftool` on each JPG, JPEG, HEIC, or DNG file
- Sets the following EXIF fields: `Artist`, `Copyright`, `UserComment`
- Overwrites the original file in-place (`-overwrite_original`)

> ⚠️ **Make sure you have a backup before running these scripts.** Both tools modify files in-place.

### 3. Get photo paths from Apple Photos (optional)

Open `get_photos_paths.applescript.scpt` in Script Editor or run:

```bash
osascript get_photos_paths.applescript.scpt
```

This targets the album named **"Djanet 2025"** and returns the file paths of RAW photos in it. Edit the album name in the script to match your own album.

---

## Configuration

### Changing the border size

In `incrust.py`, modify `border_cm` (default: `2`):

```python
def add_watermark_and_frame(file_path, signature, border_cm=2, target_dpi=300):
```

### Changing the font

The script uses `MarkerFelt.ttc` (pre-installed on macOS). To use a different font, change:

```python
font = ImageFont.truetype("/System/Library/Fonts/Supplemental/MarkerFelt.ttc", 50)
```

to any `.ttf` or `.ttc` font path on your system.

### Changing font size

Modify the size argument (default: `50`):

```python
font = ImageFont.truetype("...", 50)   # increase for larger images / print
```

---

## Workflow Example

```
1. Export photos from Apple Photos to /Volumes/backup
2. (Optional) Run get_photos_paths.applescript.scpt to list RAW file paths
3. Run import_subprocess.py to sign EXIF metadata
4. Run incrust.py to add visible border + signature watermark
5. Share or print the signed images
```

---

## Notes

- Orientation is **not auto-corrected**. If your images are rotated, correct them manually before running the scripts.
- HEIC support requires `pillow-heif` which registers the opener automatically.
- The DNG output is saved as a new JPEG (`_watermarked.jpg`) — the original DNG is not modified.

---

## Author

**Maxime Grenu** ([cluster2600](https://github.com/cluster2600))

---

## License

MIT — see [LICENSE](LICENSE) file or use freely with attribution.

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-improvement`)
3. Commit your changes (`git commit -m 'feat: describe what you did'`)
4. Push to your branch and open a Pull Request
