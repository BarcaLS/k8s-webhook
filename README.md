# 🚀 Webhook Redeploy Script for Kubernetes

This project provides a lightweight HTTP webhook server built with `ash` and `socat`, designed to trigger safe and controlled rollout restarts of Kubernetes deployments.

## 🔧 Features

- Minimal footprint using Alpine `ash`, `jq`, `curl`, and `socat`
- Triggers rollout restarts via a simple HTTP POST request
- Automatically patches and restores `imagePullPolicy` to force image re-pull
- Supports deployment selection by `app` label or direct name match
- Includes detailed logging with timestamps
- Token-based authentication to restrict access
- Built-in HTTP server running on port `8080` using `socat`

## 🧩 Use Case

Ideal for CI/CD workflows or external systems where you need a simple and secure way to trigger a Kubernetes deployment restart through an HTTP call. You can use it, for example, with GitLab webhooks or any other automation tool.

## 📦 Deployment

This script is provided as a Kubernetes `ConfigMap` as part of a Helm chart. It is typically used alongside a lightweight container image (e.g., Alpine) that runs the script on startup.

### Example HTTP Request

```bash
curl -X POST http://<pod-ip>:8080/<TOKEN>/<NAMESPACE>/<APP>
```

- `<TOKEN>`: Secret token configured in your values file (`.Values.webhook.token`)
- `<NAMESPACE>`: Kubernetes namespace
- `<APP>`: `app` label or name of the deployment

## ✅ Behavior Overview

1. Parses the namespace and app from the request path.
2. Finds the deployment by label or direct match.
3. Temporarily patches all containers' `imagePullPolicy` to `Always`.
4. Applies a restart annotation to force rollout.
5. Waits for the rollout to complete.
6. Restores the original `imagePullPolicy` settings.

## ⚠️ Security Notes

- Only accepts `POST` requests with a valid token.
- Exposes port 8080 with no TLS — recommended to be used behind an ingress or service mesh with proper security controls.

## 📄 License

MIT License. Feel free to use, modify, and share.
