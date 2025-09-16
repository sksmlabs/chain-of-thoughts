#### Kafka Admin

- Purpose: Manage the cluster itself.
- Typical actions in your usage:
    - Create topics (sol.txs, sol.accounts, sol.blocks, sol.logs).
    - Configure partitions, replication factor, retention policies.
- List or delete topics (dev only).

-----
#### Kafka Producer
- Purpose: Write data (messages/events) into topics.
- In your usage:
    - Yellowstone stream handler → serializes a transaction update → Producer sends it to sol.txs topic with key=signature.
    - Same for accounts, blocks, logs.
- Runs continuously while your indexer is streaming.

-----
```
// Remove
docker-compose down -v
// Create
docker-compose up -d
```
```
# both kafka and kafka-ui should be "Up"
docker ps
```
```
// Check UI Configuration
docker exec -it kafka-ui printenv | grep KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS
```
```
// Listing existing topics
docker exec -it kafka /opt/bitnami/kafka/bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 --list
```
