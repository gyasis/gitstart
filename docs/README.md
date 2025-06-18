<p align="center">
<img width="400" src="https://raw.githubusercontent.com/shinokada/gitstart/main/images/gitstart.png" />
</p>

<p align="center">
<a href='https://ko-fi.com/Z8Z2CHALG' target='_blank'><img height='42' style='border:0px;height:42px;' src='https://storage.ko-fi.com/cdn/kofi3.png?v=3' alt='Buy Me a Coffee at ko-fi.com' /></a>
</p>

<h1  align="center">Gitstart</h1>

[More details about Gitstart.](https://towardsdatascience.com/automate-creating-a-new-github-repository-with-gitstart-1ae961b99866)

## Overview

> Gitstart creates, adds, and pushes with one line - now supporting both GitHub and GitLab!

This script automates creating a git repo. The script will:

- Create .gitignore if you provide a language.
- Create a license.txt depends on your choice.
- Create a new repo at GitHub.com or GitLab.com.
- Create a README.md file with the repo name.
- Add README.md and commit with a message.
- Add the remote and push the file.

The script stores your username configuration separately for GitHub and GitLab and uses the directory name as the repository name.

## Requirements

- UNIX-like system (Tested on Ubuntu and MacOS.)
- [jq](https://stedolan.github.io/jq/) for JSON processing.
- **For GitHub**: [GitHub CLI](https://cli.github.com/manual/) >v2.3.0.
- **For GitLab**: [GitLab CLI (glab)](https://gitlab.com/gitlab-org/cli/-/releases) latest version.
- SSH keys configured for your chosen platform(s).

### Installing CLI Tools

**GitHub CLI:**
Linux users can download gh CLI from the [Releases page](https://github.com/cli/cli/releases), then run:
```sh
sudo apt install ./gh_x.x.x_xxxxxxx.deb
```

**GitLab CLI:**
```sh
# Homebrew (macOS/Linux)
brew install glab

# APT (Debian/Ubuntu)
sudo apt install glab

# Or download from releases
curl -s https://api.github.com/repos/profclems/glab/releases/latest | jq -r '.assets[] | select(.name | contains("linux_amd64.deb")) | .browser_download_url' | xargs wget
sudo apt install ./glab_*_linux_amd64.deb
```

## Installation

### Linux/macOS using Awesome

After installing [Awesome package manager](https://github.com/shinokada/awesome):

```sh
awesome install shinokada/gitstart
```

### macOS using Homebrew

If you have Homebrew on your macOS, your can run:

```sh
brew tap shinokada/gitstart && brew install gitstart
```

### Debian/Ubuntu

Download the latest version from [releases page](https://github.com/shinokada/gitstart/releases) and run:

```sh
sudo apt install ./gitstart_version_all.deb
```




## Uninstallation

If you install Gitstart either Awesome package manager/Homebrew/Debian package, then the following will uninstall Gistart.

```sh
curl -s https://raw.githubusercontent.com/shinokada/gitstart/main/uninstall.sh > tmp1 && bash tmp1 && rm tmp1
```

## Usage

### Authentication Setup

Since gitstart uses SSH keys for authentication, ensure your SSH keys are configured:

```sh
# Test GitHub SSH connection
ssh -T git@github.com

# Test GitLab SSH connection  
ssh -T git@gitlab.com
```

### Basic Usage

**GitHub (default):**
```sh
# Create repo in current directory
gitstart -d repo-name

# Create repo with language-specific .gitignore
gitstart -d repo-name -l python

# Explicitly specify GitHub
gitstart -d repo-name --github
```

**GitLab:**
```sh
# Create GitLab repo
gitstart -d repo-name --gitlab

# Create GitLab repo with language and group
gitstart -d repo-name -l python --gitlab -g mygroup/subgroup

# Work in current directory
cd existing_directory
gitstart . --gitlab
```

### GitLab Groups/Namespaces

You can specify a GitLab group or namespace in two ways:

**Using the flag:**
```sh
gitstart -d repo-name --gitlab -g proddev4/data
```

**Using environment variable:**
```sh
export GITSTART_GITLAB_GROUP="proddev4/data"
gitstart -d repo-name --gitlab
```

### Repository Visibility

**GitHub:** Choose between Public or Private (default: Public)

**GitLab:** Choose between Public, Internal, or Private (default: Internal)

### License Selection

- The script asks you about your license preference.
  
```sh
Is it correct your GitHub username is shinokada. y/yes/n/no
y
>>> Your GitHub username is shinokada.
>>> Your new repo name is test1.
>>> Using provider: github
Select a license:
1) MIT: I want it simple and permissive.
2) Apache License 2.0: I need to work in a community.
3) GNU GPLv3: I care about sharing improvements.
4) None
5) Quit
Your license: 2
Apache
Creating remote repo /Users/shinichiokada/Downloads/test1
>>> Creating GitHub repository...
✓ Created repository shinokada/test1 on GitHub
✓ Added remote git@github.com:shinokada/test1.git
>>> LICENSE is created.
>>> Creating .gitignore for python...
>>> .gitignore created.
>>> Creating README.md.
>>> Adding README.md and .gitignore.
>>> Commiting with a message 'first commit'.
>>> You have created a GitHub repo at https://github.com/shinokada/test1
```

**GitLab Example:**
```sh
>>> Your GitLab username is myuser.
>>> Your new repo name is myproject.
>>> Using provider: gitlab
>>> Using GitLab group/namespace: proddev4/data
>>> You are using GitLab. Choose visibility:
1) Public
2) Internal  
3) Private
4) Quit
Repository visibility: 2
Internal
Creating remote repo /Users/myuser/myproject
>>> Creating GitLab repository...
✓ Created repository proddev4/data/myproject on GitLab
>>> You have created a GitLab repo at https://gitlab.com/proddev4/data/myproject
```

## Command Line Options

```
gitstart [ -l | --lang programming_language ] [ -d | --dir directory ] [ --github | --gitlab ] [ -g | --group group_name ] [ -h | --help | -v | --version]
```

### Options

- `-l, --lang` - Programming language for .gitignore template (e.g., python, javascript, go)
- `-d, --dir` - Directory name for the new repository (use `.` for current directory)
- `--github` - Use GitHub as the Git provider (default)
- `--gitlab` - Use GitLab as the Git provider
- `-g, --group` - GitLab group/namespace (e.g., `proddev4/data`)
- `-h, --help` - Show help message
- `-v, --version` - Show version number

## Environment Variables

- `GITSTART_GITLAB_GROUP` - Default GitLab group/namespace to use when `--gitlab` is specified

**Example:**
```sh
export GITSTART_GITLAB_GROUP="proddev4/data"
# Now all GitLab repos will be created under proddev4/data/ by default
```

## Configuration Files

- `~/.gitstart_config` - Stores your GitHub username
- `~/.gitstart_config_gitlab` - Stores your GitLab username

These files are created automatically when you first run the script with each provider.

## About Licensing

Read more about [Licensing](https://docs.github.com/en/free-pro-team@latest/rest/reference/licenses).

## Author

Shinichi Okada

- [Medium](https://shinichiokada.medium.com/)
- [Twitter](https://twitter.com/shinokada)

## License

Copyright (c) 2021 Shinichi Okada (@shinokada)
This software is released under the MIT License, see LICENSE.
