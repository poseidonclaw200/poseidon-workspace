# sysinfo - Interactive System Monitor

A lightweight, interactive Python app to view system information on any Linux machine.

## Features

- **Overview**: System basics (OS, uptime, hostname, CPU cores, memory)
- **CPU**: Per-core usage with visual bars, frequency, core count
- **Memory**: RAM and swap usage with visual bars
- **Disk**: Partition usage for all mounted filesystems
- **Network**: Interface info and I/O statistics
- **Processes**: Top processes by memory and CPU usage
- **Interactive**: Navigate with number keys, smooth TUI with colors

## Requirements

- Python 3.6+
- `psutil` (installed automatically)
- Linux OS
- Terminal with color support

## Installation

### Option 1: Quick Install (Recommended)
```bash
chmod +x install_sysinfo.sh
./install_sysinfo.sh
```

Then run from anywhere:
```bash
sysinfo
```

### Option 2: Manual Install
```bash
pip3 install psutil
chmod +x sysinfo.py
python3 sysinfo.py
```

### Option 3: No Install (Just Run)
```bash
pip3 install psutil
python3 sysinfo.py
```

## Usage

```
Press keys to navigate:
  1 - Overview
  2 - CPU info
  3 - Memory info
  4 - Disk usage
  5 - Network info
  6 - Top processes
  q - Quit
```

## File Structure

```
sysinfo.py          - Main app (standalone, ~350 lines)
install_sysinfo.sh  - Installer script
README.md           - This file
```

## How It Works

- Uses `psutil` for cross-platform system info
- Renders TUI with `curses` (built-in Python)
- Updates data on each key press (no lag)
- Works on any Linux distro (Ubuntu, Debian, CentOS, Fedora, etc.)

## Troubleshooting

**"psutil not found"**
```bash
pip3 install psutil
```

**"Command not found" after install**
Make sure `/usr/local/bin` is in your PATH:
```bash
echo $PATH | grep /usr/local/bin
```

**Colors not showing**
Try: `export TERM=xterm-256color`

## License

Free to use and modify. Enjoy! 🔱
