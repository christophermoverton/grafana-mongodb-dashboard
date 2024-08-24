# Weather Alerts Dashboard with MongoDB, Prometheus, and Grafana

This project provides a comprehensive monitoring and alerting system for weather alerts using MongoDB, Prometheus, and Grafana. It leverages the MongoDB exporter to expose weather alert data from MongoDB to Prometheus, which is then visualized in Grafana.

## Features

- **MongoDB**: Stores weather alert data.
- **Prometheus**: Scrapes metrics from MongoDB via the MongoDB exporter.
- **MongoDB Exporter**: Exposes MongoDB metrics (including weather alerts) to Prometheus.
- **Grafana**: Visualizes data in dashboards and provides alerting capabilities.

## Related Projects

This project is part of a broader weather alerting system and is used in conjunction with the following projects:

- **[Weather Alert Consumer](https://github.com/christophermoverton/weather-alert-consumer)**: Consumes weather alert data from Kafka and inserts it into MongoDB.
- **[Docker Kafka WeatherAlert](https://github.com/christophermoverton/docker_kafka_weatheralert)**: Provides a Kafka-based system for publishing and consuming weather alert data.

## Prerequisites

- Docker and Docker Compose installed on your machine.

## Project Structure

```bash
.
├── docker-compose.yml          # Docker Compose file to orchestrate services
├── init-mongo.js               # Initialization script for MongoDB users and roles
├── grafana/                    # Grafana configuration and provisioning
├── prometheus/                 # Prometheus configuration
└── README.md                   # Project documentation
```

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/weather-alert-dashboard.git
cd weather-alert-dashboard
```

### 2. Environment Configuration

Ensure the MongoDB, Prometheus, and Grafana services are properly configured in the `docker-compose.yml` file.

### 3. Run the Project

Start all services (MongoDB, Prometheus, Grafana, and the MongoDB exporter) by running:

```bash
docker-compose up -d
```

### 4. Configure MongoDB Exporter

The MongoDB exporter exposes MongoDB metrics to Prometheus. Make sure the correct MongoDB URI is configured in the `docker-compose.yml`:

```yaml
mongodb_exporter:
  image: bitnami/mongodb-exporter:latest
  container_name: mongodb_exporter
  environment:
    - MONGODB_URI=mongodb://monitorUser:monitorPassword@mongodb:27017/admin
  ports:
    - "9216:9216"
  networks:
    kafka-network:
      ipv4_address: 172.20.0.8
  restart: always
```

### 5. Access the Services

- **Prometheus**: Visit `http://localhost:9090`.
- **Grafana**: Visit `http://localhost:3000` (default login: `admin/admin`).

### 6. Configure Grafana

1. Log in to Grafana.
2. Add Prometheus as a data source, pointing to `http://prometheus:9090`.
3. Import or create dashboards to visualize the weather alert data from MongoDB.

### 7. Example Prometheus Queries

#### Basic Queries

- Check if MongoDB is up:
  ```promql
  mongodb_up{job="mongodb_exporter"}
  ```

- Check memory usage in MongoDB:
  ```promql
  go_memstats_alloc_bytes{job="mongodb_exporter"}
  ```

#### Weather Alerts Queries

Use regex to filter and fetch weather alerts metrics from the `weather_alerts_db`:

```promql
{job="mongodb_exporter", __name__=~".*weather_alerts.*"}
```

### 8. Custom Queries for `weather_alerts_db`

Here are example queries for MongoDB metrics related to the `weather_alerts_db` database using Prometheus:

- **Filter all metrics for `weather_alerts_db`**:
  ```promql
  {job="mongodb_exporter", database="weather_alerts_db"}
  ```

- **Average object size**:
  ```promql
  mongodb_dbstats_avgObjSize{job="mongodb_exporter", database=~"weather_alerts_db"}
  ```

- **Number of collections**:
  ```promql
  mongodb_dbstats_collections{job="mongodb_exporter", database=~"weather_alerts_db"}
  ```

- **Total data size**:
  ```promql
  mongodb_dbstats_dataSize{job="mongodb_exporter", database=~"weather_alerts_db"}
  ```

- **Number of objects**:
  ```promql
  mongodb_dbstats_objects{job="mongodb_exporter", database=~"weather_alerts_db"}
  ```

### 9. Troubleshooting

- **MongoDB Exporter Issues**: Ensure the MongoDB URI is correctly configured in the `docker-compose.yml`.
- **Prometheus Not Scraping**: Check the logs for Prometheus and MongoDB exporter containers.
  ```bash
  docker logs mongodb_exporter
  docker logs prometheus
  ```

- **Grafana Issues**: Ensure that Grafana is properly connected to Prometheus.

### 10. Known Issues

- **Dynamic IP Assignment**: To avoid issues with dynamic IP assignment, ensure that the MongoDB exporter container is configured with a fixed IP by setting `ipv4_address` in the `docker-compose.yml`.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contributing

Feel free to submit issues or pull requests. Contributions are welcome!

## Author

- Christopher Overton - [christophermoverton](https://github.com/christophermoverton)

