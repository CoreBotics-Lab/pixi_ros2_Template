# Pixi ROS 2 Lyrical Workspace Template

A reproducible, fully isolated **ROS 2 Lyrical + RoboStack** development template powered by [Pixi](https://pixi.sh).

Provides Docker-like workspace isolation with **100% native hardware access** (NVIDIA/AMD GPUs, displays, USB sensors, serial devices) and zero host system pollution.

---

## Features

- **Isolated & Reproducible**: Powered by RoboStack Lyrical and Pixi. No `sudo apt` or `rosdep` pollution on the host.
- **Dynamic Fast Builds**: Custom build scripts supporting single packages, wildcards (`build my_pkg*`), and clean builds (`clean all`).
- **Tab Autocompletion**: Package name completion works automatically inside `pixi shell` (`build <TAB>`).
- **VS Code Pre-configured**:
  - C++ IntelliSense via **Clangd** with automatic compile command indexing.
  - Python diagnostics via **Pyrefly** & Pylance.
  - Interactive Debugging for C++ nodes, Python nodes, and GDB attach.
  - Recommended extensions bundle in `.vscode/extensions.json`.
- **Wayland / Multi-Monitor Fix**: Built-in environment activation variables that eliminate Qt/OGRE viewport flickering in RViz2 and Gazebo Harmonic on high-DPI and fractional scaling setups.

---

## Ubuntu Setup (Native)

### 1. Prerequisites
Install [Pixi](https://pixi.sh) on your host machine (if not already installed):
```bash
curl -fsSL https://pixi.sh/install.sh | bash
```

### 2. Setup Workspace
Clone or download this template, then initialize the environment:
```bash
git clone https://github.com/CoreBotics-Lab/pixi_ros2_lyricalLuthTemplate.git
cd pixi_ros2_lyricalLuthTemplate
pixi install
```

### 3. Enter Environment & Build
```bash
pixi shell
```
Inside the shell, your ROS 2 environment is active and custom helper commands are ready:
```bash
# Build all packages with symlink-install
build

# Build specific packages (supports TAB completion)
build <package_name>

# Build with debug symbols
debug <package_name>

# Clean build artifacts
clean all
```

---

## Windows Setup (via WSL2)

Windows users can run this template with full GPU acceleration, GUI display support (WSLg), and native Linux performance using WSL2.

### Step 1: Install WSL2 and Ubuntu
1. Open **PowerShell** as **Administrator** on Windows 10/11.
2. Run the following command (Ubuntu 26.04 recommended by default, or 24.04 based on preference):
   ```powershell
   wsl --install -d Ubuntu-26.04
   ```
   *(Or for Ubuntu 24.04: `wsl --install -d Ubuntu-24.04`)*
3. Restart your computer if prompted by Windows.
4. After restarting, Ubuntu will launch in a terminal window. Follow the prompt to create your Linux username and password.

### Step 2: Open Ubuntu Terminal
For subsequent sessions, open Ubuntu by either:
- Searching for **Ubuntu** in the Windows Start menu.
- Opening **Windows Terminal** and selecting the Ubuntu profile.

### Step 3: Install Pixi inside Ubuntu
Inside the Ubuntu terminal, install Pixi:
```bash
curl -fsSL https://pixi.sh/install.sh | bash
exec bash
```
*(Alternatively, close and reopen your Ubuntu terminal to ensure `pixi` is fully loaded into your PATH).*

### Step 4: Clone and Launch the Workspace
Clone the repository into your Linux home directory (avoid cloning directly onto Windows drive paths like `C:\` or `/mnt/c/` for optimal file I/O performance):
```bash
cd ~
git clone https://github.com/CoreBotics-Lab/pixi_ros2_lyricalLuthTemplate.git
cd pixi_ros2_lyricalLuthTemplate
pixi install
pixi shell
```

### Step 5: VS Code Integration (Recommended)
1. Install [VS Code](https://code.visualstudio.com/) on Windows.
2. Install the **WSL** extension (`ms-vscode-remote.remote-wsl`) from the VS Code Marketplace.
3. Inside your Ubuntu terminal, navigate to the template directory and launch VS Code:
   ```bash
   cd ~/pixi_ros2_lyricalLuthTemplate
   code .
   ```
   > **Tip:** If running `code .` outputs `command not found: code`, open VS Code on Windows, press `Ctrl+Shift+P` (or `F1`), select **WSL: Connect to WSL**, and open the folder `~/pixi_ros2_lyricalLuthTemplate`. This initializes the VS Code WSL server agent and registers the terminal shortcut permanently.

   VS Code will connect directly into your WSL2 Linux workspace with all template settings, Clangd, and Pyrefly tooling pre-configured. Graphical applications (RViz2, Gazebo) launched from the terminal will open seamlessly on your Windows desktop via WSLg.

---

## Testing the Setup

To verify that the ROS 2 daemon, middleware, and pub/sub communication are operating correctly, open two terminals and run:

**Terminal 1 (Publisher):**
```bash
cd pixi_ros2_lyricalLuthTemplate
pixi shell
ros2 run demo_nodes_py talker
```

**Terminal 2 (Subscriber):**
```bash
cd pixi_ros2_lyricalLuthTemplate
pixi shell
ros2 run demo_nodes_py listener
```

Alternatively, you can execute them directly without entering the shell using `pixi run`:

**Terminal 1 (Publisher):**
```bash
cd pixi_ros2_lyricalLuthTemplate
pixi run ros2 run demo_nodes_py talker
```

**Terminal 2 (Subscriber):**
```bash
cd pixi_ros2_lyricalLuthTemplate
pixi run ros2 run demo_nodes_py listener
```

---

## Switching to ROS 2 Jazzy

If your project requires ROS 2 Jazzy instead of Lyrical, perform the following two quick updates:

### 1. Update `pixi.toml`
- Change channel URL:
  ```toml
  channels = ["https://prefix.dev/robostack-jazzy", "conda-forge"]
  ```
- Replace package prefixes from `ros-lyrical-*` to `ros-jazzy-*`:
  ```toml
  [dependencies]
  python = "3.12.*"
  ros-jazzy-desktop = "*"
  ros-jazzy-xacro = "*"
  ros-jazzy-urdf = "*"
  ros-jazzy-tf-transformations = "*"
  ros-jazzy-slam-toolbox = "*"
  ros-jazzy-ros-gz-sim = "*"
  ros-jazzy-ros-gz-bridge = "*"
  ros-jazzy-robot-localization = "*"
  ros-jazzy-joint-state-publisher-gui = "*"
  ros-jazzy-nav2-lifecycle-manager = "*"
  ros-jazzy-rmw-cyclonedds-cpp = "*"
  ```

### 2. Update `.vscode/settings.json` and `pyrefly.toml`
- In `.vscode/settings.json`, set `"ros.distro": "jazzy"` and change Python paths from `3.14` to `3.12`.
- In `pyrefly.toml`, set `python-version = "3.12"` and search paths to `python3.12`.

### 3. Reinstall Environment
```bash
cd pixi_ros2_lyricalLuthTemplate
pixi install
```

---

## Workspace Structure

```text
├── .clangd                # Clangd language server flags and configuration
├── .vscode/               # Pre-configured tasks, launch profiles, and recommended extensions
├── bash_scripts/          # Build and autocompletion helper scripts
├── dds_config/            # Workspace CycloneDDS XML configuration
├── src/                   # Place your ROS 2 packages here
├── pixi.toml              # Pixi environment & dependencies specification
├── pixi.lock              # Reproducible dependency lockfile
└── pyrefly.toml           # Python type-checking configuration
```
