Here’s a clean and complete README.md file for your Kafka + ZooKeeper cluster project using Docker Compose:

⸻


# Kafka + ZooKeeper Cluster (3 Nodes Each) Using Docker Compose

This setup creates a **production-ready Apache Kafka cluster** with **three ZooKeeper nodes** and **three Kafka brokers** using Bitnami Docker images. It includes:

## 🚀 Key Features

- **Persistent Storage**: Data is preserved across container restarts
- **High Availability**: Tolerates single node failures
- **Production-Ready Config**:
  - Replication Factor: `3`
  - Minimum In-Sync Replicas: `2`
- **Network Isolation**: Uses a custom bridge network (`app-mysql`) for internal communication

---

## 📦 Components Overview

### 🐘 ZooKeeper Cluster (3 Nodes)
- **Image**: `bitnami/zookeeper:3.8`
- **Server IDs**: 1, 2, 3
- **Ports**: `2181`, `2182`, `2183`
- **Volumes**:
  - `/data` for snapshots
  - `/datalog` for transaction logs

### ⚡ Kafka Cluster (3 Brokers)
- **Image**: `bitnami/kafka:3.4`
- **Broker IDs**: 1, 2, 3
- **Ports**: `9092`, `9093`, `9094` (Host)
- **Internal Ports**: `19092`, `19093`, `19094` (Container-to-container)
- **Volumes**: Persistent storage for each broker

---

## 📖 Usage

### Start the Cluster

```bash
docker-compose up -d

Check Cluster Status

docker-compose ps

View Kafka Logs

docker-compose logs kafka-1

Stop the Cluster

docker-compose down

Destroy All Data (⚠️ DANGER)

docker-compose down -v


⸻

🎯 Access Information
	•	Kafka Brokers:
localhost:9092, localhost:9093, localhost:9094
	•	ZooKeeper Nodes:
localhost:2181, localhost:2182, localhost:2183

⸻

🧪 Cluster Testing & Validation

Create Test Topic

docker exec -it kafka-1 kafka-topics.sh \
  --create --topic test-topic \
  --bootstrap-server localhost:9092 \
  --partitions 3 --replication-factor 3

List Topics

docker exec -it kafka-1 kafka-topics.sh \
  --list --bootstrap-server localhost:9092


⸻

🔄 Clean Restart

docker-compose down -v
docker-compose up -d
sleep 60

Check ZooKeeper Status

docker exec -it zookeeper-1 zkServer.sh status
docker exec -it zookeeper-2 zkServer.sh status
docker exec -it zookeeper-3 zkServer.sh status

You should see:
	•	1 node in leader mode
	•	2 nodes in follower mode

⸻

Verify Broker Registration

docker exec -it zookeeper-1 zkCli.sh -server localhost:2181 <<< "ls /brokers/ids"

Expected Output:

[1, 2, 3]


⸻

Create Cluster Test Topic (Internal Port)

docker exec -it kafka-1 kafka-topics.sh \
  --create --topic test-cluster \
  --bootstrap-server kafka-1:19092 \
  --partitions 3 --replication-factor 3


⸻

🧹 Troubleshooting & Cleanup

Full Reset

docker-compose down
docker volume ls | grep -E "(kafka|zk)"
docker volume prune -f
docker container prune -f

Manually Remove Named Volumes

docker volume rm stack3_kafka1-data stack3_kafka2-data stack3_kafka3-data
docker volume rm stack3_zk1-data stack3_zk1-datalog \
                 stack3_zk2-data stack3_zk2-datalog \
                 stack3_zk3-data stack3_zk3-datalog


⸻

✅ Verify Everything Works

docker-compose up -d
docker-compose logs -f
docker exec -it zookeeper-1 zkServer.sh status
docker exec -it zookeeper-1 zkCli.sh -server localhost:2181 <<< "ls /brokers/ids"


⸻

📁 Project Structure

.
├── docker-compose.yaml
├── README.md
└── volumes/
    ├── kafka1-data/
    ├── kafka2-data/
    ├── kafka3-data/
    ├── zk1-data/
    ├── zk1-datalog/
    ├── zk2-data/
    ├── zk2-datalog/
    ├── zk3-data/
    └── zk3-datalog/


⸻

🧰 Helpful Commands Summary

Task	Command
Start cluster	docker-compose up -d
Stop cluster	docker-compose down
Destroy all data	docker-compose down -v
View logs	docker-compose logs kafka-1
Create topic	kafka-topics.sh --create
List topics	kafka-topics.sh --list
Check ZooKeeper	zkServer.sh status
Check broker IDs	zkCli.sh <<< "ls /brokers/ids"


⸻

🧠 Notes
	•	All services share the same app-mysql Docker network
	•	Replication settings ensure no data loss under single broker failure
	•	Best for local testing, PoC, and learning production-grade Kafka

⸻

📜 License

MIT

Let me know if you want a PDF or downloadable `.md` version.
