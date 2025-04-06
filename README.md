# NVIDIA Container Toolkit Offline Installation Guide for RHEL 9.5

This guide provides complete instructions for setting up a local repository and installing the NVIDIA Container Toolkit on an offline RHEL 9.5 server.

GPG key source: https://nvidia.github.io/libnvidia-container/gpgkey

## Prerequisites

- A machine with internet access (for initial package download)
- Docker installed on the download machine
- Sufficient disk space for the packages (approximately 500MB)

## Step 1: Download Packages from Online Machine

1. Create a directory for the packages:
   ```
   mkdir -p ./repo
   ```

2. Download the NVIDIA GPG key:
   ```
   curl -s -L https://nvidia.github.io/libnvidia-container/gpgkey -o ./repo/gpgkey
   ```

3. Use a RHEL UBI container to download packages with proper dependencies:
   ```
   docker run --rm -it --platform linux/amd64 -v ./repo:/packages registry.access.redhat.com/ubi9/ubi bash
   ```

4. Inside the container, set up the repository and download packages:
   ```
   curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo \
     -o /etc/yum.repos.d/nvidia-container-toolkit.repo
   dnf install -y dnf-plugins-core
   dnf download --resolve --downloaddir=/packages nvidia-container-toolkit
   exit
   ```

5. Verify the downloaded packages and GPG key:
   ```
   ls -lh ./repo/
   ```

## Step 2: Prepare the Offline Repository

1. Create the repository configuration file `nvidia-container-toolkit.repo`:
   ```
   [local-nvidia]
   name=Local NVIDIA Container Toolkit Repo
   baseurl=file:///var/nvidia-packages
   enabled=1
   gpgcheck=1
   gpgkey=file:///var/nvidia-packages/gpgkey
   obsolete=0
   ```

2. Transfer the entire directory (including `repo` folder with packages, GPG key, and `.repo` file) to the offline server.

## Step 3: Install on Offline Server

1. Install required tools:
   ```
   sudo dnf install -y createrepo
   ```

2. Set up the local repository:
   ```
   sudo mv ./repo /var/nvidia-packages
   sudo chmod -R 755 /var/nvidia-packages
   cd /var/nvidia-packages
   sudo createrepo .
   ```

3. Configure the repository:
   ```
   sudo mv nvidia-container-toolkit.repo /etc/yum.repos.d/
   sudo dnf clean all
   sudo dnf makecache
   ```

4. Install the toolkit:
   ```
   sudo dnf install -y nvidia-container-toolkit
   ```

5. Verify installation:
   ```
   nvidia-container-toolkit --version
   ```

## GPG Key Verification Notes

- **gpgcheck=1**: Enables package signature verification for security
- **gpgkey**: Points to the local GPG key file for verification
- The key is downloaded from NVIDIA's official source during Step 1
- Signature verification ensures package integrity and authenticity

## Repository Configuration Notes

- **obsolete=0**: Prevents automatic package obsoletion, ensuring stable offline operation
- **baseurl**: Uses /var/nvidia-packages for persistent storage (better than /tmp)
- All paths must match the actual location on your system

## Troubleshooting

- If GPG verification fails:
  ```
  sudo rpm --import /var/nvidia-packages/gpgkey
  ```
- Check dependencies with:
  ```
  sudo dnf deplist nvidia-container-toolkit
  ```
- Verify repository metadata:
  ```
  ls -l /var/nvidia-packages/repodata/
  ```

## Maintenance

To update packages in the future:
1. Repeat the download process on an online machine
2. Transfer new packages and updated GPG key to the offline server
3. Regenerate repository metadata:
   ```
   cd /var/nvidia-packages
   sudo createrepo --update .
   ```
