# Python Components in AIDE-extension

The Python components form the core of the AIDE-extension's advanced data processing and AI/ML capabilities. They bridge the R-based AIDE environment with powerful Python libraries and custom-developed Rust modules. This section details the key Python modules and their functionalities.

## Core Responsibilities

-   **Advanced Document Processing:** Handling diverse document formats beyond standard PDFs, including scanned images, DOCX files, and HTML content.
-   **AI/ML Integration:** Leveraging state-of-the-art machine learning models for tasks like Named Entity Recognition (NER), document layout analysis, semantic search, and data extraction.
-   **Healthcare Data Specialization:** Implementing logic for processing medical documents, mapping extracted data to FHIR resources, and ensuring compliance with healthcare standards.
-   **Orchestration:** Managing the flow of data between R, Python, and Rust components.

## Key Python Modules (Conceptual)

*(These will likely be organized into subdirectories within the `python/` folder of the extension, e.g., `python/document_processing/`, `python/ml_models/`, `python/fhir_handler/`)*

### 1. Document Processing (`document_processor.py`)

-   **Multi-Format Document Handling:**
    -   Utilizes libraries like `pymupdf` (MuPDF) for robust PDF parsing, text extraction, image extraction, and metadata retrieval.
    -   Integrates `python-docx` for DOCX file processing.
    -   Uses `BeautifulSoup` or `lxml` for HTML content parsing.
    -   Handles image formats (PNG, JPG, TIFF) for OCR.
-   **Enhanced PDF Extraction:**
    -   Advanced table detection and extraction from PDFs.
    -   Logical structure recovery (headings, paragraphs, lists).
    -   Extraction of embedded images and their captions.
-   **OCR Capabilities:**
    -   Interfaces with Tesseract OCR via `pytesseract`.
    -   Includes pre-processing steps for images (e.g., deskewing, binarization) to improve OCR accuracy, potentially calling Rust functions for performance.
    -   Post-processing of OCR output (e.g., spell correction, noise removal).
-   **Intelligent Document Chunking:**
    -   Methods for splitting large documents into meaningful segments for LLM processing or ML model input.
    -   Considers semantic boundaries, section breaks, and token limits.

### 2. AI/ML Integration (`ml_service.py`)

-   **Named Entity Recognition (NER):**
    -   Integration with pre-trained NER models (e.g., from spaCy, Hugging Face Transformers) for general entities (persons, organizations, dates).
    -   Support for fine-tuning or using domain-specific NER models for healthcare (e.g., MedSpacy, BioBERT) or scientific entities.
-   **Document Layout Analysis & Structure Detection:**
    -   Models to understand the visual and structural layout of documents (e.g., identifying headers, footers, columns, figures, tables).
    -   Can leverage libraries like `LayoutParser` or custom computer vision models.
-   **Semantic Search & Text Similarity:**
    -   Using sentence transformers or other embedding models to find semantically similar text passages or documents.
-   **Advanced Semantic Chunking & Summarization:**
    -   Employing LLMs or abstractive/extractive summarization models for creating concise summaries or more context-aware chunks.
-   **Confidence Scoring:**
    -   Developing or integrating mechanisms to provide confidence scores for extracted data, indicating the reliability of the extraction.
-   **Model Management:**
    -   Utilities for loading, managing, and versioning ML models.

### 3. Healthcare Data Extraction & FHIR Handling (`fhir_processor.py`)

-   **Medical Document Structure Recognition:**
    -   Specialized logic to understand common structures in medical documents (e.g., SOAP notes, discharge summaries, pathology reports).
-   **Extraction to FHIR-Compliant Formats:**
    -   Mapping extracted clinical information (e.g., diagnoses, medications, procedures, lab values) to the appropriate FHIR resources (Condition, MedicationStatement, Procedure, Observation, etc.).
    -   Utilizes FHIR data models (e.g., using Python FHIR models like `fhir.resources` from the `fhir.py` library or custom classes).
-   **Validation Against FHIR Standards & Profiles:**
    -   Ensuring that generated FHIR resources are valid according to base FHIR specifications and any relevant implementation guide profiles.
    -   May call Rust FHIR SDK utilities for high-performance validation.
-   **Terminology Service Integration (Conceptual):**
    -   Future capability to connect to terminology services for mapping extracted terms to SNOMED CT, LOINC, RxNorm.
-   **Interaction with RustEMR:**
    -   Handles API calls or other forms of communication to send/receive FHIR data to/from RustEMR.

### 4. Rust Bridge (`rust_bindings.py` - a conceptual wrapper)

-   While not a direct processing module, this represents the Python interface to the Rust components.
-   Uses libraries like `PyO3` to expose Rust functions (e.g., for high-speed parsing, OCR acceleration) as Python-callable functions.
-   Manages data type conversions between Python and Rust.

## Dependencies

Key Python libraries will include (but are not limited to):

-   `reticulate` (for R integration, managed on the R side)
-   `pymupdf`
-   `pytesseract`
-   `python-docx`
-   `beautifulsoup4` / `lxml`
-   `spacy` / `transformers` (Hugging Face)
-   `scikit-learn`
-   `pandas` / `numpy`
-   `fhir.py` (or similar FHIR resource library)
-   `requests` (for API interactions)

## Error Handling and Logging

Robust error handling and comprehensive logging will be implemented across all Python modules to ensure reliability and aid in debugging, with logs potentially surfaced to the R/Shiny UI.
