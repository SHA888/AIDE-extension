# R Integration with AIDE-extension

This document outlines how the AIDE-extension, primarily built with Python and Rust, integrates with the core AIDE R application. The R `reticulate` package is the cornerstone of this integration, enabling R to call Python functions seamlessly.

## Core Principles

-   **Leverage `reticulate`:** The `reticulate` package provides a comprehensive toolkit for R-Python interoperability. It handles Python session management, object conversion between R and Python, and calling Python scripts and functions.
-   **R as the Orchestrator (for UI-driven workflows):** In many scenarios, the AIDE R Shiny application will remain the primary user interface. R code will orchestrate calls to the Python components of the extension to perform advanced processing tasks initiated by the user.
-   **Modular R Wrappers:** A set of R wrapper functions will be developed to abstract the `reticulate` calls, providing a clean and R-idiomatic interface to the Python functionalities of the extension.

## Key R Integration Components

### 1. R Wrapper Functions (`r/extension_interface.R` - conceptual)

-   **Purpose:** To provide simple R functions that internally handle the `reticulate` calls to Python modules in the extension.
-   **Examples:**
    -   `aide_ext_process_document(file_path, output_format = "fhir_bundle")`: An R function that takes a file path, calls the Python document processing pipeline, and returns, for example, a FHIR bundle (perhaps as a JSON string or an R list).
    -   `aide_ext_extract_entities(text_vector, model_name = "biobert")`: An R function that sends text to a Python NER model and returns extracted entities as an R data frame.
    -   `aide_ext_configure_python(python_path = NULL, env_name = "aide_env")`: An R function to help users specify or set up the Python environment for `reticulate`.
-   **Features:**
    -   Input validation for R arguments.
    -   Conversion of R data types (vectors, lists, data frames) to appropriate Python types (lists, dicts, Pandas DataFrames) before calling Python.
    -   Conversion of Python return values back to R-friendly data structures.
    -   Error handling: Capturing Python exceptions and translating them into R errors or warnings.

### 2. Python Environment Management (within R)

-   **Using `reticulate` functions:**
    -   `reticulate::use_python()`: To specify a particular Python interpreter.
    -   `reticulate::use_virtualenv()` or `reticulate::use_condaenv()`: To activate a specific Python virtual environment or Conda environment where the extension's Python dependencies are installed.
    -   `reticulate::py_install()`: Potentially to help users install Python dependencies if a dedicated setup script is not used.
-   **Documentation:** Clear instructions will be provided on how to set up the Python environment that `reticulate` will use, ensuring all necessary Python packages for the extension are available.

### 3. Configuration Management (`r/config.R` - conceptual)

-   **Purpose:** To manage settings related to the Python integration, such as paths to Python scripts, default model names, or API keys if needed.
-   **Mechanism:** R scripts that load configuration from files (e.g., YAML or JSON) or environment variables, making these settings available to the R wrapper functions.

### 4. UI Extensions in AIDE Shiny App

-   **New UI Elements:** The core AIDE Shiny app (`app.R`) will be extended with new UI inputs and outputs to expose the functionality provided by the AIDE-extension.
    -   *Example:* A new tab or section for advanced document processing with options to select different OCR engines or ML models (controlled by Python).
-   **Displaying Results:** R/Shiny code will be updated to display results returned from the Python components (e.g., visualizing extracted FHIR data, displaying tables of entities).
-   **Progress Indication:** For long-running Python tasks, `reticulate` and Shiny's asynchronous capabilities might be used to provide progress updates in the UI.

## Data Flow: R to Python and Back

1.  **User Action in Shiny UI:** A user interacts with a new UI element in the AIDE Shiny app (e.g., clicks a button to process a document with advanced features).
2.  **R Event Handler:** The corresponding R event handler in `app.R` is triggered.
3.  **Call to R Wrapper Function:** The R event handler calls one of the `aide_ext_*` wrapper functions.
4.  **`reticulate` in Action:**
    -   The R wrapper function uses `reticulate::py_run_file()` or `reticulate::import()` and then calls specific Python functions.
    -   R data (e.g., file path, text) is passed as arguments. `reticulate` converts these to Python types.
5.  **Python Execution:** The Python script/function in AIDE-extension executes (processing the document, running an ML model, etc.). This might involve Python calling Rust components internally.
6.  **Return to R:**
    -   The Python function returns a result (e.g., a Python dictionary, a Pandas DataFrame).
    -   `reticulate` converts the Python object back into an R object (e.g., an R list, data frame).
7.  **R Processes Result:** The R wrapper function receives the R object and may perform additional transformations.
8.  **Update Shiny UI:** The R event handler uses the result to update the Shiny UI (e.g., display a table, show a plot, present extracted text).

## Error Handling

-   Python exceptions will be caught by `reticulate` and can be handled in the R wrapper functions.
-   Efforts will be made to provide informative error messages to the R user, indicating whether the error originated in R, Python, or Rust.

## Setup and Dependencies

-   Users will need to have R and a compatible Python version installed.
-   A specific Python environment (virtualenv or Conda) with all the AIDE-extension's Python dependencies (like `pymupdf`, `transformers`, etc.) will be required.
-   The `reticulate` R package must be installed in the R environment.
-   Clear setup instructions will be provided in `docs/getting_started.md`.
