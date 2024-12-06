`helmfile.yaml` - to deploy multiple charts at once (like compose file in for docker)

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

3. Move binary to `/usr/bin/` or `/usr/local/bin/`
```bash
# it is important to move in order not to create soft/hard links to system-wide binary
sudo mv helmfile /usr/bin/
```

4. Check if it worked
```bash
which helmfile
ls -l /usr/bin/helmfile
helmfile --version
```
