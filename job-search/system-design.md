### Systems Design

### Key Generation Service

Randomly generate 6 letter strings beforehand to store in a database and make sure all of the keys are unique and quickly present new key to be used.

#### Answer

- Use a robust random number generator to ensure uniform distribution of keys.

- Choose a database that supports efficient unique constraints and fast read/write operations. A relational database (e.g., PostgreSQL) or a NoSQL database (e.g., MongoDB) could be used.

- Design a table or collection to store the keys, ensuring the key field has a unique constraint.

##### Workflow

- Generate a Key
  - Generate a random 6-letter string.
  - Check if the key already exists in the database.
  - If it exists, generate a new key and repeat the check.
  - If it does not exist, store the key in the database.
- Retrieve a Key
  - Query the database to retrieve a key.
  - Ensure the retrieved key is marked or flagged to prevent future reuse if necessary.

---

### Read/Write separation

Separate read and write to scale and optimize upload and download requests and into separate services

#### Answer

**System Components:**

- **Write Service**: Handles all data uploads.
- **Read Service**: Handles all data downloads.
- **Database (Primary and Replica)**: Primary database for writes and replicas for reads.
- **Load Balancer**: Distributes read requests among replicas.
- **Message Queue**: Ensures eventual consistency between the primary and replica databases.

**Architecture Design**:

- **Write Path**:
  - Client sends a write request to the Write Service.
  - Write Service writes data to the Primary Database.
  - The Primary Database asynchronously replicates the data to one or more Replica Databases via a message queue or a replication mechanism.
- **Read Path**:
  - Client sends a read request to the Read Service.
  - Read Service queries the Load Balancer.
  - Load Balancer distributes the read request to one of the Replica Databases.
  - Replica Database returns the data to the Read Service, which then responds to the client.

**Database Selection**: Choose a database system that supports read replicas and asynchronous replication (e.g., PostgreSQL, MySQL, MongoDB).

**Load Balancer**: Implement a load balancer for distributing read requests (e.g., HAProxy, Nginx, AWS ELB).

**Message Queue**: Use a message queue to handle asynchronous replication and ensure eventual consistency (e.g., Apache Kafka, RabbitMQ).

**Handling Data Consistency**:

- Ensure that the system can handle eventual consistency. Write operations are immediately visible in the primary database but may take time to propagate to replicas.
- Implement a mechanism to handle read-after-write consistency if required (e.g., by reading from the primary database for a short period after a write).

---

### Replication

Reliability and redundancy via duplicating upload and download image services

#### Answer

**System Components**:

- **Upload Service**: Handles image uploads.
- **Download Service**: Handles image downloads.
- **Primary Storage**: Main storage location for uploaded images.
- **Replica Storage**: Additional storage locations for redundancy.
- **Load Balancer**: Distributes requests among multiple service instances.
- **Message Queue**: Ensures eventual consistency and reliable replication.

**Architecture Design**:

- **Upload Path**:
  - Client sends an image to the Upload Service.
  - Upload Service stores the image in the Primary Storage.
  - Upload Service sends a message to the Message Queue to replicate the image.
  - Replica Services (subscribers to the Message Queue) replicate the image to Replica Storage locations.
- **Download Path**:
  - Client requests an image from the Download Service.
  - Download Service queries the Load Balancer.
  - Load Balancer distributes the request to an appropriate Replica Storage.
  - If a replica fails, the Load Balancer redirects the request to another available replica.

**Primary and Replica Storage**: Choose a distributed storage system that supports replication (e.g., Amazon S3, Google Cloud Storage, or a custom solution using a distributed file system like HDFS).

**Load Balancer**: Implement a load balancer for distributing requests (e.g., HAProxy, Nginx, AWS ELB).

**Message Queue**: Use a reliable message queue to handle replication (e.g., Apache Kafka, RabbitMQ).

**Health Monitoring**: Use a monitoring system to check the health of storage and services (e.g., Prometheus, Grafana, AWS CloudWatch).