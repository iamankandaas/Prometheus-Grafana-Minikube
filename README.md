```markdown
# 📊 Flask App Monitoring with Prometheus & Grafana on Minikube

This project is about setting up a simple Flask app, deploying it on a local Kubernetes cluster (Minikube), and monitoring it using Prometheus and Grafana. The goal was to simulate a production-style observability setup where metrics from a web app are collected, scraped, and visualized using industry-standard tools.

---

## 🛠️ Tools & Tech Used

- **Flask** (Python web framework)
- **Prometheus** (metrics collection + querying)
- **Grafana** (visualization dashboards)
- **Docker** (for containerization)
- **Kubernetes + Minikube** (for local K8s environment)
- **Helm** (to install Prometheus & Grafana easily)

---

## 📌 Project Flow

### ✅ Step 1: Flask App Setup
- Created a basic Flask app with a `/metrics` endpoint.
- Used the `prometheus_client` library to expose custom metrics like:
  - Total API request count
  - API response latency

### ✅ Step 2: Dockerize the App
- Wrote a `Dockerfile` for the Flask app.
- Built the image using:
```

docker build -t flask-metrics-app\:latest .

```

### ✅ Step 3: Deploy on Minikube
- Started the Minikube cluster.
- Created a Kubernetes manifest (`flask-app.yaml`) to deploy the app as a pod and expose it via a service.
- Loaded the Docker image into Minikube using:
```

minikube image load flask-metrics-app\:latest

```
- Deployed the app:
```

kubectl apply -f flask-app.yaml

```
- Accessed the Flask service locally using:
```

minikube service flask-metrics-app --url

```

### ✅ Step 4: Install Prometheus using Helm
- Installed Helm (package manager for K8s).
- Added the Prometheus chart repo and installed Prometheus into the `monitoring` namespace:
```

helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace

```

### ✅ Step 5: Configure Prometheus to Scrape Flask App
- Got the internal ClusterIP and port of the Flask service:
```

kubectl get svc

```
- Edited the Prometheus ConfigMap to add Flask as a static scrape target.
- Restarted the Prometheus server pod to apply the new config:
```

kubectl rollout restart deployment prometheus-server -n monitoring

```

### ✅ Step 6: Verified Scraping in Prometheus UI
- Port-forwarded Prometheus to localhost:
```

kubectl port-forward -n monitoring svc/prometheus-server 9090:80

```
- Opened `localhost:9090` and searched for custom metrics like:
```

total\_api\_requests\_total

```

### ✅ Step 7: Install Grafana
- Added the Grafana Helm chart repo and installed Grafana:
```

helm install grafana grafana/grafana -n monitoring --create-namespace

```
- Port-forwarded Grafana to access the dashboard:
```

kubectl port-forward svc/grafana -n monitoring 3000:80

````
- Retrieved default admin password:
```bash
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode
````

### ✅ Step 8: Add Prometheus to Grafana

* After logging into Grafana (`localhost:3000`), added Prometheus as a data source.
* Used the internal cluster URL:

  ```
  http://prometheus-server.monitoring.svc.cluster.local:80
  ```

### ✅ Step 9: Create Grafana Dashboard

* Created a new panel in Grafana to visualize:

  * Number of API requests
  * Request rate (e.g., `rate(total_api_requests_total[1m])`)
* Customized the panel for better readability.

---

## 📖 What I Learned

* How to expose and collect metrics in a real web app.
* Deploying apps in Kubernetes with manifests.
* Using Helm to simplify tool installation on K8s.
* Editing Prometheus config and working with `ConfigMap`.
* Setting up Grafana and connecting it to Prometheus.
* Creating visual dashboards for real-time monitoring.

---

## 🚀 Possible Improvements (Future Work)

* Set up Alertmanager for email/Slack alerts.
* Use Ingress to expose services more securely.
* Create more detailed dashboards (error rates, latencies).
* Automate the setup using Helm charts or Kustomize.

---

## ✅ Status

✔️ Completed – Ready to be used for learning and showcasing monitoring/observability skills.

---

## 🙋‍♂️ Author

**Ankan Das**
M.Tech | Robotics & AI | IIT Guwahati

```