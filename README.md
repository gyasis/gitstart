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

### Updating Gitstart

**If installed via Awesome:**
```bash
awesome update gitstart
# Or update all packages
awesome update all
```

**If installed via Homebrew:**
```bash
brew upgrade gitstart
```

**If installed manually:**
```bash
# Remove old version
sudo rm /usr/local/bin/gitstart

# Clone latest version
cd /tmp
git clone https://github.com/gyasis/gitstart.git
chmod +x gitstart/gitstart
sudo cp gitstart/gitstart /usr/local/bin/gitstart

# Verify version
gitstart -v
```

**Check current version:**
```bash
gitstart -v
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

### Complete Workflow Examples

**Example 1: Creating a GitHub Python project**
```bash
$ gitstart -d my-python-app -l python --github

>>> Creating my-python-app.
Is it correct your GitHub username is gyasis? y/yes/n/no: y
>>> Your GitHub username is gyasis.
>>> Your new repo name is my-python-app.
>>> Using provider: github
Select a license:
1) MIT: I want it simple and permissive.
2) Apache License 2.0: I need to work in a community.
3) GNU GPLv3: I care about sharing improvements.
4) None
5) Quit
Your license: 1
MIT
Creating remote repo /home/user/my-python-app
>>> Creating GitHub repository...
✓ Created repository gyasis/my-python-app on GitHub
✓ Added remote git@github.com:gyasis/my-python-app.git
>>> Configured Git email for GitHub: user@example.com
>>> LICENSE is created.
>>> Creating .gitignore for Python...
>>> .gitignore created.
>>> Creating README.md.
>>> Adding README.md and .gitignore.
>>> Commiting with a message 'first commit'.
>>> You have created a GitHub repo at https://github.com/gyasis/my-python-app
```

**Example 2: Creating a GitLab project in organization**
```bash
$ export GITSTART_GITLAB_GROUP="acme-corp/data-team"
$ gitstart -d customer-analytics -l python --gitlab

✅ Using GitLab group from environment: acme-corp/data-team

>>> Creating customer-analytics.
Is it correct your GitLab username is gyasis? y/yes/n/no: y
>>> Your GitLab username is gyasis.
>>> Your new repo name is customer-analytics.
>>> Using provider: gitlab
Select a license:
1) MIT: I want it simple and permissive.
2) Apache License 2.0: I need to work in a community.
3) GNU GPLv3: I care about sharing improvements.
4) None
5) Quit
Your license: 1
MIT
>>> You are using GitLab. Choose visibility:
1) Public
2) Internal
3) Private
4) Quit
Repository visibility: 2
Internal
Creating remote repo /home/user/customer-analytics
>>> Creating GitLab repository...
>>> Using GitLab group/namespace: acme-corp/data-team
✓ Created repository acme-corp/data-team/customer-analytics on GitLab
>>> Configured Git email for GitLab: user@company.com
>>> Configured GPG signing key for GitLab
>>> LICENSE is created.
>>> Creating .gitignore for Python...
>>> .gitignore created.
>>> Creating README.md.
>>> Adding README.md and .gitignore.
>>> Commiting with a message 'first commit'.
[main 1a2b3c4] first commit
>>> You have created a GitLab repo at https://gitlab.com/acme-corp/data-team/customer-analytics
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

## 🔍 How It Works

### Interactive Prompts You'll See

When you run `gitstart`, you'll be asked to make several choices:

#### 1. Username Confirmation
```
Is it correct your GitLab username is gyasis? y/yes/n/no:
```
- **What it does:** Confirms your cached username from `~/.gitstart_config` (GitHub) or `~/.gitstart_config_gitlab` (GitLab)
- **Your choices:**
  - `y` or `yes`: Use the displayed username
  - `n` or `no`: Enter a new username (will be saved for next time)

#### 2. License Selection
```
Select a license:
1) MIT: I want it simple and permissive.
2) Apache License 2.0: I need to work in a community.
3) GNU GPLv3: I care about sharing improvements.
4) None
5) Quit
Your license:
```
- **What it does:** Downloads the selected license from GitHub's License API
- **Your choices:**
  - `1`: MIT License - Simple, permissive, great for open source
  - `2`: Apache 2.0 - Patent protection, good for corporate/community projects
  - `3`: GNU GPLv3 - Copyleft, derivative works must also be open source
  - `4`: None - No license file created
  - `5`: Quit - Exit the script

#### 3. Repository Visibility (GitLab Only)
```
Repository visibility:
1) Public
2) Internal
3) Private
4) Quit
```
- **What it does:** Sets who can see your GitLab repository
- **Your choices:**
  - `1`: Public - Anyone on the internet can see it
  - `2`: Internal - Any logged-in GitLab user can see it (common for corporate instances)
  - `3`: Private - Only you and invited members can see it
  - `4`: Quit - Exit the script

#### 4. GitLab CLI Prompts (GitLab Only)

When using GitLab, `glab` may ask additional questions:

**"Directory not git initialized. Run git init?"**
- **Answer:** Say `No` or let it time out
- **Why:** The script will initialize git itself with the correct configuration

