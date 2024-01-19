# Kubernetes

Kubernetes adalah sistem orkestrasi kontainer open-source yang mengotomatiskan pemasangan, skala, dan pengelolaan aplikasi kontainer. Kubernetes menangani kompleksitas operasi sehingga Anda dapat menskalakan beban kerja dan mengelola deployment kontainer di beberapa server. 

## Membuat sebuah cluster menggunakan k3s yang berisikan 2 node as a master and worker.

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/f2c568ca-0394-4179-bcef-30d0ba85b8e6)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/acbfdc67-6b22-4c0f-8a53-d9668b5c0d68)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/85b4d2ed-7a14-4dd3-84b5-6446e192ac44)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/470fae8c-7a78-4ddb-b5cc-287a371d82bc)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/b06647d5-de79-41fd-8b00-029fd507583f)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/a8d220cf-7ef9-4d33-9d5b-7f134713041b)

## Install ingress nginx using manifest

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/b478cded-1fde-474f-85f1-b7601ea89986)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/ed1216db-dbf0-4f78-af4b-851bf491390c)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/00652a7e-a117-461b-8006-0766b1219901)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/dd3a2d96-dbbd-4c5e-b254-1bf056f29a31)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/da8cee8c-6ec9-4967-b73d-9d84dd469fae)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/f6decb40-155b-4621-84ca-b2343082f7db)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/25812806-4674-4d5b-8713-61cfc609abec)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/a7641278-2ecb-41bd-a721-e33f866ccf27)

## Ingress Configuration

buat nginx.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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

---

apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

---

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: irwan.studentdumbways.id  
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/d2cf9674-1231-467c-8731-316863920237)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/c9e3c80b-fdae-404b-bce7-d8be4fcd8660)

## Setup pvc
buat file ```pvc.yaml```

```

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-path-pvc
  namespace: irwan
spec:
  storageClassName: local-path
  resources:
    requests:
      storage: 2Gi
  accessModes:
    - ReadWriteOnce

```

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/a970c2bc-4923-46f2-a555-54a4b4438e94)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/72927df7-d0c9-4b45-919d-912ab8a6f779)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/c6466207-07a6-4dc7-8ee7-d088c525c3a1)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/e5c7d3c4-1db2-486a-8874-7fb685adcdae)

buat ```pod.yml```
```

apiVersion: v1
kind: Pod
metadata:
  name: volume-test
  namespace: irwan
spec:
  containers:
  - name: volume-test
    image: nginx:alpine
    imagePullPolicy: Always
    volumeMounts:
    - name: vol 
      mountPath: /data
    ports:
      - containerPort: 80
  volumes:
  - name: vol
    persistentVolumeClaim:
      claimName: local-path-pvc

```

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/92138a08-128d-479f-ab6c-ad0d23c92c02)

## Deploy mongodb with pvc
buat file ```dbmongol.yaml```
```
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-secret
  namespace: irwan
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: aXJ3YW4= #irwan 
  MONGO_INITDB_ROOT_PASSWORD: aXJ3YW4xMjM=   #irwan123
  MONGO_INITDB_DATABASE: aXJ3YW5kYg==   #irwandb

---

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
  namespace: irwan
spec:
  serviceName: "mongodb"
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:4.4
          envFrom:
             - secretRef:
                 name: mongodb-secret
                 optional: false
          volumeMounts:
            - name: mongodb-storage
              mountPath: /data/db
      volumes:
      - name: mongodb-storage
        persistentVolumeClaim:
          claimName: local-path-pvc

---

apiVersion: v1
kind: Service
metadata:
  name: mongodb
  namespace: irwan
spec:
  selector:
    app: mongodb
  type: NodePort
  ports:
    - name: port
      port: 27017
      targetPort: 27017

```

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/e08623bb-1d9e-4577-b905-03ec3ade1589)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/6c01ea97-bcb7-44d4-8fa1-dff33406f0fc)

## Membuat Dummy Database 

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/c88700e1-a8fc-4ddd-bad6-ce36f20029f0)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/d406393a-b08f-423d-8060-93a6b7cfa09d)

![image](https://github.com/irwanpanai/devops19-dumbways-irwanpanai/assets/89429810/3fc935a8-7cb7-48c4-b50c-81faddc8be90)

