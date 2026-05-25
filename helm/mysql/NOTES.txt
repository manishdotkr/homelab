MySQL has been deployed successfully.

RELEASE   : {{ .Release.Name }}
NAMESPACE : {{ .Release.Namespace }}
VERSION   : {{ .Chart.AppVersion }}

---

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Connection details
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
helm/mysql/
├── Chart.yaml                  # chart metadata, version, appVersion
├── values.yaml                 # all tuneable defaults
├── secrets.yaml                # ← NOT in the chart; apply manually first
├── mysql-argocd.yaml           # ← NOT in the chart; ArgoCD AppProject + App
└── templates/
    ├── _helpers.tpl            # named templates (fullname, labels, etc.)
    ├── pv-pvc.yaml             # PersistentVolume + PersistentVolumeClaim
    ├── statefulset.yaml        # StatefulSet (MySQL container)
    ├── service.yaml            # ClusterIP Service + headless Service
    ├── configmap.yaml          # my.cnf config + optional init scripts
    ├── serviceaccount.yaml     # ServiceAccount
    └── NOTES.txt               # printed after helm install


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Connection details
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Host (in-cluster) : {{ include "mysql.fullname" . }}.{{ .Release.Namespace }}.svc.cluster.local
  Port              : {{ .Values.service.port }}
  Database          : {{ .Values.mysql.database }}
  {{- if .Values.mysql.user }}
  User              : {{ .Values.mysql.user }}
  {{- end }}

  Root password is stored in Secret → {{ .Values.mysql.existingSecret }}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Quick test (run from inside the cluster)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  kubectl run mysql-client --rm -it --restart=Never \
    --image=mysql:{{ .Chart.AppVersion }} \
    --namespace={{ .Release.Namespace }} \
    -- mysql -h {{ include "mysql.fullname" . }} \
             -u root \
             -p"$(kubectl get secret {{ .Values.mysql.existingSecret }} \
                    -n {{ .Release.Namespace }} \
                    -o jsonpath='{.data.MYSQL_ROOT_PASSWORD}' | base64 -d)"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Data is persisted at (on the node)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  {{ .Values.persistence.hostPath }}