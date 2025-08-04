# OpenShift NGINX App Deployment with CodeReady Containers

This project walks through deploying a simple NGINX application on a single-node OpenShift cluster using **CodeReady Containers (CRC)**. It uses YAML manifests to create a deployment, expose a service, and route external traffic.

---

## 📌 Project Goals

✅ Deploy a local OpenShift 4.x cluster using CRC  
✅ Use the OpenShift CLI (`oc`) to interact with the cluster  
✅ Deploy a basic NGINX application via YAML manifests  
✅ Expose the application using OpenShift Routes  
✅ Build portfolio documentation with screenshots and clear steps

---

## 🧰 Tools Used

- CodeReady Containers (CRC)
- OpenShift 4.x Web Console and CLI (`oc`)
- NGINX Docker image
- YAML Manifests
- GitHub (for documentation and versioning)

---

## 🖥️ Local CRC Cluster Setup

Minimum specs:
- 4 vCPU, 8+ GB RAM, 35+ GB disk space
- RHEL 9, CentOS 9 Stream, or Fedora

### 🔽 Install and Start CRC

```bash
tar -xvf crc-linux-*.tar.xz
sudo mv crc-linux-*/crc /usr/local/bin/
crc setup
crc start
```

📸 *Screenshot: Terminal showing `crc start` output (console URL + credentials)*

---

## 🔐 Login to OpenShift

```bash
oc login --token=<your_token> --server=https://api.crc.testing:6443
```

📸 *Screenshot: CLI or web login confirmation*

---

## 🆕 Create a Project

```bash
oc new-project dev
```

📸 *Screenshot: Output of `oc new-project dev`*

---

## 📦 Deploy the NGINX App

### `manifests/nginx-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

```bash
oc apply -f manifests/nginx-deployment.yaml
```

📸 *Screenshot: Output of `oc get pods` showing nginx pod in Running state*

---

## 🔧 Create ClusterIP Service

### `manifests/nginx-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

```bash
oc apply -f manifests/nginx-service.yaml
```

📸 *Screenshot: Output of `oc get svc`*

---

## 🌍 Expose the Route

### `manifests/nginx-route.yaml`

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: nginx
spec:
  to:
    kind: Service
    name: nginx
  port:
    targetPort: 80
```

```bash
oc apply -f manifests/nginx-route.yaml
oc get route
```

📸 *Screenshot: Output showing route URL like `http://nginx-dev.apps-crc.testing`*

---

## 🔎 Access in Browser

- Paste the route URL in your browser.
- You should see the NGINX welcome page.

📸 *Screenshot: Browser showing NGINX*

---

## 🛠️ Troubleshooting Tips

- If the NGINX image fails to pull:  
  ➤ Use a different tag (`nginx:1.25-alpine`) or `oc logs <pod>` to diagnose.

- If the pod won't start:  
  ➤ Check `oc describe pod <pod>` for Events section.

- Route not accessible?  
  ➤ Ensure the pod is running and the route exists (`oc get route`).

---

## 📂 Folder Structure

```text
basic-openshift-deployment/
├── README.md
├── manifests/
│   ├── nginx-deployment.yaml
│   ├── nginx-service.yaml
│   └── nginx-route.yaml
├── screenshots/
│   ├── crc-start.png
│   ├── oc-login.png
│   ├── project-created.png
│   ├── pod-running.png
│   ├── route-exposed.png
│   └── nginx-browser.png
```

---

## 📘 Lessons Learned

- How to use `oc` CLI for project and resource management
- Deploying apps using YAML manifests
- Using OpenShift Routes for external access
- Basic troubleshooting of OpenShift deployments

---

## 🧪 How to Reproduce

1. Install and start CRC on a supported OS  
2. Login to OpenShift with `oc login`  
3. Create a project: `oc new-project dev`  
4. Apply all YAML files in the `manifests/` folder  
5. Visit the exposed route in your browser  
6. Reference screenshots for visual confirmation

---
