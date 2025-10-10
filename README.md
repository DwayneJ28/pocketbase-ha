# pocketbase-ha
Highly Available Leaderless PocketBase Cluster powered by `go-ha` database/sql driver.

## Features

- **High Availability**: Run multiple PocketBase instances in a leaderless cluster.
- **Replication**: Synchronize data across nodes using NATS.
- **Embedded or External NATS**: Choose between an embedded NATS server or an external one for replication.

## Prerequisites

- **Go**: Version `1.25` or later is required.
- **CGO**: Ensure `CGO_ENABLED=1` is set.

## Installation

Install the latest version of `pocketbase-ha` using:

```sh
go install github.com/litesql/pocketbase-ha@latest
```

### Docker image

```sh
docker pull ghcr.io/litesql/pocketbase-ha:latest
```

## Configuration

Set up your environment variables to configure the cluster:

| Environment Variable | Description                                                                 |
|----------------------|-----------------------------------------------------------------------------|
| `PB_NAME`            | Unique name for the node.                                                  |
| `PB_REPLICATION_URL` | NATS connection URL for replication (use if connecting to an external NATS server). Example: `nats://localhost:4222`. |
| `PB_REPLICATION_SUBJECT` | Stream name for data replication |
| `PB_NATS_PORT`       | Port for the embedded NATS server (use only if running an embedded NATS server). |
| `PB_NATS_STORE_DIR`  | Directory for storing data for the embedded NATS server.                   |
| `PB_NATS_CONFIG`     | Path to a NATS configuration file (overrides other NATS settings).         |

## Usage

### Starting a Cluster

1. Start the first PocketBase HA instance:

    ```sh
    PB_NAME=node1 PB_REPLICATION_SUBJECT=pb PB_NATS_PORT=4222 pocketbase-ha serve
    ```

2. Start a second instance in a different directory:

    ```sh
    PB_NAME=node2 PB_REPLICATION_SUBJECT=pb PB_REPLICATION_URL=nats://localhost:4222 pocketbase-ha serve --http 127.0.0.1:8091
    ```

> **Note**: You can skip setting the superuser password for the second instance.

### Running a NATS Cluster with docker

To run a NATS cluster using Docker Compose, use the following command:

```sh
docker compose up
```

You can define the superuser password using this command:

```sh
docker compose exec -e PB_NATS_CONFIG="" node1 /app/pocketbase-ha superuser upsert EMAIL PASS
```

Access the three nodes using the following address:

- Node1: http://localhost:8090
- Node2: http://localhost:8091
- Node3: http://localhost:8092

> **Tip**: Ensure all nodes are synchronized by verifying the logs or using the PocketBase admin interface.

## Contributing

Contributions are welcome! Feel free to open issues or submit pull requests to improve this project.

## License

This project is licensed under the [MIT License](LICENSE).