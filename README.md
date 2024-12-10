# EPICS Base and Modules Installation and Deployment Guide for Ubuntu 22.04 (Using EPICS-env)

## Table of Contents

1. [Introduction](#1-introduction)
2. [Prerequisites](#2-prerequisites)
   - [Operating System Requirements](#operating-system-requirements)
   - [Required Software and Tools](#required-software-and-tools)
3. [Installing EPICS Base Using EPICS-env](#3-installing-epics-base-using-epics-env)
4. [Installing EPICS Modules Using EPICS-env](#4-installing-epics-modules-using-epics-env)
5. [User Permissions and Directory Structure](#5-user-permissions-and-directory-structure)
6. [Setting Up the Environment](#6-setting-up-the-environment)
7. [Testing the Installation](#7-testing-the-installation)
8. [Troubleshooting](#8-troubleshooting)
9. [Further Resources](#9-further-resources)

---

## 1. Introduction

**EPICS** (Experimental Physics and Industrial Control System) is a software framework used for building control systems in scientific environments. This guide provides step-by-step instructions to install **EPICS Base** and its modules on **Ubuntu 22.04**, using the **EPICS-env** project, which automates the installation and deployment process.

---

## 2. Prerequisites

### Operating System Requirements

This guide is designed for **Ubuntu 22.04 LTS** (Jammy). Other versions of Ubuntu or Debian-based distributions may work with minor modifications.

### Required Software and Tools

Before proceeding, ensure that the following tools are installed:

- **GNU Compiler Collection (GCC)** (or another C/C++ compiler)
- **make** (build tool)
- **git** (for cloning repositories)
- **perl** (required by some EPICS modules)
- **python3** (required for certain modules)
- **python3-numpy** (required for some modules)
- **python-is-python3** (ensures that `python` commands use Python 3.x)
- **tcl** (optional, depending on modules)
- **python3-dev**, **python3-setuptools**, **build-essential**
- **cmake** (required for some modules)
- **re2c** (required for some modules)

To install the necessary dependencies, run:

```bash
sudo apt update
sudo apt install build-essential python3-dev python3-setuptools git perl python3 tcl cmake re2c python3-numpy python-is-python3 autoconf libusb-1.0-0-dev libtool libsnmp-dev libevent-dev libssh2-1-dev 
```

---

## 3. Installing EPICS Base Using EPICS-env

The **EPICS-env** project simplifies the process of downloading, building, and installing **EPICS Base** and associated modules.

### Cloning the EPICS-env Repository

Start by cloning the **EPICS-env** repository to `/tmp/EPICS-env`:

```bash
git clone https://github.com/jeonghanlee/EPICS-env.git /tmp/EPICS-env
```

### Create the Installation Directory

Before installing **EPICS Base**, create the installation directory **`/usr/local/epics`**:

```bash
sudo mkdir -p /usr/local/epics
```

This command ensures the directory is created if it doesn't already exist.

### Set Permissions for the Directory

Make sure the user performing the installation has the necessary write permissions to the installation directory. Replace `user` with your actual username:

```bash
sudo chown -R user:user /usr/local/epics
```

Alternatively, to grant access to all users for reading, while only the admin has write access:

```bash
sudo chmod -R 755 /usr/local/epics
```

### Configuring the Installation Path

By default, **EPICS-env** installs **EPICS Base** into the user's **home directory** (`~/epics`). If you want to install **EPICS Base** elsewhere, such as in `/usr/local/epics`, you need to create a **`CONFIG_SITE.local`** file to specify the installation path.

1. Navigate to the **`configure`** directory inside the EPICS-env repository:

   ```bash
   cd /tmp/EPICS-env/configure
   ```

2. Create the **`CONFIG_SITE.local`** file with the custom installation path using the `echo` command:

   ```bash
   echo "INSTALL_LOCATION=/usr/local/epics" > CONFIG_SITE.local
   ```

   This ensures that **EPICS Base** will be installed into **`/usr/local/epics`**.

---

### Running the EPICS Base Build Process

After configuring the installation path, run the following commands to build and install **EPICS Base**:

1. Navigate back to the **EPICS-env** directory:

   ```bash
   cd /tmp/EPICS-env
   ```

2. Run the build commands:

   ```bash
   make init.base         # Initializes the base build process
   make conf.base         # Configures the base build
   make conf.base.show    # Displays the configuration
   make patch.base        # Applies any necessary patches
   make build.base        # Builds EPICS Base
   make install.base      # Installs EPICS Base into /usr/local/epics
   make exist             # Verifies the installation
   ```

   **Note**: The **EPICS Base** will be installed under **`/usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/base`**, with the architecture and EPICS version automatically appended.

---

## 4. Installing EPICS Modules Using EPICS-env

Once **EPICS Base** is installed, you can proceed to install **EPICS modules**.

### Install the `uldaq-env` Dependency

The **`uldaq-env`** project provides support for **ULDAQ** hardware integration within EPICS. Follow these steps to install it:

1. **Clone the `uldaq-env` repository** to the appropriate directory:

   ```bash
   git clone https://github.com/jeonghanlee/uldaq-env.git /tmp/uldaq-env
   ```

2. **Navigate to the `uldaq-env` directory**:

   ```bash
   cd /tmp/uldaq-env
   ```

3. **Run the following commands** to build and install the **`uldaq-env`**:

   ```bash
   make init          # Initializes the ULDAQ environment
   make conf          # Configures the ULDAQ environment
   make build         # Builds the ULDAQ environment
   make install       # Installs the ULDAQ environment into /usr/local
   ```

   **Note**: These commands will install the **`uldaq-env`** dependency to the default location (`/usr/local/`), which is appropriate for system-wide installations.

### Set the EPICS_BASE Environment Variable

After installing **EPICS Base** and **`uldaq-env`**, you need to set the `EPICS_BASE` environment variable to point to the correct EPICS Base path. Add the following line to your `.bashrc` or other relevant shell configuration file:

```bash
export EPICS_BASE=/usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/base
export EPICS_HOST_ARCH=linux-x86_64    # Adjust based on your architecture
```

After modifying your profile, remember to **source it** to apply the changes:

```bash
source ~/.bashrc
```

### Build and Install the EPICS Modules

Now you can proceed with the build and installation of **EPICS modules**:

1. Initialize the module build process:

   ```bash
   cd /tmp/EPICS-env
   make init.modules        # Initializes the module build process
   ```

2. Configure the module build process:

   ```bash
   make conf.modules        # Configures the module build process
   ```

3. (Optional) Clean previous builds:

   ```bash
   make clean.modules       # Cleans previous builds (optional)
   ```

4. Build the EPICS modules:

   ```bash
   make build.modules       # Builds the EPICS modules
   ```

5. Install the modules into `/usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/modules`:

   ```bash
   make install.modules     # Installs the modules
   ```

6. Make symbolic links into `/usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/modules`:

   ```bash
   make symlinks.modules     # Creates symlinks
   ```

7. Verify the installation:

   ```bash
   make exist.modules       # Verifies the module installation
   ```

---

## 5. User Permissions and Directory Structure

### Directory Structure

Once installed, the directory structure will look like this:

- **Global installation**: `/usr/local/epics`
  - `/usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/base`: EPICS Base directory
  - `/usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/modules`: Installed EPICS modules

### User Permissions

For **global installations** under `/usr/local/epics`, ensure that users who need access have appropriate permissions:

```bash
sudo chmod -R 755 /usr/local/epics
```

To give write access to specific users or groups:

```bash
sudo chown -R user:group /usr/local/epics  # Replace with the correct user and group
```
---

## 6. Setting Up the Environment

To ensure that the system can locate **EPICS Base** and its modules, you need to set up environment variables.

### Set Environment Variables

Add the following lines to your shell profile (`~/.bashrc` or `~/.bash_profile`):

```bash
export EPICS_BASE=/usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/base
export EPICS_HOST_ARCH=linux-x86_64    # Adjust for your architecture
export PATH=$PATH:$EPICS_BASE/bin/$EPICS_HOST_ARCH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$EPICS_BASE/lib/$EPICS_HOST_ARCH
```

Then, reload your profile:

```bash
source ~/.bashrc
```

This ensures that **EPICS Base** and its modules will be found by the system.

---

## 7. Testing the Installation

To verify that the installation of **EPICS Base** and its modules was successful, you can check if the relevant binaries and libraries are present on the system.

### Check EPICS Base Binaries

1. Verify that the EPICS Base binaries are present by listing the files in the `bin` directory:

   ```bash
   ls /usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/base/bin/$EPICS_HOST_ARCH
   ```

   You should see binaries like `softIoc`, `camonitor`, and other EPICS tools.

### Check EPICS Libraries

1. Verify that the EPICS libraries are present by listing the files in the `lib` directory:

   ```bash
   ls /usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/base/lib/$EPICS_HOST_ARCH
   ```

   You should see `.so` files (shared libraries), such as `libca.so` (for Channel Access) and `libdb.so` (for the database).

### Check Installed Modules

For the installed modules, you can check if the module directories exist and if their binaries and libraries are present.

1. List the contents of the modules directory to see which modules were installed:

   ```bash
   ls /usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/modules
   ```

   You should see directories for the installed modules (e.g., `motor`, `sscan`).

2. For each installed module, check if the corresponding binaries and libraries are present under the module's `bin` and `lib` subdirectories. For example:

   ```bash
   ls /usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/modules/asyn/bin/$EPICS_HOST_ARCH
   ls /usr/local/epics/1.1.0/ubuntu-22.04/7.0.7/modules/asyn/lib/$EPICS_HOST_ARCH
   ```

   You should see module-specific binaries and shared libraries.

### Summary

If the correct binaries and libraries are present, this confirms that **EPICS Base** and the installed modules are correctly set up and functional.

---

## 8. Troubleshooting

If you encounter errors during the installation, check the following:

- Ensure all dependencies are installed.
- Check that the **`/usr/local/epics`** directory has the correct permissions.
- Make sure the `EPICS_BASE` and `EPICS_HOST_ARCH` environment variables are correctly set.

---

## 9. Further Resources

- [EPICS Documentation](https://epics.anl.gov)
- [EPICS Base GitHub Repository](https://github.com/epics-base/epics-base)
- [EPICS Modules GitHub Repository](https://github.com/epics-modules)
