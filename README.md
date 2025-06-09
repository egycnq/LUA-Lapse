# LUA+Lapse - PS4 Post-Exploitation Framework

A fork of [remote_lua_loader](https://github.com/shahrilnet/remote_lua_loader) with advanced post-exploitation capabilities for PS4 firmware 11.00 and 12.02.

> ⚠️ **DISCLAIMER**: This project is for educational and research purposes only. Use at your own risk.

> 💡 **Note**: If you do not want to dump your kernel, just do not insert a USB device and you can use this exploit for full FTP access only.

## 🎯 What We've Achieved

This project implements several advanced kernel exploitation techniques:

### 1. **Sandbox Escape** 🔓

- Breaks out of the PS4's BSD jail restriction
- Grants root filesystem access
- Modifies process credentials to achieve elevated privileges
- Enables access to previously restricted system areas

### 2. **Kernel Base (KBASE) Discovery** 🔍

- Implements both static and dynamic methods to find kernel base address
- Uses EVF (Event Flag) string leak for KBASE calculation

### 3. **Kernel ELF Dumper** 💾

- Dumps the entire PS4 kernel to USB storage
- Supports large kernel dumps with progress tracking
- Implements chunked reading (16KB blocks) for stability
- Includes real-time progress notifications

### 4. **Full FTP Access** 📁

- After running this exploit, the game process is jailbroken
- You can use `ftp_server.lua` from the original remote_lua_loader repo
- Provides full filesystem access via FTP
- No more restrictions on file browsing and transfers

## 📊 Supported Firmware

| Firmware | Status    |
| -------- | --------- |
| 11.00    | ✅ Tested |
| 12.02    | ✅ Tested |
| 9.00     |🧪 Under Testing |

"Thanks to n0llptr for adding 9.00 offsets"

> 🚧 **Work in Progress**

## 🎮 How to Use

### Prerequisites

1. **A PS4 with firmware 11.00 or 12.02**
2. **A supported LUA game** (see [remote_lua_loader](https://github.com/shahrilnet/remote_lua_loader) for list)
3. **Remote LUA Loader** setup on your PC

### Steps

1. Follow the setup instructions from [remote_lua_loader](https://github.com/shahrilnet/remote_lua_loader)
2. Launch your LUA-supported game on PS4
3. Connect using the remote_lua_loader on your PC
4. Send our modified `lapse.lua` file to your PS4
5. The exploit will run automatically

### After Exploitation

- Your game process is now jailbroken
- You can send `ftp_server.lua` from the original repo for full FTP access
- USB kernel dumps will be saved to `/mnt/usb0/kernel.elf`

## 🔧 Technical Details

### EVF (Event Flag) Constant

The EVF constant is a kernel string that can be leaked through various vulnerabilities. We use this leak to calculate the kernel base address:

```
KBASE = leaked_evf_pointer - firmware_specific_evf_offset
```

### Finding KBASE for Other Firmwares

The code includes a brute-force scanner that:

1. Aligns addresses to page boundaries (0x1000)
2. Scans backwards from the leaked pointer
3. Validates findings using:
   - ELF header magic bytes
   - Target ID validation

### Sandbox Escape Process

1. **Credential Modification**: Zeros out uid/gid fields in `p_ucred`
2. **Jail Breaking**: Updates process prison pointer to `prison0`
3. **Root Access**: Replaces jail/root directory vnodes with system root vnode

## 🚀 Features

- **Automatic Firmware Detection**: Detects running firmware and applies appropriate offsets
- **Robust Error Handling**: Validates operations at each step
- **Progress Notifications**: Real-time updates during kernel dumping
- **USB Dump Support**: Saves kernel dump to `/mnt/usb0/kernel.elf`
- **FTP Compatibility**: Jailbroken process allows full FTP server functionality

## This project demonstrates:

- Kernel memory manipulation techniques
- Process privilege escalation methods
- ELF format parsing and validation
- Memory scanning algorithms
- Filesystem jailbreak techniques

## ⚡ Technical Implementation

### Key Functions

1. **`calculate_kbase()`** - Static KBASE calculation using known offsets
2. **`find_kbase()`** - Dynamic KBASE discovery with validation
3. **`escape_sandbox()`** - Implements the jailbreak sequence
4. **`dump_kernel_elf()`** - Handles kernel dumping with progress tracking
5. **`get_kernel_elf_size()`** - Parses ELF headers to determine dump size

### Memory Layout Understanding

The exploit relies on understanding PS4's kernel memory layout:

- Process structures (`curproc`)
- Credential structures (`ucred`)
- File descriptor tables
- Vnode references

## ⚠️ Important Notes

- **USB Required**: Kernel dumping requires a FAT32/exFAT formatted USB drive
- **Stability**: This is experimental code - expect potential crashes

## 📝 Credits

- Original [remote_lua_loader](https://github.com/shahrilnet/remote_lua_loader) by shahrilnet
- flatz
- null_ptr
- specter
- chendo
- EchoStretch
- al-azif
- and Everyone who contributed offset documentation

## 📜 License

This project is provided as-is for educational purposes. See the original remote_lua_loader repository for base licensing terms.
