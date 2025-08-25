# bash-url-watch

A simple, lightweight Bash script to monitor the output of commands (like fetching a URL) for changes. It's designed to be a minimalist tool with few dependencies for basic monitoring tasks.

> Built partly with the help of [Google Gemini](https://gemini.google.com)

### Inspiration

This script is a minimalist, Bash-only tool inspired by the powerful and feature-rich Python-based [urlwatch](https://github.com/thp/urlwatch) by Thomas Perl. If you need more advanced features like various reporters (email, push notifications), complex filters, and extensive job types, `urlwatch` is an excellent choice. `bash-url-watch` aims for simplicity and minimal dependencies.

## Features

- **Monitors Command Output**: Tracks changes in the output of any shell command.
- **Simple YAML Configuration**: Define your jobs in an easy-to-read YAML file.
- **Variable Substitution**: Use placeholders like `%%%VAR_NAME%%%` in commands, defined in an optional `globals.conf` for reusability and security (e.g., for API keys).
- **Reserved Variables**: Access internal script data like `%%%SCRIPT_IDENTITY%%%` (e.g., `bash-url-watch v0.5.0`) for use in commands.
- **Colored Diff**: Shows a clear, colored `diff` when changes are detected. Can optionally use `colordiff` if installed.
- **XDG Compliant**: Follows the XDG Base Directory Specification for configuration and state files.
- **Minimal Dependencies**: Requires only `diff` and `yq`.
- **Summary Report**: Provides a clean summary of what has changed after running.

## Requirements

- `diff` (from `diffutils`)
- [yq](https://github.com/mikefarah/yq/) (a portable command-line YAML processor)
- **Optional**: [colordiff](https://www.colordiff.org/) for enhanced diff highlighting. The script will use it if it's available in your `PATH`.

## Installation

1. Download the script:

```bash
sudo curl -o /usr/local/bin/bash-url-watch https://raw.githubusercontent.com/clove3am/bash-url-watch/main/bash-url-watch
```

2. Make it executable:

```bash
chmod +x /usr/local/bin/bash-url-watch
```

## Configuration

The script uses two configuration files, both located in `~/.config/bash-url-watch/`.

1. **Create the configuration directory:**

    ```bash
    mkdir -p ~/.config/bash-url-watch/
    ```

### Main Config: `urls.yaml`

This is the main file where you define the jobs to be monitored.

1. **Create the configuration file `urls.yaml`:**

    ```bash
    touch ~/.config/bash-url-watch/urls.yaml
    ```

2. **Add your jobs to the file.** The file should contain a list of objects, where each object has a `name` and a `command`.

    **Example `urls.yaml`:**

    ```yaml
    name: Example.com
    command: curl -fsS 'https://example.com'
    ---
    name: Arch Linux News
    command: curl -fsS 'https://archlinux.org/' |  xq -q '.article-content > p'
    ---
    name: Check API with Custom User-Agent
    command: curl -fsSA %%%SCRIPT_IDENTITY%%% %%%MY_API_URL%%%
    ```

### Optional Globals Config: `globals.conf`

This optional file allows you to define variables that can be substituted into the `command` field of your jobs. This is ideal for reusing values or for keeping secrets like API keys out of your main `urls.yaml` file.

1. **Create the globals file `globals.conf`:**

    ```bash
    touch ~/.config/bash-url-watch/globals.conf
    ```

2. **Add your variables in a `KEY="VALUE"` format.**

    **Example `globals.conf`:**

    ```bash
    MY_API_URL="https://api.service.com/v1/status"
    API_KEY="abc123def456-your-secret-token"
    ```

## Usage

The script stores the state (the last known output of each command) in the `~/.local/state/bash-url-watch/` directory.

| Command                                          | Description                                              |
| ------------------------------------------------ | -------------------------------------------------------- |
| `bash-url-watch`                                 | Run all jobs and report on changes.                      |
| `bash-url-watch --list` or `-l`                  | List all configured jobs with their numbers.             |
| `bash-url-watch --check <num>` or `-k <num>`     | Run a specific job by its number from the list.          |
| `bash-url-watch --all` or `-a`                   | Show output for all jobs, even if there are no changes.  |
| `bash-url-watch --no-summary`                    | Hide the final summary report.                           |
| `bash-url-watch --no-color` or `-n`              | Disable color output.                                    |
| `bash-url-watch --clean`                         | Remove old state files for jobs no longer in the config. |
| `bash-url-watch --urls <file>` or `-u <file>`    | Specify a custom path to your `urls.yaml` file.          |
| `bash-url-watch --globals <file>` or `-c <file>` | Specify a custom path to your `globals.conf` file.       |
| `bash-url-watch --completion`                    | Generate the bash completion script.                     |
| `bash-url-watch --help` or `-h`                  | Show the help message.                                   |
| `bash-url-watch --version` or `-v`               | Show the script version.                                 |

### Example Cron Job

To run the script automatically, you can set up a cron job. Using `--no-color` and `--no-summary` is recommended to prevent clutter in your cron logs.

For example, to run it every 30 minutes:

```
*/30 * * * * /usr/local/bin/bash-url-watch --no-color --no-summary
```

## Contributing

Contributions to improve the script are welcome! Please feel free to submit issues or pull requests on the project's repository.

## License

This script is released under the MIT License. See the [LICENSE](LICENSE) file for details.