# Getting Started with AIDE & AIDE-extension

This guide will walk you through setting up the AIDE project and its `AIDE-extension` submodule on your local machine. This setup will allow you to run the AIDE Shiny application and utilize the enhanced functionalities provided by the Python and Rust components in the extension.

## Prerequisites

Ensure you have the following software installed on your system:

1.  **R and RStudio:**
    -   R (version 4.0 or higher recommended).
    -   RStudio IDE (latest version recommended).
2.  **Python:**
    -   Python (version 3.8 or higher recommended).
    -   `pip` (Python package installer).
    -   `venv` (for creating virtual environments, usually included with Python).
3.  **Rust (for development of Rust components):**
    -   Install Rust via `rustup` from [https://rustup.rs/](https://rustup.rs/). This will also install `cargo` (Rust's package manager and build tool).
4.  **Git:**
    -   Version control system for cloning the repositories.
5.  **Tesseract OCR Engine (for OCR capabilities):**
    -   Installation instructions vary by OS. See the [Tesseract documentation](https://tesseract-ocr.github.io/tessdoc/Installation.html).
    -   Ensure the Tesseract language data files for your desired languages are installed.

## Setup Instructions

### 1. Clone the Main AIDE Repository

If you haven't already, clone the main AIDE repository. It's crucial to use the `--recurse-submodules` flag to ensure the `AIDE-extension` submodule is cloned along with it.

```bash
git clone --recurse-submodules https://github.com/SHA888/AIDE.git
cd AIDE
```

If you cloned AIDE previously without the submodules, navigate to your AIDE directory and run:

```bash
git submodule update --init --recursive
```

This will initialize and fetch the `AIDE-extension` submodule, placing it in the `extension/` directory within your AIDE project.

### 2. Set Up the R Environment

1.  **Open the AIDE Project in RStudio:**
    -   Launch RStudio.
    -   Go to `File > Open Project...` and navigate to the cloned `AIDE` directory. Select the `.Rproj` file (e.g., `AIDE.Rproj`).

2.  **Install Required R Packages:**
    -   The AIDE project likely has a list of required R packages (e.g., `shiny`, `reticulate`, `jsonlite`, `tidyverse`, etc.). Check for a script like `install_packages.R` or look at the libraries loaded in `app.R` or `global.R`.
    -   You can typically install them using the R console in RStudio:
        ```R
        # Example packages (replace with actual list from AIDE project)
        install.packages(c("shiny", "reticulate", "jsonlite", "dplyr", "readr"))
        ```
    -   Ensure `reticulate` is installed, as it's vital for Python integration.

### 3. Set Up the Python Environment for `AIDE-extension`

The `AIDE-extension` uses Python for its core processing. A dedicated Python virtual environment is highly recommended.

1.  **Navigate to the `AIDE-extension` directory:**
    From your terminal, if you are in the `AIDE` root directory:
    ```bash
    cd extension
    ```

2.  **Create a Python Virtual Environment:**
    ```bash
    python -m venv .venv
    ```
    This creates a virtual environment named `.venv` inside the `extension` directory.

3.  **Activate the Virtual Environment:**
    -   On macOS and Linux:
        ```bash
        source .venv/bin/activate
        ```
    -   On Windows:
        ```bash
        .venv\Scripts\activate
        ```
    Your terminal prompt should now indicate that the `.venv` environment is active.

4.  **Install Python Dependencies:**
    The `AIDE-extension` should have a `requirements.txt` file listing its Python dependencies.
    ```bash
    pip install -r requirements.txt
    ```
    This will install packages like `pymupdf`, `pytesseract`, `flask` (if a Python-based API is used), `transformers`, `spacy`, etc., into the virtual environment.
    *Note: If there isn't a `requirements.txt` yet, you will need to create one based on the Python components documentation (`docs/python_components.md`).*

### 4. Configure `reticulate` in R

R needs to know which Python environment to use for the extension.

1.  **Inform `reticulate` about the Python environment:**
    You can do this in your R session or by adding a line to your `.Rprofile` file in the AIDE project directory, or within the AIDE `global.R` or `app.R` script before any `reticulate` functions are called.

    Add the following R code (adjust the path if necessary):
    ```R
    # In your R script (e.g., global.R or at the start of app.R)
    # This tells reticulate to use the virtual environment within the extension subdirectory
    Sys.setenv(RETICULATE_PYTHON = "extension/.venv/bin/python") # For Linux/macOS
    # For Windows, it would be: Sys.setenv(RETICULATE_PYTHON = "extension/.venv/Scripts/python.exe")

    # Alternatively, more robustly using reticulate functions:
    library(reticulate)
    if (Sys.info()["sysname"] == "Windows") {
      use_virtualenv("extension/.venv", required = TRUE)
    } else {
      use_virtualenv("extension/.venv", required = TRUE) # Path is relative to project root
    }
    ```
    Using `use_virtualenv("extension/.venv", required = TRUE)` is often more robust as `reticulate` can better locate the environment. Ensure the path is relative to the AIDE project root if `extension` is a direct subdirectory.

2.  **Verify Python Configuration in R:**
    In the R console (after the above setup):
    ```R
    library(reticulate)
    py_config()
    ```
    This should show that `reticulate` is pointing to the Python interpreter within your `extension/.venv` virtual environment and list the installed Python packages.

### 5. Build Rust Components (If Necessary)

If the `AIDE-extension` includes Rust components that need to be compiled (e.g., for Python bindings via PyO3/Maturin):

1.  **Navigate to the Rust project directory within the extension:**
    This might be `extension/rust/` or `extension/src-rust/`, etc. Check the `AIDE-extension` structure.
    ```bash
    cd extension/rust_module # Example path
    ```

2.  **Build the Rust project:**
    Typically, this involves using `cargo` with `maturin` if building Python wheels.
    ```bash
    # If using maturin for a Python package
    maturin develop # Or `maturin build --release` for a release build
    ```
    Follow the specific build instructions provided in `AIDE-extension/docs/rust_integration.md` or its own README.
    After a successful build, the compiled Rust library should be accessible to the Python components.

## Running the AIDE Application

1.  **Ensure the Python virtual environment is active** if you are running Python scripts directly or if R/`reticulate` isn't fully managing the environment activation for its session.
2.  **Launch the Shiny App:**
    -   Open `app.R` (or the main Shiny app file) in RStudio.
    -   Click the "Run App" button in RStudio, or run `shiny::runApp()` in the R console from the AIDE project directory.

3.  **Test Extension Functionality:**
    -   Navigate to the parts of the AIDE application that utilize the new features from the `AIDE-extension`.
    -   Verify that data is being passed correctly between R and Python, and that the Python/Rust components are executing as expected.
    -   Check the R console and any Python logs for errors.

## Troubleshooting

-   **`reticulate` Python Version Mismatch:** Ensure `py_config()` shows the correct Python environment. If not, revisit the `reticulate` configuration steps.
-   **Python Package Not Found:** Make sure all dependencies in `extension/requirements.txt` are installed in the active virtual environment.
-   **Tesseract Not Found/Working:** Verify Tesseract installation and that it's in your system's PATH. Test `pytesseract` from a Python script within the `.venv`.
-   **Rust Compilation Errors:** Check Rust installation, `Cargo.toml` configurations, and any error messages from `cargo build` or `maturin`.
-   **Submodule Issues:** If `extension/` is empty or outdated, re-run `git submodule update --init --recursive` in the AIDE root directory.

Refer to the specific documentation within the `AIDE-extension` for more detailed setup or troubleshooting steps related to its internal components.
