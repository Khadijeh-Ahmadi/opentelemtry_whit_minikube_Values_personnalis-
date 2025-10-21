# opentelemtry_whit_minikube_Values_personnalis-
Namespace: otel-demo26

Fichier de valeurs : my-values-file.yaml

Ce dépôt contient une configuration prête à l'emploi pour déployer la démo OpenTelemetry sur Minikube avec une configuration personnalisée du collecteur OpenTelemetry, ainsi qu'un PVC pour l'exportation de fichiers.

Exportateurs activés : OpenSearch, Prometheus (HTTP OTLP + scraping), Jaeger/OTLP, Zipkin et File (via PVC).

Tempo est facultatif (non installé par défaut). 

Instructions ci-dessous:

1) Prérequis

   Linux (testé sur Ubuntu 24.04)
   
   Docker, kubectl, Helm v3+, Minikube

3) Démarrer Minikube
   
   minikube start \
   --driver=docker \
   --memory=24720 \
   --cpus=6 \
   --disk-size=100g \
   --addons=ingress,metrics-server \
   --kubernetes-version=stable

5) Créer le namespace et la PVC
   
   kubectl create namespace otel-demo26
   kubectl apply -n otel-demo26 -f otel-file-export-pvc.yaml

7) Ajouter le repo Helm et déployer le demo
   
   helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
   helm repo update

   helm install my-otel-demo open-telemetry/opentelemetry-demo \
    -n otel-demo26 \
    -f my-values-file.yaml


9) Vérifier

   kubectl get pods -n otel-demo26
   kubectl logs -n otel-demo26 deploy/otel-collector --tail=100

10) Accès rapides (port-forward)
    
   kubectl --namespace default port-forward svc/frontend-proxy 8080:8080

  Ensuit:
  
    Web store: http://localhost:8080/
    Grafana: http://localhost:8080/grafana/
    Load Generator UI: http://localhost:8080/loadgen/
    Jaeger UI: http://localhost:8080/jaeger/ui/

9) Suppression

    
    helm uninstall my-otel-demo -n otel-demo26
    kubectl delete pvc otel-file-export -n otel-demo26
    kubectl delete namespace otel-demo26


