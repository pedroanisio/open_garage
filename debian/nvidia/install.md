
# NVIDIA linux driver + container toolkit

```sh
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

## Install

```sh
sudo apt-get update; sudo apt-get install -y nvidia-container-toolkit
```

```sh
sudo nvidia-ctk runtime configure --runtime=docker
```

```sh
sudo systemctl restart docker
```
## non-free non-free-firmware

```sh
 /etc/apt/sources.list
```

```sh
cat << EOF >> /etc/apt/sources.list
deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
EOF
```

```sh
apt update; apt install nvidia-driver -y
```

## Test

```sh
docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
```
## Reference
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html

