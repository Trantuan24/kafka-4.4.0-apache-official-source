# Source mapping for Apache Kafka `4.4.0` (`apache/kafka:4.4.0`)

Tài liệu này ghi lại nguồn gốc (provenance) và cấu trúc bộ source cho **Apache Kafka `4.4.0`** (phiên bản stable mới nhất phát hành tháng 10/2026) cùng với image container chính thức:

```text
apache/kafka:4.4.0
apache/kafka-native:4.4.0
```

Repository tổng hợp này có cấu trúc:

```text
kafka-4.4.0-apache-official-source/
├── README.md
└── apache-kafka-4.4.0/          # source Apache Kafka 4.4.0 upstream (bao gồm cả docker/ chính chủ)
```

Thư mục con là snapshot source sạch tại revision được ghi bên dưới; metadata `.git` của repository upstream không được lồng vào repository tổng hợp.

## Tổng quan mapping

```text
apache-kafka-4.4.0
  Apache Kafka tag 4.4.0
  commit 1156b2752aaa28eafc0b4c4d387e63356793268d
                  │
                  ├── mã nguồn lõi Kafka upstream (Java/Scala)
                  └── docker/ (Dockerfile JVM & Native + runtime scripts chính thức)
                  │
                  ▼
  apache/kafka:4.4.0  (hoặc apache/kafka-native:4.4.0)
```

> **Lưu ý về Bitnami:** Từ tháng 08/2025, Broadcom/Bitnami đã ngừng cập nhật miễn phí các image Debian công khai và đóng băng nhánh legacy (`bitnamilegacy/kafka`) ở phiên bản `4.0.0-debian-12-r10`. Do đó với Apache Kafka `4.4.0`, bộ đóng gói container chuẩn là **Official Apache Kafka Docker Image** nằm trực tiếp trong thư mục `apache-kafka-4.4.0/docker/`.

## 1. Source Apache Kafka `4.4.0`

Thông tin đã ghim:

```text
Repository:    https://github.com/apache/kafka.git
Tag:           4.4.0 (tag object: 9661b1ef7a0120e476399250b31d530792506787)
Commit:        1156b2752aaa28eafc0b4c4d387e63356793268d
Version:       4.4.0
Scala Version: 2.13.18
Swagger:       2.2.52
License:       Apache License 2.0
```

Kafka `4.4.0` chạy hoàn toàn ở chế độ **KRaft** (đã loại bỏ hoàn toàn ZooKeeper từ bản 4.0.0) và yêu cầu **Java 17+** cho broker/tools.

## 2. Source đóng gói Docker chính thức (`apache-kafka-4.4.0/docker/`)

Các thành phần quan trọng:

- `docker/jvm/Dockerfile`: Dockerfile chính thức cho image `apache/kafka:4.4.0` (chạy trên JVM với dynamic CDS/JSA).
- `docker/native/Dockerfile`: Dockerfile cho image `apache/kafka-native:4.4.0` (GraalVM native image).
- `docker/resources/`: Các script khởi chạy và cấu hình runtime.
- `docker/examples/`: Các file `docker-compose` mẫu cho KRaft single-node và cluster.

Thông số trong `docker/jvm/Dockerfile`:

```text
Base image:   docker.io/library/eclipse-temurin:25-jre-alpine
Kafka path:   /opt/kafka
Config path:  /etc/kafka/docker
Data path:    /var/lib/kafka/data
Runtime user: appuser
Exposed port: 9092
```

## 3. Image chính thức & lệnh tải

```powershell
# Official JVM image
docker pull apache/kafka:4.4.0

# Official Native image
docker pull apache/kafka-native:4.4.0
```

## 4. Build từ source

### Build binary / JAR bằng Gradle

```powershell
cd "D:\12. VNPT-IT\OpenSource\kafka-4.4.0-apache-official-source\apache-kafka-4.4.0"
.\gradlew releaseTarGz
```

### Build Docker image từ `docker/jvm/Dockerfile`

Sau khi có file binary `kafka_2.13-4.4.0.tgz` (từ bước build Gradle hoặc tải từ `https://downloads.apache.org/kafka/4.4.0/kafka_2.13-4.4.0.tgz`):

```powershell
cd "D:\12. VNPT-IT\OpenSource\kafka-4.4.0-apache-official-source\apache-kafka-4.4.0\docker\jvm"
docker build -t local/kafka:4.4.0 --build-arg kafka_url="https://downloads.apache.org/kafka/4.4.0/kafka_2.13-4.4.0.tgz" .
```
