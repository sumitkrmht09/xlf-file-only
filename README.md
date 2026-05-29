# FrameMaker XLIFF-Only Translation Studio

An automated translation and graphic OCR replacement pipeline for Adobe FrameMaker documents exported to XLIFF format (`.xlf` / `.xliff`).

Unlike other versions that require uploading a secondary graphics ZIP file, **this application only requires the user to upload the XLIFF file**. The system automatically parses the XLIFF document, identifies the referenced graphic/PDF filenames, locates them inside the repository or local filesystem, translates both the XLIFF text and the text within the images, updates internal references, and outputs a double-nested ZIP file.

---

## 🏗️ Architecture & Component Overview

* **Streamlit UI (`app.py`)**: A premium dark-mode dashboard with real-time logs, progress bars, and stats metrics.
* **XLIFF Translation Engine (`translate_xliff_openai_2.py`)**: Parses XLIFF tags, classification engine for safety/standard segments, handles bulk OpenAI API translation batches, and rebuilds the XLIFF XML structure.
* **Graphics OCR Locator (`image_ocr_translator.py`)**: Decodes MIF binary blobs, maps reference paths, walks the local environment (or `/app` directory inside the Render container) to find files by name, performs text extraction/OCR/translation, and overwrites drawing layers with translated text.
* **Bundled Fonts (`arial.ttf`/`arialbd.ttf`)**: Bundled inside the application to guarantee cross-platform support (Docker, Windows, Render Linux) for text drawing without depending on system-level fonts.

---

## 📂 Double-Nested ZIP Output Layout

To ensure Adobe FrameMaker can resolve relative paths natively when you extract the output deliverable, the ZIP file is built with both single and double-nested folder mappings:

```text
translated_de_Title.zip/
 ├── translated_de_Title/              <-- Single-nested structure
 │    ├── graphics/
 │    └── text_conversion_file/
 └── translated_de_Title/              <-- Double-nested structure
      └── translated_de_Title/
           ├── graphics/
           └── text_conversion_file/
```
Extracting this ZIP automatically resolves relative paths like `../translated_de_Title/graphics/image.png` without any custom post-processing scripts.

---

## 🚀 How to Run Locally

### 1. Prerequisites
Create a `.env` file in the root directory:
```env
OPENAI_API_KEY=your-api-key-here
```

### 2. Set Up Virtual Environment & Dependencies
```bash
# Create and activate environment
python -m venv .venv
.venv\Scripts\activate   # Windows

# Install dependencies
pip install -r requirements.txt
```

### 3. Launch Streamlit
```bash
streamlit run app.py
```
Open [http://localhost:8501](http://localhost:8501) in your browser.

---

## 🌐 Deploy to Render

Deploy this application to Render in **one click**:

1. Click the following button:
   👉 **[Deploy to Render](https://render.com/deploy?repo=https://github.com/sumitkrmht09/xlf-file-only)**
2. Input your `OPENAI_API_KEY`.
3. Render will spin up the Python runtime, build dependencies, and launch the Streamlit server automatically.
