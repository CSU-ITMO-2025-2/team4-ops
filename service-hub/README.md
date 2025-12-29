# service-hub Helm chart

Helm chart to deploy the service-hub demo stack (api-gateway, ticket-service, notification-service) together with Kafka and Zookeeper. PostgreSQL is expected as an external database.

## What it deploys
- Zookeeper and a single Kafka broker with the CPU/memory requests/limits that were required in your cluster.
- api-gateway, ticket-service, notification-service Deployments and ClusterIP Services.
- An optional secret (`secrets.name`) that stores `jwt-secret` and `db-password`.

## Defaults wired to your DB
`values.yaml` is prefilled to reach the provided database:
- host `79.174.89.43`, port `18809`, db `team4_db`, user `team4`.
- Set `secrets.dbPassword` to `Hr2nTdC8#y4Mz1vG` (already in the sample values). Change it if you rotate the password.

## Install
1. Build and push images for `api-gateway`, `ticket-service`, `notification-service`, and set the repositories/tags in `values.yaml`.
2. Install into your namespace (example uses `team4-ns`):
   ```bash
   helm upgrade --install service-hub ./helm/service-hub \
     --namespace team4-ns --create-namespace
   ```
3. Check status:
   ```bash
   kubectl get pods,svc -n team4-ns
   ```

## Tuning
- Resource requests/limits for Kafka/Zookeeper are set to `100m` / `500Mi`. Adjust in `values.yaml` if needed.
- To enable persistent storage for Kafka, set `kafka.persistence.enabled=true` and adjust `kafka.persistence.size`.
- Override secrets without editing files:
  ```bash
  helm upgrade --install service-hub ./helm/service-hub \
    --namespace team4-ns --create-namespace \
    --set secrets.dbPassword="Hr2nTdC8#y4Mz1vG" \
    --set secrets.jwtSecret="devsecret"
  ```
