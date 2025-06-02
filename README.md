# Secure-Aider
A Docker-based solution for running Aider in a sandboxed environment.

## Features
*   **Sandboxed Execution**: Runs Aider within a Docker container, isolating it from your host system and ensuring dependencies don't conflict with your system.
*   **Version Control Integration**: Easily configure Git user details for commits made by Aider, ensuring proper attribution.
*   **Centralized Configuration**: Manages Aider's configuration (`aider-config.yaml`) and environment variables (`aider-env`) within the `Secure-Aider` directory, making it easy to manage and update.

## Setup

1.  **Clone the Repository**:
    First, clone the `Secure-Aider` repository to your local machine. If you haven't already, you can do so using Git:

    ```bash
    # Clone repository
    git clone https://github.com/nnxmms/Secure-Aider
    
    # Move into cloned repository
    cd Secure-Aider
    ```
    (If you already have the directory, simply navigate to it.)

2.  **Configure Aider Settings**:
    *   **`aider-config.yaml`**: This file contains Aider's main configuration. Review and adjust it as needed.
        *   `model`: Specifies the AI model to use (e.g., `gemini/gemini-2.5-flash-preview-05-20`).
        *   `dark-mode`: Set to `true` for a dark terminal background.
        *   `code-theme`: Sets the syntax highlighting theme for code blocks.
        *   `auto-commits`: Controls whether Aider automatically commits changes after each interaction.

    *   **`aider-env`**: This file holds sensitive environment variables, primarily your LLM API key. **It is crucial that you do not commit this file to public repositories.**
        *   `GEMINI_API_KEY`: Replace the placeholder value with your actual Gemini API key.

3.  **Add `saider` Function to Your Shell**:
    To simplify running Aider within the Docker container, add the following function to your shell's configuration file (e.g., `~/.zshrc`, `~/.bashrc`, `~/.profile`).

    ```bash
    saider() {
        # Configure Git user details for commits made by Aider within the container.
        # Replace "Your Name" and "your.email@example.com" with your actual Git user name and email.
        # These settings are temporary for the current Docker run session.
        git config user.name "Your Name"
        git config user.email "your.email@example.com"

        # Run Aider in a Docker container
        docker run --rm -it \
            --user $(id -u):$(id -g) \
            --volume "$(pwd):/app" \
            --volume "/path/to/Secure-Aider:/aider-config" \
            paulgauthier/aider-full \
            --config /aider-config/aider-config.yaml \
            --env-file /aider-config/aider-env \
            "$@"
    }
    ```
    **Important**:
    *   Replace `/path/to/Secure-Aider` with the **absolute path** to the directory where you cloned this `Secure-Aider` repository. For example, if you cloned it to `/home/user/Secure-Aider`, use that path.
    *   Replace `"Your Name"` and `"your.email@example.com"` with your desired Git user name and email. These will be used by Aider when it makes commits to your project.
    *   `paulgauthier/aider-full` is the Docker image used, which contains Aider and its dependencies.
    *   `--user $(id -u):$(id -g)` ensures that files created by Aider inside the container have the correct ownership on your host system.
    *   `--volume "$(pwd):/app"` mounts your current working directory (where you run `saider`) into the container as `/app`, allowing Aider to access your project files.
    *   `--volume "/path/to/Secure-Aider:/aider-config"` mounts this `Secure-Aider` directory into the container as `/aider-config`, making your `aider-config.yaml` and `aider-env` files accessible to Aider.

4.  **Reload Your Shell Configuration**:
    After adding the function, reload your shell configuration to make the `saider` command available in your current terminal session:

    ```bash
    source ~/.zshrc # or ~/.bashrc, ~/.profile, etc., depending on your shell
    ```

## Usage

Once set up, you can run Aider by simply typing `saider` in your terminal from within any Git repository you want Aider to work on:

