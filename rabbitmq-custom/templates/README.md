### Why Do We Use `range` in Deployment and Service Templates?

In our Helm charts, we define multiple ports in the `values.yaml` file (for example, RabbitMQ's AMQP, management UI, and Prometheus metrics ports).

Instead of hardcoding each port in the Kubernetes manifests (`Deployment` and `Service`), we use the `range` function to **loop over all ports dynamically**.

---

#### What Does This Give Us?

1. **Flexibility:** We can add or remove ports just by changing the `values.yaml` file without modifying the template files.  
2. **DRY Principle (Don't Repeat Yourself):** The template code is written once and automatically applies to all defined ports.  
3. **Easier Management:** Managing multiple ports becomes simpler with a single list instead of multiple repeated entries.  
4. **Reduced Error Risk:** Less chance of mistakes compared to manually adding or removing ports in multiple places.

---

#### How It Works (Example):

```yaml
{{- range .Values.service.ports }}
- name: {{ .name }}
  port: {{ .port }}
  targetPort: {{ .targetPort }}
  protocol: {{ .protocol | default "TCP" }}
{{- end }}
```

This loops over each port defined in `values.yaml` and injects it into the Kubernetes manifest.

The same applies in the `Deployment` template for container ports, ensuring all required ports are exposed automatically.

---

This approach makes the Helm chart more scalable, maintainable, and adaptable for applications exposing multiple ports.
