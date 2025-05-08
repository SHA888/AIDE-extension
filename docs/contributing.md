# Contributing to AIDE-extension

We welcome contributions to the AIDE-extension project! Whether you're interested in fixing bugs, adding new features, improving documentation, or helping with testing, your input is valuable. This guide provides information on how to contribute effectively.

## How to Contribute

We primarily use GitHub for managing contributions. The general workflow is as follows:

1.  **Fork the Repository:**
    Start by forking the `AIDE-extension` repository on GitHub to your own account.

2.  **Clone Your Fork:**
    Clone your forked repository to your local machine.
    ```bash
    git clone https://github.com/YOUR_USERNAME/AIDE-extension.git
    cd AIDE-extension
    ```

3.  **Create a New Branch:**
    Create a descriptive branch for your changes. This helps keep contributions organized.
    ```bash
    git checkout -b feature/your-awesome-feature # For new features
    git checkout -b fix/issue-123-description # For bug fixes
    ```

4.  **Make Your Changes:**
    -   Implement your feature or fix the bug. Ensure your code adheres to the project's coding style and conventions (see below).
    -   If adding new functionality, consider adding relevant tests.
    -   Update documentation if your changes affect how users or developers interact with the extension.

5.  **Set Up Your Environment:**
    Follow the instructions in `docs/getting_started.md` to set up the necessary R, Python, and Rust environments. Ensure you can build and test the extension locally.

6.  **Test Your Changes:**
    -   Thoroughly test your changes to ensure they work as expected and do not introduce regressions.
    -   If you've added Python components, ensure they integrate correctly with R via `reticulate` (if applicable to your changes) and that Rust components compile and function as intended.

7.  **Commit Your Changes:**
    Write clear and concise commit messages. We generally follow the [Conventional Commits](https://www.conventionalcommits.org/) specification. For example:
    -   `feat: Add support for extracting data from DICOM metadata`
    -   `fix: Correct handling of empty lines in text parser`
    -   `docs: Update Python component setup instructions`
    -   `style: Apply black formatting to Python code`
    -   `refactor: Improve performance of FHIR resource generation`
    -   `test: Add unit tests for new OCR preprocessor`

8.  **Push to Your Fork:**
    Push your changes to your forked repository on GitHub.
    ```bash
    git push origin feature/your-awesome-feature
    ```

9.  **Open a Pull Request (PR):**
    -   Navigate to the original `AIDE-extension` repository on GitHub.
    -   You should see a prompt to create a Pull Request from your recently pushed branch. Click it.
    -   Provide a clear title and a detailed description of your changes in the PR.
        -   Explain the problem your PR solves or the feature it adds.
        -   Describe your solution and any important design decisions.
        -   If your PR addresses an existing issue, link to it (e.g., "Closes #123").
    -   Ensure your PR targets the `main` branch of the `AIDE-extension` repository.

10. **Code Review and Discussion:**
    -   Project maintainers and other contributors will review your PR.
    -   Be prepared to discuss your changes and make further modifications based on feedback.
    -   Automated checks (CI/CD pipeline) might also run on your PR.

11. **Merge:**
    Once your PR is approved and passes all checks, a maintainer will merge it into the `main` branch.

## Development Workflow with Main AIDE Project

Remember that `AIDE-extension` is a submodule of the main `AIDE` project. If your changes in the extension need to be reflected or utilized in the main AIDE R Shiny application, refer to `docs/development_workflow.md` for detailed instructions on how to update the submodule pointer in the parent `AIDE` repository.

## Coding Conventions and Style Guides

To maintain consistency and readability, please adhere to the following (or project-specific established conventions):

-   **Python:**
    -   Follow [PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/).
    -   Use a code formatter like `black` and a linter like `flake8` or `pylint`.
    -   Include docstrings for modules, classes, functions, and methods (e.g., Google style or NumPy style).
-   **Rust:**
    -   Follow the official [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/).
    -   Use `rustfmt` to format your code (usually run via `cargo fmt`).
    -   Use `clippy` for linting (usually run via `cargo clippy`).
    -   Include documentation comments for public APIs.
-   **R:**
    -   Follow a consistent style, such as the [tidyverse style guide](https://style.tidyverse.org/).
    -   Use `styler` for code formatting and `lintr` for linting.
    -   Comment your code clearly.
-   **Markdown (for documentation):**
    -   Use consistent formatting. Consider using a Markdown linter like `markdownlint`.

## Issue Tracking

-   Report bugs or suggest features by creating an issue on the `AIDE-extension` GitHub repository.
-   Before creating a new issue, please check if a similar one already exists.
-   Provide as much detail as possible: steps to reproduce, expected behavior, actual behavior, versions of relevant software, and screenshots if helpful.

## Communication

-   For discussions related to specific issues or PRs, use the comments section on GitHub.
-   For more general discussions or questions, check if the project has a mailing list, forum, or chat channel (details would be in the main project README or contribution guidelines).

## Licensing

By contributing to AIDE-extension, you agree that your contributions will be licensed under its [GPL-3.0 License](license.md).

Thank you for considering contributing to AIDE-extension!
