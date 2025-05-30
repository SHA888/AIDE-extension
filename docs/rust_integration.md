# Rust Integration: Connecting with RustEMR and Jamliquor

The AIDE-extension leverages the performance and safety of Rust for critical backend tasks and integrates with existing open-source Rust projects developed by the UbudCare team: `RustEMR` and `Jamliquor`. This section outlines the integration strategy and data flow.

## Core Philosophy

-   **Leverage Existing Strengths:** Instead of reinventing the wheel, AIDE-extension will utilize the robust capabilities already present in RustEMR (for EMR functionalities and FHIR data management) and Jamliquor (for high-performance text processing and data utilities).
-   **Python as the Bridge:** The primary interaction with these Rust projects will be managed through Python components within AIDE-extension. Python bindings (e.g., using PyO3 or Maturin) will expose Rust functionalities to the Python layer.
-   **FHIR for Interoperability:** Where healthcare data is concerned, FHIR will be the primary standard for data exchange, particularly with RustEMR, which utilizes the [FlixCoder/fhir-sdk](https://github.com/FlixCoder/fhir-sdk).

## Integration with [RustEMR](https://github.com/UbudCare/RustEMR)

RustEMR serves as a foundational Electronic Medical Record system.

### Purpose within AIDE-extension Ecosystem:

-   **FHIR-Compliant Data Storage:** RustEMR can act as a persistent store for clinical data extracted or processed by AIDE-extension, ensuring data is managed in a FHIR-native way.
-   **Source of Structured Clinical Data:** For workflows requiring patient data, AIDE-extension can query RustEMR for existing patient information.
-   **Validation & Conformance:** RustEMR, using its FHIR SDK, can help validate FHIR resources generated by AIDE-extension.

### Integration Points & Data Flow:

1.  **Python to RustEMR (Data Ingestion/Query):
    -   The Python healthcare components in AIDE-extension (specifically `PyFHIR`) will communicate with RustEMR.
    -   This could be via a RESTful API exposed by RustEMR or direct library calls if RustEMR exposes a suitable interface consumable from Python (potentially through Rust bindings called by Python).
    -   **Extracted Data to RustEMR:** When AIDE-extension extracts clinical information and maps it to FHIR resources, these resources can be sent to RustEMR for storage.
    -   **Querying RustEMR:** AIDE-extension might query RustEMR for patient data to contextualize document processing (e.g., fetching a patient's problem list before analyzing a new clinical note).

2.  **FHIR SDK (`FlixCoder/fhir-sdk`):
    -   Both AIDE-extension's Rust components and RustEMR leverage this SDK.
    -   AIDE-extension's Rust layer can use this SDK to construct, parse, and validate FHIR resources before they are passed to the Python layer or sent to RustEMR.

### Example Workflow:

1.  AIDE-extension (Python) processes a scanned clinical document using its OCR (Rust accelerated) and ML models.
2.  Key clinical entities are extracted (e.g., diagnosis, medication).
3.  Python component maps these entities to FHIR `Condition` and `MedicationStatement` resources.
4.  These FHIR resources are potentially validated by a Rust utility function (using `fhir-sdk`).
5.  The validated FHIR resources are sent to RustEMR (via API or direct binding) for storage under the relevant patient record.

## Integration with [Jamliquor](https://github.com/jamliqr/jamliquor)

Jamliquor provides a suite of high-performance data processing tools and utilities in Rust.

### Purpose within AIDE-extension Ecosystem:

-   **Performance-Critical Text Processing:** For tasks like large-scale text cleaning, tokenization, or specialized parsing routines that need to be extremely fast, AIDE-extension can delegate to Jamliquor's modules.
-   **Data Utilities:** Jamliquor might offer utility functions for data manipulation or transformation that can be reused by AIDE-extension's Rust components.

### Integration Points & Data Flow:

1.  **Rust-to-Rust Integration:**
    -   AIDE-extension's own Rust components (e.g., `DocParse` for high-performance parsing) can directly use Jamliquor as a library dependency.
    -   This allows for tight coupling and maximum performance when Rust components in AIDE-extension need functionality provided by Jamliquor.

2.  **Indirectly via Python:**
    -   If a Python component in AIDE-extension requires a text processing task that is best handled by Jamliquor, it would call a Rust function within AIDE-extension, which in turn uses Jamliquor.

### Example Workflow:

1.  AIDE-extension's Python document processor receives a batch of very large, poorly formatted text files.
2.  The Python component calls a dedicated Rust function in AIDE-extension designed for bulk preprocessing.
3.  This Rust function utilizes Jamliquor's libraries for efficient string manipulation, cleaning, and initial tokenization.
4.  The preprocessed text is returned to the Python layer for further AI/ML-based analysis.

## Future Considerations

-   **Shared Crates:** As development progresses, common Rust functionalities between AIDE-extension, RustEMR, and Jamliquor might be refactored into shared Rust crates (libraries) to promote code reuse and consistency.
-   **Cross-Project API Standardization:** Defining clear API boundaries, even for internal Rust-to-Rust library usage, will be important for maintainability.

