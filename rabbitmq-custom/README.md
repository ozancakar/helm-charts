# 🛠 Custom Helm Charts

Hello!  
This repository contains custom Helm charts that I've created for deploying applications with environment-specific configurations.

In standard Helm charts, the default values are usually static. However, in real-world scenarios, different environments (like **dev**, **staging**, and **production**) often require different configurations.

To handle this cleanly, I introduced a directory structure under `environments/`, allowing me to:

- Deploy to multiple environments using separate `values.yaml` files
- Customize each environment independently (e.g. user credentials, ingress, HPA)

---

## 🔧 Multiple Port Support

By default, Helm charts created with `helm create` only support a **single port** under the `service` section.

In this project, we customized:

1. `values.yaml`: to support **multiple ports**, including AMQP (5672), management UI (15672), and Prometheus metrics (9419)
2. `service.yaml`: to loop over `service.ports[]` and generate multiple port definitions dynamically

This makes it easy to expose multiple services for RabbitMQ or any other application.

```yaml
service:
  type: ClusterIP
  ports:
    - name: amqp
      port: 5672
      targetPort: 5672
    - name: management
      port: 15672
      targetPort: 15672
    - name: prometheus
      port: 9419
      targetPort: 9419
```

---

## 📁 Folder Structure

```bash
rabbitmq-custom/
├── templates/
├── environments/
│   ├── dev/values.yaml
│   ├── staging/values.yaml
│   └── prod/values.yaml
```

Each environment can enable or disable features like:

- Ingress
- HorizontalPodAutoscaler (HPA)
- NodePort exposure
- Different image tags or credentials

---

## ✅ Example Commands

Install in the dev environment:

```bash
helm upgrade --install rabbitmq-dev ./rabbitmq-custom \
  --namespace dev-infra --create-namespace \
  -f ./rabbitmq-custom/environments/dev/values.yaml
```

---

## 🚧 TODO (Coming Soon)

- Use Kubernetes Secrets for sensitive credentials
- Add GitHub Actions for chart validation and linting
- Encrypt values with SOPS for Git-safe secret management

---

Maintained by **Ozan Çakar**.
