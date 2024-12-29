# AI_tools
Lazy installation for running local AI on my laptop Lenovo Legion Ryzen 5600, RTX3070, linux debian based (ubuntu)


## Installation


### Update system libraries

```bash
sudo apt update && sudo apt upgrade
```


### Download ollama installer, and execute it
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

  Ollama is a runner for AI models. It is used like docker. You type `ollama run <model_name>` and like docker, it downloads the model and execute it on your PC. If you're familiar with hub.docker.com, the equivalent exists on ollama. The site is `https://ollama.com/search`, you will find many AI models. There are usually several sizes for each model, so you'll need to download the size that fits your graphic card memory, or your system memory
  
---------------------------
### Install docker (several steps)
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$UBUNTU_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Create docker group
```bash
sudo groupadd docker
```


### Add current user to the docker group

```bash
sudo usermod -aG docker $USER
```

### In order to avoid logoff and login again, join the group
```bash
newgrp docker
```


### install NVIDIA drivers for working thru docker
```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey |sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
&& curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
&& sudo apt-get update
```

### install the nvidia bouzin
```bash
sudo apt-get install -y nvidia-container-toolkit
```

### configure docker to use nvidia bouzin
```bash
sudo nvidia-ctk runtime configure --runtime=docker
```

### Get an AI model
Let's get qwen2, an excellent models for coding tasks
```bash
ollama run qwen2.5-coder:32b
```
we get the 32b version because its size is 18GB. 


### Run ollama + OpenWebUI on the same machine
* should be available on http://localhost:3000
* uses NVIDIA first, and when not enough graphic memory available, it will use system memory and switch to CPUs 
```bash
docker run  --network=host --gpus all-v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 --name open-webui --restart always ghcr.io/open-webui/open-webui:cuda`
``` 
The first user is admin.


-----------------
### if suspend or resume makes AI failing:
in some cases, when the PC has been in suspend/resume, the GPUs cannot be used anymore (ollama will run with CPU info, which is way more slower) so you will have to run this command manually
```bash
sudo rmmod nvidia_uvm && sudo modprobe nvidia_uvm
```
