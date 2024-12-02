
# Running Ollama with Docker

## 1. Install Docker

If Docker isn’t installed on your system:

- **For Linux**: Follow the [Docker installation guide for Linux](https://docs.docker.com/engine/install/).
- **For macOS/Windows**: Install Docker Desktop from [Docker's official website](https://www.docker.com/get-started/).

Verify Docker installation:

```bash
docker --version
```

---

## 2. Pull the Ollama Docker Image

Download the Ollama Docker image to get started:

```bash
docker pull ollama/ollama
```

This command pulls the latest version of the Ollama image.

---

## 3. Run the Container

You can start the container in different modes depending on your hardware:

### For CPU-only Systems

```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

- `-d`: Runs the container in detached mode.
- `-v ollama:/root/.ollama`: Mounts a volume to persist model data.
- `-p 11434:11434`: Maps the container's API port to the host.
- `--name ollama`: Names the container `ollama`.

### For NVIDIA GPU Acceleration

1. Install NVIDIA Container Toolkit:

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey     | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list     | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g'     | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
```

2. Configure Docker to Use NVIDIA Runtime:

```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

3. Run the Container:

```bash
docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

### For AMD GPU Acceleration

Use the `rocm` tag and include AMD GPU device access:

```bash
docker run -d --device /dev/kfd --device /dev/dri -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama:rocm
```

---

## 4. Run a Model Locally

Once the container is running, you can execute commands to interact with the model.

### Start an Interactive Session:

```bash
docker exec -it ollama ollama run llama3
```

### Pull a Specific Model:

To download and use a model like `llama3.2:3b`:

```bash
docker exec -it ollama ollama pull llama3.2:3b
```

### Test the Model:

Run the model with a prompt:

```bash
docker exec -it ollama ollama run llama3.2:3b
```

---

## 5. Access Models via API

Ollama provides an API for external applications. You can send requests to the API running on port `11434`.

Example using `curl`:

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2:3b",
  "prompt": "Suggest a recipe using chicken, garlic, and rice."
}'
```

---

## 6. Stop and Manage the Container

### To stop the running container:

```bash
docker stop ollama
```

### To restart the container:

```bash
docker start ollama
```

### To remove the container:

```bash
docker rm ollama
```

---

## Additional Resources

Visit the [Ollama Library](https://ollama.com) to explore more models and capabilities.
