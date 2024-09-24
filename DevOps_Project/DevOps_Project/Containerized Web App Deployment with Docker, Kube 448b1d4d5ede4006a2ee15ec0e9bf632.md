# Containerized Web App Deployment with Docker, Kubernetes

![image2%203.png](image2%203.png)

![image3%202.png](image3%202.png)

![image4%201.png](image4%201.png)

```yaml
# apache-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
      - name: apache
        image: httpd:2.4
        ports:
        - containerPort: 80
```

```yaml
# custom-app-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: custom-app-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: custom-app
  template:
    metadata:
      labels:
        app: custom-app
    spec:
      containers:
      - name: custom-app
        image: nileshmuthal1317/website:latest
        ports:
        - containerPort: 80
```

```yaml
# services.yaml

apiVersion: v1
kind: Service
metadata:
  name: apache-service
spec:
  selector:
    app: apache
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
---
apiVersion: v1
kind: Service
metadata:
  name: custom-app-service
spec:
  selector:
    app: custom-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```

```yaml
# ingress.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /apache
        pathType: Prefix
        backend:
          service:
            name: apache-service
            port:
              number: 80
      - path: /custom
        pathType: Prefix
        backend:
          service:
            name: custom-app-service
            port:
              number: 80
```

```docker
# Dockerfile

# Use an official Apache HTTP Server as the base image
FROM httpd:2.4

# Copy the content of the website to the Apache server's root directory
COPY . /usr/local/apache2/htdocs/

# Expose port 80
EXPOSE 80
```

![image5%201.png](image5%201.png)

![image6%202.png](image6%202.png)

![image7%202.png](image7%202.png)

![image8%202.png](image8%202.png)

![image9%201.png](image9%201.png)

![image10%201.png](image10%201.png)