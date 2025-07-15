# Unihox-AI-WebMetadata-and-OCR-Extract-Harvester

A robust asynchronous Python pipeline for crawling, downloading, extracting, and structuring metadata from Sanskrit and culturally relevant document repositories. Built to fulfill the requirements of the Data Harvesting & Structuring Assignment with advanced OCR and web scraping features.

# **✨ Features**

- **Smart asynchronous web crawling**
– Handles polite crawling with semaphores and user-agent headers
– Depth-based link traversal to discover documents across subpages

- **File discovery and downloading**
– Supports common formats: PDF, EPUB, DOC, DOCX, TXT
– Only downloads new files (skips already-downloaded ones)

- **Intelligent text extraction**
– Direct PDF text parsing (via PyMuPDF)
– PaddleOCR (English) and Tesseract OCR (Sanskrit) fallback for scanned PDFs
– Automatically handles image-based pages

- **Metadata & checksums**
– Extracts: Title, author, publication year, domain, download URL
– Adds: ISO-8601 scrape timestamp, SHA-256 checksum, document_id (hashed)

- **Structured output**
– One JSON file per document in outputs/ folder
– Normalized metadata and clean text content
– Organized by website domain

**Integration-ready**
– Easily portable: outputs stored in Google Drive (Colab-mounted)
– Can be zipped for export or used in downstream NLP/ML tasks

# **File Structure**
```
metadata-crawler/
|
├── metadata_ocr.py               
├── metadata_ocr_test.ipynb       
├── README.md                     
│
└── sanskritdocs/                 
    ├── downloads/               
    └── outputs/                 
```

# **JSON Record Example**
```
{
  "site": "ayushportal.nic.in",
  "document_id": "03a864aa2e",
  "title": "Document Ayurveda",
  "authors": ["Name Surname"],
  "pub_year": "2020",
  "language": "Sanskrit",
  "download_url": "https://...",
  "scraped_at": "2025-07-15T05:41:28.367003Z",
  "checksum": "a1b2c3...",
  "content": "Extracted or OCR text"
}
```

# **How to Use**
**1. 📦 Install Dependencies (in Google Colab)**
   
   ```
    !pip install aiohttp aiofiles pymupdf paddleocr bs4 nest-asyncio python-magic pytesseract -q
    !sudo apt-get install tesseract-ocr tesseract-ocr-san tesseract-ocr-hin libmagic1 -y
    !wget https://github.com/tesseract-ocr/tessdata/raw/main/san.traineddata -P /usr/share/tesseract-ocr/4.00/tessdata/
   ```
**2. 📂 Mount Google Drive**

Ensure your Drive is mounted to store output files:
```
from google.colab import drive
drive.mount('/content/drive')
```
**3. 🚀 Run the Script**

You can execute the full workflow using:
```
await SanskritCrawler().run()
```
This will:

- Crawl the configured websites (starting from BASE_URLS)

- Identify downloadable files (PDF, DOCX, EPUB, TXT)

- Download them to /content/drive/MyDrive/sanskritdocs/downloads

- Extract text using direct parsing or OCR (PaddleOCR for English, Tesseract for Sanskrit fallback)

- Extract metadata (title, author, publication year, checksum, etc.)

- Save each document’s info as a structured JSON in /sanskritdocs/outputs

**4. 📝 Output**  

- One JSON file per document with extracted metadata + content

- Each JSON is named using a hashed document ID (e.g., a82f93de12.json)

- Downloads and outputs are saved inside your mounted Google Drive folder

**5. 🛠️ Notes & Best Practices**

- Polite crawling: Uses a semaphore to limit concurrency (default: 2 concurrent downloads)

- PDF extraction first attempts text parsing, then OCR if needed

- OCR strategy:

   - PaddleOCR used for English/Hindi (lightweight, efficient)

   - Tesseract used as fallback for Sanskrit (lang='san')

- Robust error handling: Skips documents that fail to download or parse

- Output is structured and consistent for downstream indexing or analysis

- You can zip the downloads and outputs directories for submission or backup
