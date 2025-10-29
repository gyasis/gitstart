# Development Guide for Gitstart

This guide is for developers who want to contribute to gitstart or customize it for their own use.

## Table of Contents

- [Setting Up for Development](#setting-up-for-development)
- [Development Workflow with Awesome Package Manager](#development-workflow-with-awesome-package-manager)
- [Testing Your Changes](#testing-your-changes)
- [Working with Hardcoded Values](#working-with-hardcoded-values)
- [Contributing Guidelines](#contributing-guidelines)
- [Release Process](#release-process)

---

## Setting Up for Development

### 1. Fork or Clone the Repository

**Fork (for contributing back):**
```bash
# Fork on GitHub first: https://github.com/gyasis/gitstart/fork
# Then clone your fork
git clone https://github.com/YOUR_USERNAME/gitstart.git
cd gitstart
```

**Clone (for personal use):**
```bash
git clone https://github.com/gyasis/gitstart.git
cd gitstart
```

### 2. Install Prerequisites

Make sure you have all the required tools:
```bash
# GitHub CLI (if testing GitHub functionality)
gh --version

# GitLab CLI (if testing GitLab functionality)
glab --version

# jq for JSON processing
jq --version

# Awesome package manager (for development workflow)
awesome -v
```

### 3. Create a Development Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/your-bug-fix
```

---

## Development Workflow with Awesome Package Manager

### Why Use Awesome for Development?

The Awesome package manager allows you to **install directly from your local git repository**, making it easy to test changes without manually copying files to `/usr/local/bin`.

### Installing from Local Repository

**Option 1: Install from current directory**
```bash
cd /path/to/your/gitstart/clone
awesome install github.com/YOUR_USERNAME/gitstart
```

**Option 2: Install from local path**
```bash
# Awesome can install from local git repositories
cd /path/to/your/gitstart/clone
awesome install .
```

Wait, that doesn't work! Let me check the correct awesome syntax:

**Correct Method: Use the GitHub URL**
```bash
# After committing and pushing to YOUR fork
awesome install YOUR_USERNAME/gitstart
```

### Testing Changes During Development

Since `awesome install .` doesn't work, here are better workflows:

#### Method 1: Direct Execution (Recommended for Quick Testing)

```bash
cd /path/to/your/gitstart/clone

# Make executable
chmod +x ./gitstart

# Run directly without installing
./gitstart -d test-repo --gitlab

# Test with different options
./gitstart -d test-github --github
./gitstart -d test-python -l python --gitlab
```

#### Method 2: Symlink to Local Version

```bash
# Remove installed version
awesome rm gitstart
# or
sudo rm /usr/local/bin/gitstart

# Create symlink to your development version
ln -s /path/to/your/gitstart/clone/gitstart ~/.local/share/bin/gitstart
# or
sudo ln -s /path/to/your/gitstart/clone/gitstart /usr/local/bin/gitstart

# Now 'gitstart' runs your development version
gitstart -d test-repo --gitlab
```

**Advantages:**
- Changes take effect immediately
- No need to reinstall after each change
- Easy to switch back to stable version

**To switch back:**
```bash
rm ~/.local/share/bin/gitstart
awesome install gyasis/gitstart
```

#### Method 3: Install from Your Fork

```bash
# After committing and pushing to YOUR fork on GitHub
awesome install YOUR_USERNAME/gitstart

# Test changes
gitstart -d test-repo --gitlab

# Update after making more changes
git push
awesome update gitstart
```

---

## Testing Your Changes

### 1. Create Test Repositories

Always test both GitHub and GitLab workflows:

```bash
# Test GitHub
cd ~/test-area
./gitstart -d test-github-repo --github

# Verify results
cd test-github-repo
git log --show-signature
git remote -v
ls -la

# Clean up
cd ..
rm -rf test-github-repo
```

```bash
# Test GitLab
cd ~/test-area
export GITSTART_GITLAB_GROUP="your-test-group/subgroup"
./gitstart -d test-gitlab-repo --gitlab

# Verify results
cd test-gitlab-repo
git log --show-signature
git remote -v
ls -la

# Clean up
cd ..
rm -rf test-gitlab-repo
```

### 2. Test Edge Cases

```bash
# Test current directory
mkdir existing-dir && cd existing-dir
gitstart -d . --gitlab

# Test with language support
gitstart -d python-project -l python --github

# Test with different licenses
gitstart -d apache-project --gitlab
# Choose Apache License 2.0

# Test with different visibility options
gitstart -d private-repo --gitlab
# Choose Private
```

### 3. Test Error Handling

```bash
# Test with home directory (should fail)
gitstart -d ~ --gitlab

# Test with missing CLI tools (temporarily rename gh or glab)
sudo mv /usr/bin/glab /usr/bin/glab.bak
gitstart -d test --gitlab  # Should fail with helpful message
sudo mv /usr/bin/glab.bak /usr/bin/glab

# Test with expired GPG key (if applicable)
# Should show clear error message
```

### 4. Test on Different Environments

If possible, test on:
- Ubuntu/Debian
- macOS
- WSL (Windows Subsystem for Linux)
- Different shells (bash, zsh)

---

## Working with Hardcoded Values

### Current Hardcoded Values (as of v0.3.0)

The script contains several hardcoded values that should be customized:

**File: `gitstart` lines 230, 233, 369, 372-373:**

```bash
# Default usernames
github_username=$(get_username "GitHub" "$gitstart_config" "gyasis")
gitlab_username=$(get_username "GitLab" "$gitlab_config" "gyasis")

# GitHub email
git config user.email "gyasis@gmail.com"

# GitLab email and GPG key
git config user.email "gyasi.sutton@herself-health.com"
git config user.signingkey "96132160E21A89003E03337A0AC7D767310FB350"
```

### For Personal Use (Forking)

If you're forking for your own use, update these values:

1. **Fork the repository**
2. **Update hardcoded values:**
   ```bash
   # Edit gitstart script
   nano gitstart

   # Change line 230, 233: Your default username
   # Change line 369: Your GitHub email
   # Change line 372: Your GitLab/organization email
   # Change line 373: Your GPG key ID (get with: gpg --list-secret-keys --keyid-format LONG)
   ```

3. **Commit your changes:**
   ```bash
   git commit -am "chore: customize hardcoded values for personal use"
   git push origin main
   ```

4. **Install from your fork:**
   ```bash
   awesome install YOUR_USERNAME/gitstart
   ```

### For Contributing (Pull Requests)

If you're contributing back to the main project:

**DO NOT** change the hardcoded values in your PR unless you're specifically making them configurable via environment variables.

**Example of making values configurable:**
```bash
# Instead of:
git config user.email "gyasis@gmail.com"

# Make it configurable:
github_email="${GITSTART_GITHUB_EMAIL:-gyasis@gmail.com}"
git config user.email "$github_email"
```

This allows users to override via environment variables while maintaining backward compatibility.

---

## Contributing Guidelines

### Code Style

- Use `bash` (not `sh` or `zsh` specific features)
- Use 4-space indentation
- Add comments for complex logic
- Follow existing code patterns
- Test on both Ubuntu and macOS if possible

### Commit Messages

Follow the conventional commits format:

```
<type>: <description>

[optional body]

[optional footer]
```

**Types:**
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `chore:` Maintenance tasks
- `refactor:` Code refactoring
- `test:` Adding tests

**Examples:**
```bash
git commit -m "feat: add support for Bitbucket repositories"

git commit -m "fix: prevent parent directory initialization for GitLab repos

The script was calling glab repo create from the parent directory,
causing glab to prompt to initialize the parent directory with git init.
Now the script cd's into the target directory first for GitLab."

git commit -m "docs: update README with GPG signing troubleshooting"
```

### Pull Request Process

1. **Create a feature branch:**
   ```bash
   git checkout -b feature/your-feature
   ```

2. **Make your changes and test thoroughly**

3. **Update documentation:**
   - Update `README.md` if user-facing changes
   - Update `CLAUDE.md` if architecture changes
   - Update `docs/README.md` for documentation site
   - Update `DEV.md` if development workflow changes

4. **Commit your changes:**
   ```bash
   git add .
   git commit -m "feat: add your feature description"
   ```

5. **Push to your fork:**
   ```bash
   git push origin feature/your-feature
   ```

6. **Create a Pull Request** on GitHub

7. **Respond to review feedback**

### What to Include in Your PR

- Clear description of what changed and why
- Test results (show terminal output of testing)
- Documentation updates
- Breaking changes clearly noted
- Screenshots/GIFs if UI/output changes

---

## Release Process

### For Maintainers

1. **Update version number** in `gitstart` script:
   ```bash
   version="0.4.0"  # Line 12
   ```

2. **Update CHANGELOG.md** (create if doesn't exist):
   ```markdown
   ## [0.4.0] - 2025-10-30

   ### Added
   - New feature X
   - New feature Y

   ### Fixed
   - Bug fix A
   - Bug fix B

   ### Changed
   - Improvement C
   ```

3. **Commit version bump:**
   ```bash
   git add gitstart CHANGELOG.md
   git commit -m "chore: bump version to 0.4.0"
   ```

4. **Create and push tag:**
   ```bash
   git tag -a v0.4.0 -m "Release version 0.4.0"
   git push origin main
   git push origin v0.4.0
   ```

5. **Create GitHub Release:**
   - Go to: https://github.com/gyasis/gitstart/releases/new
   - Choose tag: `v0.4.0`
   - Title: `v0.4.0`
   - Description: Copy from CHANGELOG.md
   - Publish release

6. **Announce update:**
   - Users can update with: `awesome update gitstart`
   - Homebrew users: `brew upgrade gitstart`

---

## Debugging Tips

### Enable Bash Debug Mode

```bash
# Add to top of script temporarily
set -x  # Print each command before execution

# Or run with debug mode
bash -x ./gitstart -d test-repo --gitlab
```

### Check Script Execution Flow

```bash
# Add temporary debug output
echo "DEBUG: dir=$dir" >&2
echo "DEBUG: Current directory: $(pwd)" >&2
echo "DEBUG: provider=$provider" >&2
```

### Test GPG Signing

```bash
# Verify GPG key is valid
gpg --list-secret-keys --keyid-format LONG

# Test signing manually
echo "test" | gpg --clearsign

# Check git GPG config
git config --get user.signingkey
git config --get commit.gpgsign
```

### Test GitLab CLI

```bash
# Check glab authentication
glab auth status

# List available flags
glab repo create --help

# Test creating repo manually
glab repo create test-manual -g proddev4/data --internal
```

---

## Project Structure

```
gitstart/
├── gitstart              # Main bash script
├── README.md             # User documentation
├── CLAUDE.md             # AI assistant context
├── DEV.md               # This file - development guide
├── LICENSE              # MIT License
├── docs/
│   ├── index.html       # Docsify configuration
│   ├── README.md        # Documentation site content
│   └── public/
│       └── css/         # Documentation styling
├── images/
│   └── gitstart.png     # Logo
└── uninstall.sh         # Uninstaller script
```

---

## Useful Commands Reference

```bash
# Development
./gitstart -d test --gitlab           # Test local changes
bash -x ./gitstart -d test --gitlab   # Debug mode
git log --oneline -10                 # Recent commits

# Git operations
git status                            # Check status
git diff                              # See changes
git add -p                            # Stage changes interactively
git commit --amend                    # Amend last commit

# Testing
which gitstart                        # Where is gitstart installed
gitstart -v                          # Check version
awesome ls | grep gitstart           # Check if installed via awesome

# Cleanup
rm -rf ~/.local/share/awesome/gitstart  # Remove awesome installation
awesome rm gitstart                     # Uninstall via awesome
```

---

## Getting Help

- **Issues:** https://github.com/gyasis/gitstart/issues
- **Discussions:** https://github.com/gyasis/gitstart/discussions
- **Original Creator:** [Shinichi Okada](https://github.com/shinokada)
- **Current Maintainer:** [Gyasi Sutton](https://github.com/gyasis)

---

## License

This project is released under the MIT License. See [LICENSE](LICENSE) for details.

When contributing, you agree that your contributions will be licensed under the same MIT License.
