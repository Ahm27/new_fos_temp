# FOS 2026 Universal Docker Template

Run the FOS project on macOS, Linux, or Windows without installing the
cross-compiler and Bochs directly on your computer. Docker provides the full
build environment, while the `run` script opens FOS in a browser through
noVNC (or directly in your terminal).

## Requirements

- Git
- Docker Desktop on macOS or Windows, or Docker Engine on Linux
- At least 5 GB of free disk space for the Docker image and build files

Make sure Docker is running before continuing.

## First-time setup

### 1. Clone the repository

```bash
git clone https://github.com/Ahm27/new_fos_temp.git
cd new_fos_temp
```

If you downloaded a ZIP instead, extract it and open a terminal in the
extracted project directory.

### 2. Run the setup for your operating system

The initial setup builds the `fos-env` Docker image and can take several
minutes.

#### macOS

Start Docker Desktop, then run:

```bash
bash setup_macos.sh
```

#### Linux

Start Docker, then run:

```bash
chmod +x setup_linux.sh
./setup_linux.sh
```

If Docker reports a permission error, follow your distribution's Docker
post-installation instructions or run the setup with an account that can use
Docker.

#### Windows

Start Docker Desktop, open PowerShell in the project directory, then run:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\setup_windows.ps1
```

The execution-policy change applies only to the current PowerShell window.

### 3. Run FOS

Browser mode (recommended):

```bash
./run
```

The script prints and normally opens a URL similar to:

```text
http://localhost:6080/<run-id>/vnc.html?autoconnect=true&resize=scale
```

If the browser does not open automatically, copy the printed URL into your
browser. Each run uses a fresh URL, so a hard refresh is not required.

Terminal mode is also available:

```bash
./run term
```

Use `Ctrl+C` to stop a run. To remove generated build files, use:

```bash
./clean
```

## Updating an existing setup

Open a terminal in your existing project and check its current repository and
local changes:

```bash
git remote -v
git status
```

### If `origin` is already `Ahm27/new_fos_temp`

Commit or stash changes you want to keep, then update normally:

```bash
git pull --ff-only
```

### If `origin` still points to the old repository

Commit or stash changes you want to keep, then change `origin` and download
the new repository's `main` branch:

```bash
git remote set-url origin https://github.com/Ahm27/new_fos_temp.git
git fetch origin
git pull --ff-only origin main
```

The `--ff-only` option prevents Git from creating an unexpected merge. If the
last command says that a fast-forward is not possible, your local repository
has different commits. Keep the old directory as a backup and make a fresh
clone instead:

```bash
cd ..
git clone https://github.com/Ahm27/new_fos_temp.git
cd new_fos_temp
```

Copy only your own modified FOS source files from the old directory into the
new clone. Do not copy `.git`, `obj`, `bochs.log`, or other generated files.

### After updating

For ordinary FOS source-code updates, the existing Docker environment is
enough; run:

```bash
./run
```

Run your operating system's setup script again only when one of these files
changed in the update:

- `Dockerfile.fos`
- `setup_macos.sh`
- `setup_linux.sh`
- `setup_windows.ps1`

Rerunning setup is safe: it refreshes the Docker image and launcher scripts
without deleting your FOS source code.

## Normal development workflow

After editing the FOS source code, run:

```bash
./run
```

By default, `run` performs a clean build. For a faster incremental build:

```bash
NOCLEAN=1 ./run
```

If incremental compilation behaves unexpectedly, use the normal `./run`
command again.

## Troubleshooting

### Docker is not running

Start Docker Desktop or the Docker service, wait until it is ready, and retry
the command.

### Port 6080 is already in use

Stop the previous FOS container or any other application using port `6080`,
then run `./run` again.

### ALSA messages or `buffer overflow detected`

Pull the latest repository version. The launcher disables container audio,
which prevents Bochs from trying to open a host ALSA device that does not
exist inside Docker.

### Rebuild the environment

If the Docker environment is missing or damaged, rerun the setup script for
your operating system. You do not need to clone the repository again.

