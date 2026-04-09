# eSim Ubuntu 25.04 (Plucky Puffin) Patch Notes

During the installation of eSim on a headless Ubuntu 25.04 Azure VM, 6 specific compatibility issues were identified and patched. This is the documentation for all fixes applied.

## 1. OS Version Recognition
* **Issue:** The installer did not recognize Ubuntu 25.04, causing an immediate exit.
* **Fix:** Updated the OS detection logic in `install-eSim.sh` to route `25.04` through the supported `24.04` installation pathway.

## 2. KiCad PPA 404 Error Bypass
* **Issue:** The official KiCad PPA does not have a release for "Plucky" yet, causing `apt update` to fail with a 404 error.
* **Fix:** Added an OS-check to bypass the `add-apt-repository` command for 25.04, relying instead on native Ubuntu system repositories for the KiCad installation.

## 3. Extraction Guard (Preserving Patches)
* **Issue:** The NGHdl setup scripts run `unzip -o nghdl.zip` automatically, which overwrites any manual patches made to the source code prior to compilation.
* **Fix:** Commented out the `unzip -o` command in the sub-scripts to preserve manual OS-level source patching.

## 4. Deprecated GTK Dependency
* **Issue:** The `libcanberra-gtk-module` package is deprecated and unavailable in Ubuntu 25.04 package lists.
* **Fix:** Updated the dependency package name to `libcanberra-gtk3-module` in the installation script.

## 5. The LLVM 20.1 / GHDL Compiler Patch & Build Guard
* **Issue:** GHDL 4.1.0 fails to configure because Ubuntu 25.04 ships with the `LLVM 20.1.2` toolchain, triggering an "Unhandled version" error. Furthermore, the automated build script overwrites manual source code patches during execution.
* **Fixes Applied (in `installGHDL` function):**
  1. **Version Bypass:** Manually patched the GHDL `configure` script to accept LLVM version `20.1`.
  2. **Extraction Guard:** Commented out `# tar xvf $ghdl.tar.gz` to prevent the installer from overwriting the manually patched source code with a fresh archive.
  3. **Pathing Guard:** Commented out `# cd $ghdl/` to maintain the correct working directory after bypassing the extraction phase.
  4. **Configure Guard:** Commented out `# ./configure --with-llvm-config=/usr/bin/llvm-config` because the patched configuration was executed manually. This allows the script to proceed directly to the `make` and `make install` build steps.

## 6. Headless Environment Desktop Fix
* **Issue:** Headless cloud VMs (like Azure) often lack a default `~/Desktop` directory. This causes the final installation step (copying the `esim.desktop` shortcut) to throw a fatal error and crash the script.
* **Fix:** Added a `mkdir -p ~/Desktop` command immediately prior to the shortcut copy process to ensure the destination directory exists.
