# Monitoring des services Azure avec des composants opensource
## Objectif
Ce repo a pour but de decrire les services de monitoring et collecte de donnees opensource pour Azure.
Ce modele peux etre reutilisable pour d'autres Cloud provider.
La cible de deploiement sera Kubernetes mais il est possible d'utiliser le deploiement sur des VMs

## Liste des services identifies :
- grafana
- prometheus
- thanos (resilience des donnees)
- loki
- ELK
- kubernetes ou VMs
- service mesh 

## Mode opératoire
Voici une proposition de mode opératoire, structurée par étapes, en tenant compte de l'intégration avec Azure et l'utilisation d'un service mesh (comme Istio ou Linkerd) :

1. Prérequis:
- Cluster Kubernetes: Vous devez avoir un cluster Kubernetes fonctionnel. Cela peut être un cluster Azure Kubernetes Service (AKS), un cluster auto-hébergé sur Azure VMs, ou un cluster local (pour les tests).
- Azure CLI: Installé et configuré avec un accès à votre abonnement Azure.
- kubectl: Installé et configuré pour interagir avec votre cluster Kubernetes.
- Helm: Installé pour simplifier le déploiement des applications sur Kubernetes.
- Service Mesh (Optionnel mais recommandé): Installer un service mesh tel qu'Istio ou Linkerd. Cela permettra une meilleure observabilité du trafic réseau et facilitera la configuration du monitoring.
- Accès à Azure Monitor: Configurer l'accès à Azure Monitor pour collecter les métriques et les logs des ressources Azure.

2. Installation et Configuration des outils de monitoring:
- Prometheus:

Utiliser Helm pour installer Prometheus :
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus
Configurer 1  Prometheus pour scraper les métriques des pods Kubernetes et des services exposés. Utiliser des ServiceMonitor pour une configuration dynamique.   
1.
overcast.blog

Configurer Prometheus pour collecter les métriques d'Azure Monitor (via l'exporter Azure Monitor pour Prometheus).

- Grafana:

Utiliser Helm pour installer Grafana :

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana
Configurer Grafana pour utiliser Prometheus comme source de données.

Créer des dashboards Grafana pour visualiser les métriques collectées par Prometheus, y compris les métriques Azure Monitor.

- Thanos (Optionnel, pour la haute disponibilité et le stockage long terme de Prometheus):

Installer les composants de Thanos (Sidecar, Query, Store Gateway, Compactor, Ruler) en utilisant Helm ou des manifests Kubernetes.
Configurer Thanos pour se connecter à Prometheus et à un stockage objet (comme Azure Blob Storage) pour le stockage long terme des métriques.
Loki (Pour l'agrégation et la recherche de logs):

- Utiliser Helm pour installer Loki et Promtail (l'agent de collecte de logs) :

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install loki grafana/loki-stack
Configurer Promtail pour collecter les logs des pods Kubernetes.

Configurer Grafana pour utiliser Loki comme source de données.

- ELK (Elasticsearch, Logstash, Kibana) (Alternative à Loki ou en complément pour des analyses plus complexes):
Installer Elasticsearch, Logstash et Kibana en utilisant Helm ou des manifests Kubernetes.
Configurer Logstash pour collecter les logs des pods Kubernetes et les envoyer à Elasticsearch.
Configurer Kibana pour visualiser et analyser les logs stockés dans Elasticsearch.

3. Intégration avec le Service Mesh:

- Configuration du Service Mesh: Configurer le service mesh pour collecter les métriques de trafic réseau (requêtes, latence, erreurs).
- Intégration avec Prometheus: Exposer les métriques du service mesh à Prometheus pour une surveillance centralisée.
- Visualisation dans Grafana: Créer des dashboards Grafana pour visualiser les métriques du service mesh, permettant ainsi de corréler les performances des applications avec le trafic réseau.

4. Monitoring des ressources Azure:

Exporter Azure Monitor pour Prometheus: Utiliser l'exporter Azure Monitor pour Prometheus pour collecter les métriques des services Azure (machines virtuelles, bases de données, etc.).
Configuration de Prometheus: Configurer Prometheus pour scraper les métriques exposées par l'exporter Azure Monitor.
Visualisation dans Grafana: Créer des dashboards Grafana pour visualiser les métriques des ressources Azure.

5. Déploiement et tests:
Déployer les applications à monitorer sur Kubernetes.
Vérifier que les métriques et les logs sont correctement collectés par Prometheus, Loki et ELK.
Créer des alertes dans Prometheus pour être notifié en cas de problèmes.
Points importants:

Sécurité: Sécuriser l'accès à Grafana, Prometheus, Loki et ELK en utilisant des mécanismes d'authentification et d'autorisation.
Persistance des données: Utiliser des volumes persistants pour stocker les données de Prometheus, Loki et Elasticsearch.
Optimisation des coûts: Optimiser la collecte des métriques et des logs pour éviter des coûts excessifs.
Observabilité: Utiliser des traces distribuées (avec Jaeger ou Zipkin) en complément des métriques et des logs pour une meilleure compréhension du comportement des applications.
Ce mode opératoire est une base. L'implémentation spécifique dépendra de vos besoins et de votre infrastructure. N'hésitez pas à consulter la documentation officielle de chaque outil pour des informations plus détaillées. L'utilisation de Helm est fortement recommandée pour simplifier les déploiements et les mises à jour.
