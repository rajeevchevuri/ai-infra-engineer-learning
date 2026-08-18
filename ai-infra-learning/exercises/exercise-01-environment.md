Run the verification script and ensure all tools are installed

#Activate env using ai-activate

#!/bin/bash
# save as verify-setup.sh

echo "=== Verifying ML Infrastructure Development Environment ==="
echo ""

# Python
echo "Python Version:"
python --version || echo "❌ Python not found"
echo ""

# Pip packages
echo "Key Python Packages:"
python -c "import torch; print(f'PyTorch: {torch.__version__}')" || echo "❌ PyTorch not installed"
python -c "import tensorflow as tf; print(f'TensorFlow: {tf.__version__}')" || echo "❌ TensorFlow not installed"
python -c "import fastapi; print(f'FastAPI: {fastapi.__version__}')" || echo "❌ FastAPI not installed"
echo ""

# Git
echo "Git Version:"
git --version || echo "❌ Git not found"
echo ""

# Docker
echo "Docker Version:"
docker --version || echo "❌ Docker not found"
docker ps > /dev/null 2>&1 && echo "✅ Docker running" || echo "⚠️  Docker not running (start Docker Desktop)"
echo ""

# Kubernetes
echo "Kubernetes Tools:"
kubectl version --client --short || echo "❌ kubectl not found"
minikube version --short || echo "❌ minikube not found"
helm version --short || echo "❌ helm not found"
echo ""

# Cloud CLI (at least one)
echo "Cloud CLIs:"
aws --version 2>/dev/null && echo "✅ AWS CLI installed" || echo "⚠️  AWS CLI not installed"
gcloud --version 2>/dev/null && echo "✅ GCloud CLI installed" || echo "⚠️  GCloud CLI not installed"
az --version 2>/dev/null && echo "✅ Azure CLI installed" || echo "⚠️  Azure CLI not installed"
echo ""

echo "=== Verification Complete ==="
echo "If you see ❌ errors, review the installation steps for those tools."

Create a simple Python script that imports PyTorch and TensorFlow
# save as import_packages.py
import torch
import tensorflow as tf
print(torch.__version__)
print(tf.__version__)


Run a Docker container and verify it works
docker run hello-world
docker ps


