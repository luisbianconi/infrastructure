# Monitoring – Tenants & Contribuições

## O que este diretório faz
- `10-monitoring-crds.yaml`: aplica **apenas CRDs** do Prometheus Operator a partir do upstream (`kube-prometheus/manifests/setup`). **Wave -2** para garantir ordem.
- `20-monitoring-stack.yaml`: instala o **kube-prometheus-stack** via Helm, usando nossos `values` em `../values/kps-values.yaml`. **Wave -1** e `skipCrds: true`.
- Este arquivo (`99-*`) é apenas documentação/âncora. Apps de times (ServiceMonitors, PodMonitors, Rules, Dashboards) ficam **em outro repo** e são onboardados por Applications próprios (wave 0+).

## Regras Enterprise
1. **CRDs** são geridos pelo time de plataforma. Ninguém mais aplica CRD.
2. Versões **pinadas**: 
   - `10` usa `kube-prometheus@release-X.Y`.
   - `20` usa `kube-prometheus-stack@<semver>`.
   Garanta compatibilidade (CRDs ↔ chart/appVersion).
3. Segredos (ex.: Grafana admin) **não** ficam em `values.yaml`. Use External Secrets ou `existingSecret`.

## Como contribuir (times de app)
- Entregue `ServiceMonitor/PodMonitor`, `PrometheusRule` e Dashboards no repo `app-config` do time.
- Padrões:
  - Labels: `team: <nome>` e `monitoring: enabled`
  - Limites de cardinalidade e naming convencionado
- Abra PR; o time de plataforma aprova e o Argo aplica.

## Troubleshooting rápido
- **"no matches for kind"**: CRDs não aplicados/ordem errada → ver sync-waves.
- **Erros de validação em CRDs**: mismatch de versão → alinhar `release` (10) com `targetRevision` do chart (20).
- **CRDs reaplicando toda hora**: não coloque CRDs no chart (`skipCrds: true` já evita).

## Próximos passos
- Ajuste DNS/Ingress do Grafana no `../values/kps-values.yaml`.
- Se necessário, crie um App para `secrets/` com `grafana-admin` até migrar para External Secrets.

