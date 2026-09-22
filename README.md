# DevOps Just Got Smarter — Getting Started with MCP

Notes on using the **Model Context Protocol (MCP)** to make AI genuinely useful in day-to-day DevOps work — not chat, but AI that actually knows what's happening across your infrastructure, where, when, and why.

> 📖 Full write-up: [DevOps Just Got Smarter — Getting Started with Model Context Protocol (MCP)](https://www.linkedin.com/pulse/devops-just-got-smarter-getting-started-model-context-gaurav-khatri-8lyzc/) by **Gaurav Khatri**

## What MCP is

MCP is a framework that lets AI systems interact intelligently and contextually with a DevOps ecosystem. Instead of isolated tools throwing alerts into the void, it connects infrastructure data — logs, metrics, configs, code, incident history — into one shared context that AI agents can understand and act on.

## Real problems it addresses

| Problem | What MCP changes |
|---|---|
| **Monitoring** that just says "something broke" | AI pulls logs (Loki), metrics (Prometheus), configs (Git), correlates them, and suggests real root causes |
| **Incident response** requiring a full war room | AI collects cross-stack context, runs diagnostics, proposes or applies known fixes, and can auto-generate post-mortems |
| **CI/CD** that feels mysterious and fragile | AI identifies flaky tests and pipeline bottlenecks before they break the build, and adapts configs dynamically |
| **IaC and security** reviews that eat a whole afternoon | AI reviews infra code, flags risks, suggests secure defaults, and detects drift across environments |

## Kubernetes example

Without MCP, debugging a crashed pod is: `kubectl get pods` → `kubectl logs` → `kubectl describe` → a Slack thread → console logs → chaos.

With MCP, an agent fetches real-time cluster data, identifies why the pod crashed, recommends a rollback based on the last stable version, and documents the incident automatically.

## What a real deployment looked like

- MCP server pods managing context definitions inside the cluster
- Real-time log + metric feeds from Prometheus and Loki
- GitOps pipelines for version-controlled context (so "what the AI knows" is reviewable, just like infra code)
- AI agents that adapt behavior based on live infrastructure state

## Example — a minimal context source definition

A sketch of what a version-controlled MCP context source looks like, feeding an agent both metrics and recent incident history for a service:

```yaml
# context/checkout-service.yaml
apiVersion: mcp.dev/v1
kind: ContextSource
metadata:
  name: checkout-service-context
spec:
  service: checkout-service
  sources:
    - type: prometheus
      query: 'rate(http_requests_total{service="checkout-service",status=~"5.."}[5m])'
    - type: loki
      selector: '{service="checkout-service"}'
      lookback: 15m
    - type: git
      repo: gitops-infra-repo
      path: apps/checkout-service
    - type: incident-history
      lookback: 90d
  agentPolicy:
    autoRemediate: false   # propose fixes, human approves before apply
    postMortem: auto
```

Keeping `autoRemediate: false` by default and requiring a human to approve the fix is the difference between "AI that helps on-call" and "AI that pages itself."

## Takeaway

MCP doesn't magically fix everything, but it makes AI contextual and useful in real DevOps work — the shift is from "chatting with a model" to building AI that actually understands the state of your systems.

---

**Author:** [Gaurav Khatri](https://www.linkedin.com/in/gaurav-khatri-devops/) — DevOps Engineer @ Sarv.com | Kubernetes (EKS), Docker, GitOps & CI/CD
