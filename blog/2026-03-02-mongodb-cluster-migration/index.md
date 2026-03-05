---
slug: mongodb-in-cluster-cloud-with-migration-process
title: MongoDB in Cluster Cloud with Migration Process
description: One of the most repetitive processes is migrating relational and non-relational databases from our on-premises environment to a cloud environment.
authors: [desobsesor]
tags: [vibra, mongodb, cloud, migration]
---
<p>
<img src="/img/mongodb.png" alt="mongodb" width="80%" height="100%" />
</p>

:::tip
Vibra stores its information in non-relational, document- and collection-oriented databases. It has deployed its databases in MongoDB in the cloud, in a service known as Atlas.
:::

🚀 **Step-by-Step Guide: Migrating MongoDB from Local to Atlas**

One of the most repetitive processes is migrating relational and non-relational databases from our on-premises environment to a cloud environment. This short post explains how to configure Atlas, create a backup, and restore a database in a basic migration process.

### 1️⃣ Prepare MongoDB Atlas
✅ **Create a Cluster**: If you haven't already, head over to MongoDB Atlas and set up a free cluster (M0). Follow this [guide](#) for detailed instructions.
✅ **User Configuration**: Set up a user with read/write permissions (e.g., migrator).
✅ **Network Access**: Add your current IP (or use 0.0.0.0/0 temporarily) in Network Access.

**Get the Atlas Connection String**:
<p>
✅ Navigate to your cluster in https://cloud.mongodb.com/ and click "Connect". 
</p>
<p>
✅ Choose "Connect your application".
</p>
<p>
✅ Copy the Connection String (e.g., `mongodb+srv://user:password@clusterxxx.mongodb.net/`).
</p>

**MongoDB on MongoDB Atlas (Free)**

:::tip
  MongoDB Atlas is a cloud service that allows you to deploy, manage, and scale MongoDB databases without having to manually configure servers. It's ideal for beginners because it offers a free tier with sufficient resources for small or test projects.
:::
 
📌 MongoDB Atlas
<p>
✅ Free to start: 512 MB of storage (enough for development).
</p>
<p>
✅ Managed: You don't need to install MongoDB on your computer.
</p>
<p>
✅ Scalable: You can upgrade as your application grows.
</p>
<p>
✅ Secure: Includes authentication, encryption, and automatic backups.
</p>
<p>
✅ Global: You can choose your server region for best performance.
</p>

### 2️⃣ Export Local Data with `mongodump`
✅ **Backup Data Locally**:
run the following command:
```bash
  mongodump --host localhost --port 27017 --db vibra-db --out C://Users/USER/Personal/mongodb_backups_vibra_2025
```
✅ **Restore Data to Cloud**:
run the following command:
```bash
  mongorestore --uri="mongodb+srv://username:Password@cluster0xxx.xxxx.mongodb.net/" C://Users/USER/Personal/mongodb_backups_vibra_2025/
```

**Vibra** is an academic app for students who want to validate and share their thoughts and experiences with other students.