**"Create a local project directory for [group]/[repo]?"**
- **Answer:** Say `Yes`
- **Why:** This creates the local directory structure that matches your remote repo

### What Gets Created

After answering the prompts, gitstart creates:

1. **📁 Directory Structure**
   ```
   my-repo/
   ├── .git/           # Git repository (initialized)
   ├── LICENSE         # Your chosen license
   ├── .gitignore      # Language-specific or minimal
   └── README.md       # Template with sections
   ```

2. **📄 README.md Template**
   ```markdown
   # my-repo

   ## Overview

   ## Requirement

   ## Usage

   ## Features

   ## Reference

   ## Author

   ## License

   Please see license.txt.
   ```

3. **🔗 Git Remote**
   - **GitHub:** `git@github.com:username/repo.git`
   - **GitLab:** `git@gitlab.com:group/repo.git` or `git@gitlab.com:username/repo.git`

4. **📝 Initial Commit**
   - Adds LICENSE, README.md, .gitignore
   - Commits with message: "first commit"
   - For GitLab: Signed with GPG (if configured)
   - Pushes to main branch

### Behind the Scenes Flow

**GitHub:**
1. Creates directory
2. Runs `gh repo create --public/--private --clone`
3. Downloads license file
4. Downloads .gitignore (if language specified)
5. Creates README.md
6. Configures email: `user.email`
7. Commits and pushes

**GitLab:**
1. Creates directory
2. Runs `glab repo create -g group --internal/--public/--private`
3. Verifies/creates `.git` directory with `git init`
4. Verifies/corrects remote URL
5. Configures email: `user.email`
6. Configures GPG signing key: `user.signingkey`
7. Downloads license file
8. Downloads .gitignore (if language specified)
9. Creates README.md
10. Commits with GPG signature: `git commit -S`
11. Pushes to main branch

### Configuration Files Created

- **`~/.gitstart_config`**: Stores your GitHub username
- **`~/.gitstart_config_gitlab`**: Stores your GitLab username

These are created automatically on first use and referenced on subsequent runs.

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

## 🔧 For Maintainers and Forkers

If you're forking this project or customizing it for your organization, you'll need to update these hardcoded values in the `gitstart` script:

### Hardcoded Credentials

**Lines 230, 233** - Default username:
```bash
github_username=$(get_username "GitHub" "$gitstart_config" "YOUR_GITHUB_USERNAME")
gitlab_username=$(get_username "GitLab" "$gitlab_config" "YOUR_GITLAB_USERNAME")
```

**Line 369** - GitHub email:
```bash
git config user.email "YOUR_GITHUB_EMAIL@example.com"
```

**Lines 372-373** - GitLab email and GPG key:
```bash
git config user.email "YOUR_GITLAB_EMAIL@example.com"
git config user.signingkey "YOUR_GPG_KEY_ID"
```

### Finding Your GPG Key ID

```bash
gpg --list-secret-keys --keyid-format LONG
# Look for sec rsa4096/YOUR_KEY_ID in the output
# Example output:
# sec   rsa4096/96132160E21A8900 2024-01-15 [SC]
#       96132160E21A89003E03337A0AC7D767310FB350
# The key ID is: 96132160E21A89003E03337A0AC7D767310FB350
```

### Making It Configurable (Recommended)

For a more maintainable approach, consider extracting these to environment variables in your fork:

```bash
# Add to ~/.bashrc or ~/.zshrc
export GITSTART_GITHUB_EMAIL="your.email@gmail.com"
export GITSTART_GITLAB_EMAIL="your.email@company.com"
export GITSTART_GITLAB_GPG_KEY="YOUR_KEY_ID"
export GITSTART_DEFAULT_USERNAME="your-username"
```

Then modify the script to read from these variables instead of using hardcoded values.

### Testing Your Fork

After customizing, test both platforms:
```bash
# Test GitHub
gitstart -d test-github-repo --github
cd test-github-repo && git log --show-signature

# Test GitLab
gitstart -d test-gitlab-repo --gitlab
cd test-gitlab-repo && git log --show-signature
```

Verify that:
- Correct email addresses are used
- GPG signing works for GitLab
- Default usernames are appropriate

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

We welcome contributions! Whether you're fixing bugs, adding features, or improving documentation.

**Quick Start for Contributors:**

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes and test thoroughly
4. Commit your changes (`git commit -m 'feat: add some amazing feature'`)
5. Push to the branch (`git push origin feature/amazing-feature`)
6. Open a Pull Request

**For detailed development workflow, testing, and using Awesome with local git repos:**

📖 **See [DEV.md](DEV.md) for the complete development guide**

The development guide covers:
- Setting up your development environment
- Installing and testing from your local fork
- Working with hardcoded values
- Testing both GitHub and GitLab workflows
- Contributing guidelines and code style
- Release process

## 📞 Support

- **Documentation**: [Full docs site](https://shinokada.github.io/gitstart/)
- **Issues**: [GitHub Issues](https://github.com/gyasis/gitstart/issues)
- **Original Creator**: [Shinichi Okada](https://github.com/shinokada)

## 📚 License

This software is released under the MIT License. See [License](License) for details.

---

**Happy coding! 🚀**