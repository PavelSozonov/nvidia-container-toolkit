# NVIDIA Container Toolkit Offline Installation Guide for RHEL 9.5

This guide provides step-by-step instructions to set up a local repository and install the NVIDIA Container Toolkit on a RHEL 9.5 server without internet access.

GPG key source: https://nvidia.github.io/libnvidia-container/gpgkey

## Prerequisites

- **Downloaded Packages**: Ensure all necessary RPM packages for the NVIDIA Container Toolkit are present in the `./repo` directory of this repository.
  
- **Repository Configuration File**: The `nvidia-container-toolkit.repo` file should be located in the root of this repository with the following content:

  ```
  [local-nvidia]
  name=Local NVIDIA Container Toolkit Repo
  baseurl=file:///var/nvidia-packages
  enabled=1
  gpgcheck=1
  gpgkey=file:///var/nvidia-packages/gpgkey
  obsolete=0
  ```

## Installation Steps

1. **Install createrepo tool** (if not already installed):
   ```
   sudo dnf install -y createrepo
   ```

2. **Transfer the Repository to the Target Server**:
   - Copy the entire repository to the RHEL 9.5 server. For example, transfer it to `/var/nvidia-packages`.

3. **Set the correct permissions**:
     ```
     sudo chmod -R 755 /var/nvidia-packages
     ```

4. **Create the local repository metadata**:
     ```
     cd /var/nvidia-packages
     sudo createrepo .
     ```

5. **Configure the Local Repository**:
   - Move the `nvidia-container-toolkit.repo` file to the Yum repository directory `/etc/yum.repos.d/`

6. **Clean and Rebuild the Yum Cache**:
   - Clear the existing Yum cache:
     ```
     sudo yum clean all
     ```
   - Rebuild the Yum cache to recognize the new local repository:
     ```
     sudo yum makecache
     ```

7. **Install the NVIDIA Container Toolkit**:
   - Install the toolkit using the local repository:
     ```
     sudo dnf install -y nvidia-container-toolkit
     ```

8. **Verify the Installation**:
   - After installation, verify that the NVIDIA Container Toolkit is installed correctly:
     ```
     nvidia-container-toolkit --version
     ```

## Notes

- **Local Repository Path**: The `baseurl` in the `nvidia-container-toolkit.repo` file is set to `/tmp/nvidia-packages`. If you choose a different directory, ensure you update this path accordingly in the `.repo` file.
  
- **Dependencies**: Ensure all dependencies for the NVIDIA Container Toolkit are included in the `repo` directory. If any dependencies are missing, you may need to download them separately and add them to the repository.
  
- **System Updates**: Since the server is offline, regular system updates won't be possible through standard repositories. Ensure that any critical updates or security patches are applied through other means.

By following these steps, you can successfully install the NVIDIA Container Toolkit on an offline RHEL 9.5 server using a local repository.
