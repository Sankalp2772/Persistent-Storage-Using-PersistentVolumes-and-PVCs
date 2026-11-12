# Persistent Storage Using PersistentVolumes and PVCs

##  Problem Statement

Deploy an application on Kubernetes that stores data on a PersistentVolume through a PersistentVolumeClaim. Demonstrate that the data remains available after the Pod is deleted and recreated.

##   Objective

To implement persistent storage in Kubernetes using PersistentVolumes (PV) and PersistentVolumeClaims (PVC), and demonstrate that data remains available independently of the Pod lifecycle.

##  Architecture

```text
PersistentVolume (PV)
        │
        ▼
PersistentVolumeClaim (PVC)
        │
        ▼
Kubernetes Deployment
        │
        ▼
Application Pod
        │
        ▼
Persistent Storage
```

##  Technologies Used

- Kubernetes
- Kind
- Docker Desktop
- kubectl
- Nginx

##  Project Structure

```text
persistent-storage-lab/
├── pv.yaml
├── pvc.yaml
├── deployment.yaml
└── README.md
```

##  Implementation

### 1. Create the PersistentVolume

```bash
kubectl apply -f pv.yaml
kubectl get pv
```

### 2. Create the PersistentVolumeClaim

```bash
kubectl apply -f pvc.yaml
kubectl get pvc
```

The PV and PVC should show the status `Bound`.

### 3. Deploy the Application

```bash
kubectl apply -f deployment.yaml
kubectl get pods -o wide
```

The application is configured to mount the PVC at:

```text
/usr/share/nginx/html/data
```

### 4. Write Data to Persistent Storage

Get the Pod name:

```bash
kubectl get pods -l app=storage-app
```

Create a test file:

```bash
kubectl exec <pod-name> -- sh -c "echo 'Persistent storage test successful - Kubernetes Lab Evaluation 2' > /usr/share/nginx/html/data/data.txt"
```

Verify the data:

```bash
kubectl exec <pod-name> -- cat /usr/share/nginx/html/data/data.txt
```

### 5. Test Data Persistence

Delete the Pod:

```bash
kubectl delete pod <pod-name>
```

The Deployment automatically creates a new Pod.

Get the new Pod name:

```bash
kubectl get pods -l app=storage-app -o jsonpath="{.items[0].metadata.name}"
```

Verify that the data still exists:

```bash
kubectl exec <new-pod-name> -- cat /usr/share/nginx/html/data/data.txt
```

Expected output:

```text
Persistent storage test successful - Kubernetes Lab Evaluation 2
```

##  Key Kubernetes Concepts Demonstrated

- PersistentVolume (PV)
- PersistentVolumeClaim (PVC)
- Volume mounting
- ReadWriteOnce access mode
- Retain reclaim policy
- Pod recreation using Deployment
- Persistent data across Pod lifecycle

##  Important Note

This project uses a `hostPath` PersistentVolume for demonstration purposes. Since `hostPath` storage is local to a Kubernetes node, the Deployment uses a `nodeSelector` to ensure the application runs on the same node.

For production multi-node environments, shared or network-backed storage should be used.

## ✅ Result

The application successfully stored data using a PersistentVolume and PersistentVolumeClaim. After the original Pod was deleted and automatically recreated by the Deployment, the new Pod successfully accessed the previously stored data.

This demonstrates that Kubernetes PersistentVolumes provide storage that is independent of the Pod lifecycle.
