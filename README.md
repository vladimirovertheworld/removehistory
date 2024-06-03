Here's a `README.md` for your script, explaining its purpose, usage, and functionality:

```markdown
# Shell History Backup and Disable Script

This script is designed to back up and disable the command history logging for various shell environments including Fish, Bash, Zsh, Tcsh, and Sh. It performs the following actions:

1. Creates a backup directory for storing history variables and files.
2. Scans and saves history variables and files for each detected shell.
3. Disables history logging and modifies related variables.
4. Deletes all history files, excluding restricted directories.

## Usage

To run the script, simply execute it in your terminal:

```bash
./backup_and_disable_history.sh
```

## Functionality

### Backup Directory

The script creates a backup directory `~/history_backup` to store the history variables and files.

### Scanning and Saving History

For each detected shell, the script scans and saves history variables and files:

- **Fish:** `fish_history`, `fish_history_max`, `fish_history_length`
- **Bash:** `HISTFILE`, `HISTSIZE`, `HISTFILESIZE`, `HISTCONTROL`, `HISTIGNORE`
- **Zsh:** `HISTFILE`, `HISTSIZE`, `SAVEHIST`
- **Tcsh:** `history`, `savehist`
- **Sh:** `HISTFILE`, `HISTSIZE`, `HISTFILESIZE`

### Disabling History Logging

The script modifies shell configuration files to disable history logging:

- **Fish:** Adds commands to `~/.config/fish/config.fish`
- **Bash:** Adds commands to `~/.bashrc`
- **Zsh:** Adds commands to `~/.zshrc`
- **Tcsh:** Adds commands to `~/.tcshrc`
- **Sh:** Adds commands to `~/.profile`

### Deleting History Files

The script deletes all history files, excluding restricted directories (`/run/user/*/gvfs`).

## Notes

- Ensure you have the necessary permissions to modify configuration files and delete history files.
- Run the script with `sudo` if required to delete system-wide history files.

## Example

```bash
#!/bin/bash

# Create backup directory
backup_dir=~/history_backup
mkdir -p "$backup_dir"

# Function to scan and save history variables and files
scan_and_save() {
    shell_name=$1
    history_vars=$2
    history_files=$3

    echo "Scanning $shell_name history variables and files..."

    # Save history variables
    for var in $history_vars; do
        if [ ! -z "${!var}" ]; then
            echo "$var=${!var}" >> "$backup_dir/${shell_name}_history_vars.txt"
        fi
    done

    # Save history files
    for file in $history_files; do
        if [ -f "$file" ]; then
            cp "$file" "$backup_dir/"
        fi
    done

    echo "$shell_name history variables and files backed up."
}

# Scan Fish history variables and files
if command -v fish &> /dev/null; then
    fish_vars="fish_history fish_history_max fish_history_length"
    fish_files="~/.local/share/fish/fish_history"
    scan_and_save "fish" "$fish_vars" "$fish_files"
fi

# Scan Bash history variables and files
if command -v bash &> /dev/null; then
    bash_vars="HISTFILE HISTSIZE HISTFILESIZE HISTCONTROL HISTIGNORE"
    bash_files="~/.bash_history"
    scan_and_save "bash" "$bash_vars" "$bash_files"
fi

# Scan Zsh history variables and files
if command -v zsh &> /dev/null; then
    zsh_vars="HISTFILE HISTSIZE SAVEHIST"
    zsh_files="~/.zsh_history"
    scan_and_save "zsh" "$zsh_vars" "$zsh_files"
fi

# Scan Tcsh history variables and files
if command -v tcsh &> /dev/null; then
    tcsh_vars="history savehist"
    tcsh_files="~/.history"
    scan_and_save "tcsh" "$tcsh_vars" "$tcsh_files"
fi

# Scan Sh history variables and files
if command -v sh &> /dev/null; then
    sh_vars="HISTFILE HISTSIZE HISTFILESIZE"
    sh_files=""
    scan_and_save "sh" "$sh_vars" "$sh_files"
fi

# Turn off history and modify variables for Fish
if command -v fish &> /dev/null; then
    echo 'set -U fish_history' > ~/.config/fish/config.fish
    echo 'set -U fish_history_max 0' >> ~/.config/fish/config.fish
    echo 'set -U fish_history_length 0' >> ~/.config/fish/config.fish
    echo "Fish shell history disabled."
fi

# Turn off history and modify variables for Bash
if command -v bash &> /dev/null; then
    echo 'unset HISTFILE' >> ~/.bashrc
    echo 'HISTSIZE=0' >> ~/.bashrc
    echo 'HISTFILESIZE=0' >> ~/.bashrc
    echo 'HISTCONTROL=ignorespace:ignoredups' >> ~/.bashrc
    echo 'HISTIGNORE="&:[bf]g:exit"' >> ~/.bashrc
    source ~/.bashrc
    echo "Bash shell history disabled."
fi

# Turn off history and modify variables for Zsh
if command -v zsh &> /dev/null; then
    echo 'unset HISTFILE' >> ~/.zshrc
    echo 'HISTSIZE=0' >> ~/.zshrc
    echo 'SAVEHIST=0' >> ~/.zshrc
    echo 'setopt HIST_IGNORE_SPACE' >> ~/.zshrc
    echo 'setopt HIST_IGNORE_DUPS' >> ~/.zshrc
    source ~/.zshrc
    echo "Zsh shell history disabled."
fi

# Turn off history and modify variables for Tcsh
if command -v tcsh &> /dev/null; then
    echo 'set history=0' >> ~/.tcshrc
    echo 'set savehist=0' >> ~/.tcshrc
    source ~/.tcshrc
    echo "Tcsh shell history disabled."
fi

# Turn off history and modify variables for Sh
if command -v sh &> /dev/null; then
    echo 'HISTFILE=/dev/null' >> ~/.profile
    echo 'HISTSIZE=0' >> ~/.profile
    echo 'HISTFILESIZE=0' >> ~/.profile
    source ~/.profile
    echo "Sh shell history disabled."
fi

# Delete all history files, excluding restricted directories
echo "Deleting history files. This may take a while..."
sudo find / -name ".*history" -type f \( ! -path "/run/user/*/gvfs" \) -exec rm -f {} \;
sudo find / -name "*_history" -type f \( ! -path "/run/user/*/gvfs" \) -exec rm -f {} \;
sudo find / -name "history" -type f \( ! -path "/run/user/*/gvfs" \) -exec rm -f {} \;
echo "All history files deleted."

echo "History logging disabled, variables modified, and history files deleted for Fish, Bash, Zsh, Tcsh, and Sh."

# End of script
```

## License

This script is released under the [MIT License](LICENSE).
```

This `README.md` provides clear instructions and details on the script's purpose, usage, and functionality.