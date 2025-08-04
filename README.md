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

<img width="826" height="969" alt="crc-start" src="https://github.com/user-attachments/assets/737f2ac0-aa4d-4fea-9357-be9cbc1449f7" />


---

## 🔐 Login to OpenShift

```bash
oc login --token=<your_token> --server=https://api.crc.testing:6443
```

<img width="865" height="190" alt="oc-login" src="https://github.com/user-attachments/assets/ffc52caf-b6b4-43d3-9dd3-5cb9ea02ea48" />


---

## 🆕 Create a Project

```bash
oc new-project dev
```

<img width="1130" height="274" alt="project-created" src="https://github.com/user-attachments/assets/389e3a58-7a70-413f-82de-ead916fe112e" />


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

<img width="586" height="69" alt="pod-running" src="https://github.com/user-attachments/assets/2456557f-84cd-42e4-bf46-7365e6befaab" />


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

<img width="626" height="65" alt="service" src="https://github.com/user-attachments/assets/036c4d19-62df-4f46-9bdd-ea806e204e1e" />


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

<img width="877" height="67" alt="route-exposed" src="https://github.com/user-attachments/assets/bfc9a293-95c0-443d-be9d-c430b55a40ba" />


---

## 🔎 Access in Browser

- Paste the route URL in your browser.
- You should see the NGINX welcome page.

<img width="1172" height="402" alt="nginx-browser" src="https://github.com/user-attachments/assets/ca505cf7-927b-4f9d-bd8f-3c18b09bb178" />


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
