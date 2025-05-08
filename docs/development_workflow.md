# Development Workflow for AIDE & AIDE-extension

This document outlines the recommended development workflow when working with the main AIDE R project and its `AIDE-extension` submodule. Understanding how to manage changes across both repositories is crucial for smooth collaboration and version control.

## Prerequisites

-   Git installed and configured.
-   Access to both repositories (your fork of AIDE and the `AIDE-extension` repository).

## Initial Setup

1.  **Clone the main AIDE repository with its submodules:**
    This is the recommended way to get both projects set up correctly from the start.
    ```bash
    git clone --recurse-submodules https://github.com/SHA888/AIDE.git
    cd AIDE
    ```
    The `--recurse-submodules` flag ensures that the `AIDE-extension` submodule is also cloned and checked out to the commit specified in the main AIDE repository.

2.  **If you've already cloned AIDE without submodules:**
    If you previously cloned the AIDE repository without the `--recurse-submodules` flag, you need to initialize and update the submodule:
    ```bash
    cd /path/to/AIDE # Navigate to your existing AIDE clone
    git submodule update --init --recursive
    ```
    This command initializes any uninitialized submodules (`--init`) and recursively updates all submodules to their committed state (`--recursive`).

## Making Changes

There are two main scenarios when making changes:

### Scenario 1: Changes Only Within the `AIDE-extension` Submodule

This is for features or fixes that are entirely contained within the extension (e.g., improving a Python script, adding a Rust utility).

1.  **Navigate to the extension directory:**
    From the root of the main `AIDE` repository:
    ```bash
    cd extension
    ```
    You are now in the `AIDE-extension` Git repository.

2.  **Ensure you are on the correct branch (e.g., `main` or a feature branch):**
    ```bash
    git status
    git checkout main # or your feature branch
    git pull # Get the latest changes for the extension
    ```

3.  **Create a new branch for your feature/fix (recommended):**
    ```bash
    git checkout -b feature/my-extension-feature
    ```

4.  **Make your code changes within the `extension` directory.**

5.  **Commit and push your changes to the `AIDE-extension` remote repository:**
    ```bash
    git add .
    git commit -m "feat: Implement awesome feature in extension"
    git push origin feature/my-extension-feature
    ```

6.  **Create a Pull Request (PR)** in the `AIDE-extension` GitHub repository for your changes to be reviewed and merged into its `main` branch.

7.  **Update the main AIDE project (after extension PR is merged):**
    Once the changes are merged into the `main` branch of `AIDE-extension`, the main `AIDE` project needs to be updated to point to this new version of the submodule.
    ```bash
    cd .. # Go back to the root of the AIDE repository
    cd extension # Go into the submodule directory
    git checkout main
    git pull # Pull the latest changes (including your merged PR)
    cd .. # Go back to the root of the AIDE repository
    git add extension # This stages the updated submodule commit
    git commit -m "chore: Update AIDE-extension to latest version (includes #PR_NUMBER_IN_EXTENSION)"
    git push # Push this update to your AIDE fork
    ```
    Now, create a PR in the main AIDE repository if necessary.

### Scenario 2: Changes That Affect Both AIDE and `AIDE-extension`

This is for features where code changes are needed in both the main AIDE R project and the `AIDE-extension` (e.g., adding a new UI element in AIDE that calls a new Python function in the extension).

1.  **First, make and commit changes in the `AIDE-extension` submodule:**
    Follow steps 1-6 from Scenario 1 to make your changes in the `AIDE-extension`, get them reviewed, and merged into its `main` branch.

2.  **Update your local `AIDE-extension` submodule to the new commit:**
    ```bash
    cd extension
    git checkout main
    git pull
    cd ..
    ```
    Your `extension` directory now points to the latest commit from its `main` branch.

3.  **Stage the updated submodule reference in the main `AIDE` repository:**
    ```bash
    git add extension
    ```
    This tells Git that the main AIDE project should now point to the new commit of the `AIDE-extension` submodule.

4.  **Make corresponding changes in the main `AIDE` R project files.**
    (e.g., update `app.R` to call new functions, modify R wrapper scripts).

5.  **Commit all changes in the main `AIDE` repository:**
    ```bash
    git add . # Add any changed R files
    git commit -m "feat: Implement new feature X using updated extension (AIDE-extension #PR_NUMBER)"
    ```

6.  **Push changes to your `AIDE` fork and create a Pull Request.**

## Keeping Your Local Repositories in Sync

1.  **Update the main `AIDE` project:**
    ```bash
    cd /path/to/AIDE
    git pull
    ```

2.  **Update the `AIDE-extension` submodule:**
    After pulling changes in the main project, the submodule reference might have been updated. To get the actual submodule code:
    ```bash
    git submodule update --init --recursive # Fetches the correct commit for the submodule
    ```
    Alternatively, if you want to update the submodule to the latest version on its *remote* branch (e.g., `main`), you can do:
    ```bash
    cd extension
    git checkout main
    git pull
    cd ..
    # Then you would need to commit this change in the main AIDE repo if it differs
    # git add extension
    # git commit -m "chore: Update extension to latest main"
    ```
    However, it's generally safer to rely on the main project's committed submodule version (`git submodule update`).

## Best Practices

-   **Commit submodule changes first:** Always commit and (ideally) merge changes in the submodule (`AIDE-extension`) *before* committing the change to the submodule pointer in the parent repository (`AIDE`).
-   **Clear Commit Messages:** Use clear commit messages that indicate whether the change is in the extension, the main app, or both. Reference PR numbers if applicable.
-   **Branching Strategy:** Use feature branches in both repositories.
-   **Pull Requests:** Use PRs for all changes in both repositories for review.
-   **Regularly update:** Keep both your main project and submodules updated to avoid large merge conflicts.

## Checking Submodule Status

-   `git submodule status`: Shows the currently checked-out commit for each submodule.
-   `git diff --submodule`: Shows differences in submodule commits between your working tree and the index, or between the index and HEAD.
