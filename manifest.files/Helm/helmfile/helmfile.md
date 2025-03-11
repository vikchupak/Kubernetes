- https://gitlab.com/twn-devops-bootcamp/latest/10-kubernetes/helm-chart-microservices/-/blob/main/helmfile.yaml?ref_type=heads

`helmfile.yaml` - to deploy multiple charts at once (like compose file for docker)

Official sources
- https://github.com/helmfile/helmfile
- https://helmfile.readthedocs.io/en/latest/

## Installation

1. Find and download latest release asset with binary, https://github.com/helmfile/helmfile/releases
```bash
# Optional: cd to ~/Downloads directory to download to it
cd ~/Downloads
curl -L -O https://github.com/helmfile/helmfile/releases/download/v0.169.2/helmfile_0.169.2_linux_amd64.tar.gz
```

2. Unzip the archive .tar.gz
```bash
# Optional: create archive directory to extract files to it
mkdir helmfile_0.169.2_linux_amd64
tar -xzvf helmfile_0.169.2_linux_amd64.tar.gz -C ./helmfile_0.169.2_linux_amd64
```

3. Move binary to `/usr/local/bin/` or `/usr/bin/`
   - `/usr/bin/` - system-wide, for binaries installed by the system package manager
   - `/usr/local/bin/` - system-wide, for binaries manually installed by the system administrator
```bash
# it is important to move in order not to create soft/hard links to system-wide binary
sudo mv helmfile /usr/local/bin/
```

***Note: desired file permissions 755 (rwxr-xr-x)***

4. Check if it worked
```bash
which helmfile
ls -l /usr/local/bin/helmfile
helmfile --version
```

## Apply helmfile

Apply helmfile
```bash
# When in helmfile.yaml file folder
helmfile sync
```

List helmfile releases managed by helmfile
```bash
helmfile list
```

Remove all helmfile resources
```bash
helmfile destroy
```
