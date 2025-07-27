# 🚀 Kubernetes Guestbook Application - Final Project

This project is a part of the final hands-on lab for the Kubernetes course. It involves building, deploying, scaling, updating, and rolling back a **Guestbook web application** using Kubernetes on IBM Cloud.

---

## 🎓 Project Overview

* **Frontend App:** A simple web guestbook with a text entry field
* **Backend:** Golang app built using multi-stage Docker build
* **Container Registry:** IBM Cloud Container Registry (ICR)
* **Orchestration Platform:** Kubernetes (kubectl)
* **Automation:** Horizontal Pod Autoscaler (HPA), Rolling Updates, Rollbacks

---

## 📂 Repository Structure

```bash
.
├── v1
│   └── guestbook
│       ├── Dockerfile
│       ├── main.go
│       ├── deployment.yml
│       └── public
│           ├── index.html
│           ├── script.js
│           ├── style.css
│           └── jquery.min.js
```

---

## 📊 Task Breakdown

### 🔢 Task 1: Dockerfile Completion

> **Objective:** Update the Dockerfile with all required build steps.

**Steps:**

* Use Golang base image for the build stage.
* Copy and build the Go app.
* Use Ubuntu as the final image.
* Copy binaries and assets.

**Snippet:**

```Dockerfile
FROM golang:1.18 AS builder
WORKDIR /app
COPY main.go .
RUN go mod init guestbook && go mod tidy
RUN go build -o main main.go

FROM ubuntu:18.04
COPY --from=builder /app/main /app/guestbook
COPY public/index.html /app/public/index.html
COPY public/script.js /app/public/script.js
COPY public/style.css /app/public/style.css
COPY public/jquery.min.js /app/public/jquery.min.js
WORKDIR /app
CMD ["./guestbook"]
EXPOSE 3000
```

---

### 🚚 Task 2: Push Image to IBM Cloud

> **Objective:** Build Docker image and push to IBM Cloud Container Registry

**Commands:**

```bash
ibmcloud cr namespace-add sn-labs-souvikmanda3
export MY_NAMESPACE=sn-labs-souvikmanda3
cd v1/guestbook

docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1

docker push us.icr.io/$MY_NAMESPACE/guestbook:v1
ibmcloud cr images
```

---

### 🌐 Task 3: Launch Guestbook v1

> **Objective:** Deploy the app and access via port-forward

**Commands:**

```bash
kubectl apply -f deployment.yml
kubectl port-forward deployment.apps/guestbook 3000:3000
```

Launch from browser using `https://<subdomain>.skillsnetwork.site/3000/`

---

### 🌌 Task 4: Create Horizontal Pod Autoscaler

> **Objective:** Enable autoscaling based on CPU usage

**Command:**

```bash
kubectl autoscale deployment guestbook --cpu-percent=5 --min=1 --max=10
```

---

### 💥 Task 5: Test Autoscaler with Load

> **Objective:** Generate load to test scaling

**Command:**

```bash
kubectl run -i --tty load-generator --rm \
  --image=busybox:1.36.0 --restart=Never \
  -- /bin/sh -c "while sleep 0.01; do wget -q -O- https://<app-url>:3000/; done"

kubectl get hpa guestbook --watch
```

---

### 🏋 Task 6: Update Guestbook to v2

> **Objective:** Modify app title/header and push updated image

**Changes in `index.html`:**

```html
<title>Souvik's Guestbook - v2</title>
<h1>Guestbook - v2</h1>
```

**Rebuild and Push:**

```bash
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1

docker push us.icr.io/$MY_NAMESPACE/guestbook:v1
```

---

### 📊 Task 7: Update CPU in Deployment

> **Objective:** Reduce resource limits for autoscaler testing

**deployment.yml changes:**

```yaml
resources:
  limits:
    cpu: 5m
  requests:
    cpu: 2m
```

**Apply Updated Deployment:**

```bash
kubectl apply -f deployment.yml
```

---

### 🌟 Task 8: Rolling Update to v2

> **Objective:** Set image to trigger rolling update

**Command:**

```bash
kubectl set image deployment guestbook guestbook=us.icr.io/$MY_NAMESPACE/guestbook:v1
kubectl rollout status deployment guestbook
```

---

### 🏆 Task 9: View Deployment History

> **Objective:** Show revision history and view details

**Commands:**

```bash
kubectl rollout history deployment/guestbook
kubectl rollout history deployment guestbook --revision=2
```

---

### 🌀 Task 10: Rollback to Revision 1

> **Objective:** Undo the latest update

**Commands:**

```bash
kubectl rollout undo deployment/guestbook --to-revision=1
kubectl get rs
```

---

## 📄 Deliverables for Submission

| Task | Screenshot Filename |
| ---- | ------------------- |
| 1    | Dockerfile.png      |
| 2    | crimages.png        |
| 3    | task3.jpg           |
| 4    | task4.jpg           |
| 5    | task5.jpg           |
| 6    | task6.jpg           |
| 7    | task7.jpg           |
| 8    | task8.jpg           |
| 9    | task9.jpg           |
| 10   | rs.png              |

---

## 🚀 Bonus (Optional Lab)

> You may proceed to deploy the guestbook app from the **OpenShift Internal Registry** in the next lab.

---

## 💪 Author

**Souvik Mandal**

Feel free to ⭐ this repo if it helped you learn Kubernetes! Happy Clustering!

---

## 🚜 Commands Summary Cheatsheet

```bash
# Deploy app
kubectl apply -f deployment.yml

# Port forward
kubectl port-forward deployment.apps/guestbook 3000:3000

# Autoscale
kubectl autoscale deployment guestbook --cpu-percent=5 --min=1 --max=10

# Rolling update
kubectl set image deployment guestbook guestbook=us.icr.io/$MY_NAMESPACE/guestbook:v1

# Rollback
kubectl rollout undo deployment/guestbook --to-revision=1
```

---

🚀 *This README is a comprehensive documentation of all steps and screenshots required to complete the Kubernetes final project.*
