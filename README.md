# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»

---

## Задание 1. Создать Deployment и обеспечить доступ к контейнерам по разным портам внутри кластера

### 1. Deployment с nginx и multitool (3 реплики)

Создан Deployment [`deployment-nginx-multitool.yaml`](deployment-nginx-multitool.yaml) с тремя репликами и двумя контейнерами:
- `nginx:1.25` — слушает порт **80**
- `wbitt/network-multitool` — слушает порт **8080** (переопределён через `HTTP_PORT`, чтобы избежать конфликта с nginx)

### 2. Service ClusterIP с маппингом портов

Создан Service [`svc-clusterip.yaml`](svc-clusterip.yaml):
- порт **9001** → nginx (targetPort: 80)
- порт **9002** → multitool (targetPort: 8080)

### 3–4. Отдельный Pod multitool и проверка curl по доменному имени сервиса

Создан Pod [`pod-multitool.yaml`](pod-multitool.yaml). Проверка доступа:

```bash
kubectl exec multitool-pod -- curl -s http://svc-nginx-multitool:9001
kubectl exec multitool-pod -- curl -s http://svc-nginx-multitool:9002
```

**Вывод curl по доменному имени сервиса (порт 9001 — nginx, порт 9002 — multitool):**

![curl из multitool-pod](screenshots/1.png)

---

## Задание 2. Service NodePort — доступ снаружи кластера

### 1. Service NodePort для nginx

Создан Service [`svc-nodeport.yaml`](svc-nodeport.yaml) с типом `NodePort`:
- port: 80 → targetPort: 80 → nodePort: **30080**

### 2. Доступ с локального компьютера

```bash
curl http://<NODE_IP>:30080
```

**Вывод curl с локального компьютера через NodePort:**

![curl через NodePort](screenshots/2.png)
