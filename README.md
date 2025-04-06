# NVIDIA Container Toolkit Offline Installation Guide for RHEL 9.5

This guide provides step-by-step instructions to set up a local repository and install the NVIDIA Container Toolkit on a RHEL 9.5 server without internet access.

## Prerequisites

- **Downloaded Packages**: Ensure all necessary RPM packages for the NVIDIA Container Toolkit are present in the `./repo` directory of this repository.
  
- **Repository Configuration File**: The `nvidia-container-toolkit.repo` file should be located in the root of this repository with the following content:

  ```
  [local-nvidia]
  name=Local NVIDIA Container Toolkit Repo
  baseurl=file:///tmp/nvidia-packages
  enabled=1
  gpgcheck=0
  ```

## Installation Steps

1. **Transfer the Repository to the Target Server**:
   - Copy the entire repository to the RHEL 9.5 server. For example, transfer it to `/opt/nvidia-container-toolkit`.

2. **Move Repository Files to the Appropriate Directory**:
   - On the RHEL server, move the `repo` directory to `/tmp/nvidia-packages`:
     ```
     sudo mv /opt/nvidia-container-toolkit/repo /tmp/nvidia-packages
     ```
   - Set the correct permissions:
     ```
     sudo chmod -R 755 /tmp/nvidia-packages
     ```

3. **Configure the Local Repository**:
   - Move the `nvidia-container-toolkit.repo` file to the Yum repository directory:
     ```
     sudo mv /opt/nvidia-container-toolkit/nvidia-container-toolkit.repo /etc/yum.repos.d/
     ```

4. **Clean and Rebuild the Yum Cache**:
   - Clear the existing Yum cache:
     ```
     sudo yum clean all
     ```
   - Rebuild the Yum cache to recognize the new local repository:
     ```
     sudo yum makecache
     ```

5. **Install the NVIDIA Container Toolkit**:
   - Install the toolkit using the local repository:
     ```
     sudo yum install nvidia-container-toolkit
     ```

6. **Verify the Installation**:
   - After installation, verify that the NVIDIA Container Toolkit is installed correctly:
     ```
     nvidia-container-toolkit --version
     ```
   - Additionally, check the status:
     ```
     sudo systemctl status nvidia-container-toolkit
     ```

## Notes

- **Local Repository Path**: The `baseurl` in the `nvidia-container-toolkit.repo` file is set to `/tmp/nvidia-packages`. If you choose a different directory, ensure you update this path accordingly in the `.repo` file.
  
- **Dependencies**: Ensure all dependencies for the NVIDIA Container Toolkit are included in the `repo` directory. If any dependencies are missing, you may need to download them separately and add them to the repository.
  
- **System Updates**: Since the server is offline, regular system updates won't be possible through standard repositories. Ensure that any critical updates or security patches are applied through other means.

By following these steps, you can successfully install the NVIDIA Container Toolkit on an offline RHEL 9.5 server using a local repository.
