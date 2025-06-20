# Gitstart

<p align="center">
<img width="400" src="https://raw.githubusercontent.com/shinokada/gitstart/main/images/gitstart.png" />
</p>

<p align="center">
<a href='https://ko-fi.com/Z8Z2CHALG' target='_blank'><img height='42' style='border:0px;height:42px;' src='https://storage.ko-fi.com/cdn/kofi3.png?v=3' alt='Buy Me a Coffee at ko-fi.com' /></a>
</p>

> **Gitstart creates, adds, and pushes with one line - now supporting both GitHub and GitLab!**

[📖 **Full Documentation**](https://shinokada.github.io/gitstart/) | [📝 **Medium Article**](https://towardsdatascience.com/automate-creating-a-new-github-repository-with-gitstart-1ae961b99866)

## Overview

This script automates creating a git repository on GitHub or GitLab. The script will:

- Create .gitignore if you provide a language
- Create a license file based on your choice
- Create a new repo at GitHub.com or GitLab.com
- Create a README.md file with the repo name
- Add README.md and commit with a message
- Add the remote and push the files

## 🚀 Quick Start

### Prerequisites

- **System**: UNIX-like (tested on Ubuntu and macOS)
- **Tools**: [jq](https://stedolan.github.io/jq/) for JSON processing
- **For GitHub**: [GitHub CLI](https://cli.github.com/manual/) >v2.3.0
- **For GitLab**: [GitLab CLI (glab)](https://gitlab.com/gitlab-org/cli/-/releases) latest version
- **Authentication**: SSH keys configured for your chosen platform(s)

### Installation

#### Option 1: Using Awesome Package Manager (Recommended)
```bash
# Install Awesome package manager
curl -s https://raw.githubusercontent.com/shinokada/awesome/main/install | bash -s install

# Add to your PATH (add to ~/.bashrc or ~/.zshrc)
export PATH=$HOME/.local/share/bin:$PATH

# Install gitstart
awesome install gyasis/gitstart
```

#### Option 2: Using Homebrew (macOS)
```bash
brew tap shinokada/gitstart && brew install gitstart
```

#### Option 3: Manual Installation
```bash
# Clone and make executable
git clone https://github.com/gyasis/gitstart.git
chmod +x gitstart/gitstart
sudo cp gitstart/gitstart /usr/local/bin/gitstart
```

### Install Required CLI Tools

**GitHub CLI:**
```bash
# Debian/Ubuntu
sudo apt install gh

# macOS
brew install gh
```

**GitLab CLI:**
```bash
# Debian/Ubuntu
sudo apt install glab

# macOS
brew install glab

# Or download from releases
curl -s https://api.github.com/repos/profclems/glab/releases/latest | jq -r '.assets[] | select(.name | contains("linux_amd64.deb")) | .browser_download_url' | xargs wget
sudo apt install ./glab_*_linux_amd64.deb
```

**jq (JSON processor):**
```bash
# Debian/Ubuntu
sudo apt install jq

# macOS
brew install jq
```

## 🔧 Configuration

### Authentication Setup
Ensure your SSH keys are configured for the platforms you want to use:

```bash
# Test GitHub SSH connection
ssh -T git@github.com

# Test GitLab SSH connection  
ssh -T git@gitlab.com

# Authenticate CLI tools
gh auth login    # for GitHub
glab auth login  # for GitLab
```

### Environment Variables

Set up your default GitLab group/namespace:

```bash
# Add to ~/.bashrc or ~/.zshrc
export GITSTART_GITLAB_GROUP="your-group/subgroup"

# Example for company workspace
export GITSTART_GITLAB_GROUP="proddev4/data"

# Reload your shell
source ~/.bashrc  # or source ~/.zshrc
```

## 📖 Usage

### Basic Examples

**GitHub (default):**
```bash
# Create repo in current directory
gitstart -d my-repo

# Create repo with language-specific .gitignore
gitstart -d my-repo -l python

# Explicitly specify GitHub
gitstart -d my-repo --github
```

**GitLab:**
```bash
# Create GitLab repo (uses environment variable for group)
gitstart -d my-repo --gitlab

# Create GitLab repo with specific group
gitstart -d my-repo --gitlab -g mygroup/subgroup

# Create GitLab repo with language
gitstart -d my-repo -l javascript --gitlab

# Work in current directory
cd existing_directory
gitstart . --gitlab
```

### Command Line Options

```
gitstart [ -l | --lang programming_language ] [ -d | --dir directory ] [ --github | --gitlab ] [ -g | --group group_name ] [ -h | --help | -v | --version]
```

#### Options
- `-l, --lang` - Programming language for .gitignore template (e.g., python, javascript, go)
- `-d, --dir` - Directory name for the new repository (use `.` for current directory)
- `--github` - Use GitHub as the Git provider (default)
- `--gitlab` - Use GitLab as the Git provider
- `-g, --group` - GitLab group/namespace (e.g., `proddev4/data`)
- `-h, --help` - Show help message
- `-v, --version` - Show version number

### Repository Visibility

**GitHub:** Choose between Public or Private (default: Public)

**GitLab:** Choose between Public, Internal, or Private (default: Internal)
- **Public**: Visible to anyone without authentication
- **Internal**: Visible to any authenticated user (GitLab-specific)
- **Private**: Visible only to project members

## 🏢 Setting Up for Your Organization

### For GitLab Organizations

1. **Find your group path:**
   ```bash
   # List your groups
   glab api user | jq '.groups'
   
   # Or visit https://gitlab.com/groups to find your group path
   ```

2. **Set environment variable:**
   ```bash
   echo 'export GITSTART_GITLAB_GROUP="your-org/your-subgroup"' >> ~/.bashrc
   source ~/.bashrc
   ```

3. **Create repos automatically in your workspace:**
   ```bash
   gitstart -d new-project --gitlab
   # Creates: https://gitlab.com/your-org/your-subgroup/new-project
   ```

### Example Company Setup

```bash
# Company: Products, Subgroup: data
export GITSTART_GITLAB_GROUP="proddev4/data"

# Now all GitLab repos go to: gitlab.com/proddev4/data/
gitstart -d customer-analytics --gitlab -l python
gitstart -d survey-tool --gitlab -l javascript
```

## 📋 Supported Languages

Gitstart supports .gitignore templates for any language available in [GitHub's gitignore repository](https://github.com/github/gitignore), including:

- Python, JavaScript, TypeScript, Go, Rust, Java, C++, C#
- Node, React, Vue, Angular, Django, Flask, Rails
- And many more...

## 🔄 Configuration Files

- `~/.gitstart_config` - Stores your GitHub username
- `~/.gitstart_config_gitlab` - Stores your GitLab username

These files are created automatically when you first run the script with each provider.

## ⚠️ Important Setup Notes

### GitLab GPG Signing Requirements

Many GitLab repositories (especially enterprise/organization repos) require **GPG-signed commits**. If you encounter this error:

```
remote: GitLab: Commit must be signed with a GPG key
! [remote rejected] main -> main (pre-receive hook declined)
```

**You need to set up GPG keys:**

1. **Check if you have GPG keys:**
   ```bash
   gpg --list-keys
   ```

2. **Generate a GPG key if needed:**
   ```bash
   gpg --full-generate-key
   # Choose RSA and RSA (default)
   # Choose 4096 bits
   # Set expiration (recommended: 1 year)
   # Enter your name and GitLab email address
   ```

3. **Add GPG key to GitLab:**
   ```bash
   # Export your public key
   gpg --armor --export YOUR_EMAIL@example.com
   
   # Copy the output and add it to GitLab:
   # GitLab → Settings → GPG Keys → Add Key
   ```

4. **Configure Git to use your GPG key:**
   ```bash
   # Get your key ID
   gpg --list-secret-keys --keyid-format LONG
   
   # Configure Git globally (optional)
   git config --global user.signingkey YOUR_KEY_ID
   git config --global commit.gpgsign true
   ```

**Note:** Gitstart automatically configures GPG signing for GitLab repositories when it detects your GPG key.

### Different Email Addresses

Gitstart automatically handles different email addresses for each platform:
- **GitHub**: Uses the email from your GitHub account
- **GitLab**: Uses your GitLab/organization email

The script sets the correct email per repository based on the provider.

### WSL (Windows Subsystem for Linux) Issues

If you're using WSL and encounter GPG passphrase prompts that fail:

1. **Set GPG_TTY environment variable:**
   ```bash
   echo 'export GPG_TTY=$(tty)' >> ~/.bashrc
   source ~/.bashrc
   ```

2. **Install and configure pinentry for WSL:**
   ```bash
   sudo apt install pinentry-curses
   echo 'pinentry-program /usr/bin/pinentry-curses' >> ~/.gnupg/gpg-agent.conf
   gpg-connect-agent reloadagent /bye
   ```

3. **For WSL2 with Windows integration:**
   ```bash
   # Install pinentry-wsl-ps1 for Windows integration
   wget https://github.com/diablodale/pinentry-wsl-ps1/releases/latest/download/pinentry-wsl-ps1.sh
   chmod +x pinentry-wsl-ps1.sh
   sudo mv pinentry-wsl-ps1.sh /usr/local/bin/
   echo 'pinentry-program /usr/local/bin/pinentry-wsl-ps1.sh' >> ~/.gnupg/gpg-agent.conf
   ```

### GitLab CLI Directory Prompt

When using GitLab, you'll see this prompt:
```
? Create a local project directory for Organization / project-name? 
```

**Always answer "Yes"** - this is normal GitLab CLI behavior and creates the local repository structure.

### Default Username Configuration

Gitstart now provides default usernames during setup:
- First-time setup prompts: `Please type your GitHub username [gyasis]:`
- Just press Enter to use the default, or type your actual username

### Troubleshooting Common Issues

1. **"command not found: gitstart"**
   ```bash
   # Make sure awesome/bin is in your PATH
   echo 'export PATH=$HOME/.local/share/bin:$PATH' >> ~/.bashrc
   source ~/.bashrc
   ```

2. **SSH key issues:**
   ```bash
   # Test connections
   ssh -T git@github.com
   ssh -T git@gitlab.com
   
   # Generate SSH keys if needed
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

3. **Permission denied errors:**
   ```bash
   # Make sure gitstart is executable
   chmod +x ~/.local/share/awesome/gitstart/gitstart
   ```

4. **GitLab group not found:**
   ```bash
   # Verify your group path
   glab api user | jq '.groups'
   # Or check: https://gitlab.com/groups
   ```

## 🗑️ Uninstallation

```bash
# If installed via Awesome
awesome rm gitstart

# If installed via Homebrew
brew uninstall gitstart

# Universal uninstall script
curl -s https://raw.githubusercontent.com/shinokada/gitstart/main/uninstall.sh | bash
```

## 📜 License Selection

Choose from:
1. **MIT**: Simple and permissive
2. **Apache License 2.0**: For community projects  
3. **GNU GPLv3**: For sharing improvements
4. **None**: No license file

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📞 Support

- **Documentation**: [Full docs site](https://shinokada.github.io/gitstart/)
- **Issues**: [GitHub Issues](https://github.com/gyasis/gitstart/issues)
- **Original Creator**: [Shinichi Okada](https://github.com/shinokada)

## 📚 License

This software is released under the MIT License. See [License](License) for details.

---

**Happy coding! 🚀**