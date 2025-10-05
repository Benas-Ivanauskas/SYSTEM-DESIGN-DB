# DATABASE SYSTEM DESIGN GUIDE

This repository contains notes, resources, and practical exercises on **database system design**, covering **core concepts**, **database types**, and **hands-on tasks** with **PostgreSQL, MongoDB, and Redis**.

---

## 📚 CORE CONCEPTS

### 1. CAP THEOREM
- **Definition:** Trade-offs between **Consistency, Availability, and Partition Tolerance** in distributed systems.
- **Key Idea:** At most two of these three properties can be guaranteed simultaneously.
- **Resources:**
  - [YouTube: CAP Theorem Explained](https://www.youtube.com/watch?v=BHqjEjzAicA)
  - [IBM: CAP Theorem Overview](https://www.ibm.com/think/topics/cap-theorem)

### 2. ACID VS BASE
- **ACID:** Strong consistency model for relational databases.
  - `Atomicity, Consistency, Isolation, Durability`
- **BASE:** Looser consistency for distributed NoSQL systems.
  - `Basically Available, Soft state, Eventually consistent`
- **Resources:**
  - [AWS: ACID vs BASE](https://aws.amazon.com/compare/the-difference-between-acid-and-base-database/)
  - [GeeksforGeeks: ACID vs BASE](https://www.geeksforgeeks.org/dbms/acid-model-vs-base-model-for-database/)

### 3. DATABASE SHARDING
- **Definition:** Horizontal partitioning of data across multiple servers for scalability.
- **Important:** Consider **cardinality** when choosing shard keys.
- **Resources:**
  - [AWS: What is Database Sharding](https://aws.amazon.com/what-is/database-sharding/)
  - [Sharding Techniques](https://www.techtalksbyanvita.com/post/sharding-a-database-optimization-technique)
  - [MongoDB Shard Key Considerations](https://www.mongodb.com/docs/manual/core/sharding-choose-a-shard-key/#std-label-shard-key-range)

### 4. DATABASE REPLICATION
- **Definition:** Copying data across multiple nodes to improve **availability, fault tolerance, and read scalability**.
- **Resource:**
  - [Medium: Understanding Database Replication](https://medium.com/@atakanserbes/understanding-database-replication-a-practical-overview-dbe23344ac)

### 5. RELATIONAL DATABASES
- **Definition:** Databases based on **tables, rows, and relationships**.
- **Guidelines:** Codd’s 12 rules and normalization.
- **Key Topics:**
  - `Relationships: 1:1, 1:N, N:M`
  - `Normalization: 1NF, 2NF, 3NF, BCNF`
- **Resources:**
  - [Relational Model](https://en.wikipedia.org/wiki/Relational_model)
  - [Codd's 12 Rules](https://en.wikipedia.org/wiki/Codd%27s_12_rules)
  - [Database Normalization Guide](https://www.geeksforgeeks.org/dbms/introduction-of-database-normalization/)
  - [SQL Relationships](https://www.geeksforgeeks.org/sql/relationships-in-sql-one-to-one-one-to-many-many-to-many/)
  - [Video: Relational Databases Deep Dive](https://www.youtube.com/watch?v=1RCMYG8RUSE&t=5226s)

### 6. NOSQL DATABASE TYPES
- **Types:** Document (MongoDB), Key-Value (Redis), Column-Family (Cassandra), Graph (Neo4j)
- **Resources:**
  - [GeeksforGeeks: Types of NoSQL Databases](https://www.geeksforgeeks.org/dbms/types-of-nosql-databases/)

### 7. POPULAR DATABASE SYSTEMS

**POSTGRESQL**
- Advanced relational database with support for **JSONB**, **PostGIS**, and strong ACID compliance.
- **Resources:**
  - [PostgreSQL vs MySQL](https://medium.com/the-table-sql-and-devtalk/difference-between-postgresql-and-mysql-a-comparison-1)
  - [AWS: MySQL vs PostgreSQL](https://aws.amazon.com/compare/the-difference-between-mysql-vs-postgresql/)

**MONGODB**
- Document-oriented NoSQL DB with **embedded documents**, **transactions**, and **full-text search**.
- **Resources:**
  - [MongoDB Fundamentals](https://www.mongodb.com/resources/products/fundamentals/basics?utm_source=chatgpt.com)
  - [MongoDB Introduction](https://www.deepnebula.dev/blog/mongodbintroduction?utm_source=chatgpt.com)

**REDIS**
- In-memory key-value store for **caching, sessions, and leaderboards**.
- **Features:** TTL, sorted sets, pub/sub, atomic operations.
- **Resources:**
  - [Redis Explained](https://dev.to/amitchandra/redis-explained-key-features-use-cases-and-a-hands-on-project-1hdf?utm_s…)
  - [YouTube: Redis Overview](https://www.youtube.com/watch?v=jgpVdJB2sKQ)

---

## 🛠 HANDS-ON TASKS

1. POSTGRES

Spin up Postgres in Docker, enable PostGIS (CREATE EXTENSION postgis), and keep the simple users / events API: GET /recent-events/:userId?limit=N for the latest events, and POST /events/batch that inserts multiple events in one transaction (rollback on any error). In the events table add a flexible properties JSONB and a geom column (a point for lat/lon). Add a small /nearby?lat=&lon=&km= endpoint that uses PostGIS to find events near a location—create the point with ST_MakePoint(lon,lat), check radius with ST_DWithin(geom, point, km*1000), and (optionally) sort by ST_Distance(geom, point); add a geospatial index on geom to keep it snappy. Also add an index on a field of the event jsonb column. Use explain analyze to make sure that your queries execute with indexes

 
2. MONGO

Build a small Node.js app + Mongo (docker). Collections: Posts + Comments + Tags. Let Posts embed Tags but store Comments in separate collection. Implement: (a) fetch posts with tags + recent comments; (b) full-text search on posts' content; (c) update operation that moves a post to “archived” (collection or status) AND decrement a counter in the Tags collection (transaction across two collections).

 
3. REDIS

Spin up Redis in Docker and build a tiny Node.js app with just three bits: (1) sessions with TTL — on login, create sess:{token} with a 30-min expiry and refresh it on each /me call to show sliding sessions; (2) a leaderboard using a sorted set — POST /score adds points and GET /top returns the top 10 via ZINCRBY/ZREVRANGE; and (3) a simple cache — GET /posts/recent first checks cache:recentPosts, else computes and stores it with SETEX (e.g., 60s), and POST /posts deletes that key to invalidate.
 
