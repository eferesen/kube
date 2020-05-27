# Mongo Setup Optional

1. Create a randomly generated key
   ```
   # Create Key
   openssl rand -base64 741 > mongo-secret
   ```
2. Run create the Mongo secret with k8s
   ```
    # Create Secret
    k create secret generic mongo --from-file=mongo-secret
   ```
3. Create gloud ssd storage class
   ```
    k apply -f googlecloud_ssd.yml
   ```
4. Setup Mongo deployment and Service
   ```
   k apply -f mongo-deployment.yml
   ```
5. Mongo Admin Setup - Manual Process for now - We need to automate this step

   ```

    # Check to see if all deployments and PVCs are created
    k get all
    k get pvc

    # Login to mongo replica
    k exec -it mongod-0 -c mongod-container bash

    # Use mongo client
    mongo

    # Do a replica set init
    rs.initiate({_id: "MainRepSet", version: 1, members: [
       { _id: 0, host : "mongod-0.mongo.default.svc.cluster.local:27017" },
       { _id: 1, host : "mongod-1.mongo.default.svc.cluster.local:27017" },
       { _id: 2, host : "mongod-2.mongo.default.svc.cluster.local:27017" }
   ]});

   # Check replica status
   rs.status();

   # Create Admin Account
   db.getSiblingDB("admin").createUser({
      user : "admin",
      pwd  : "Admin Pass ",
      roles: [ { role: "root", db: "admin" } ]
   });

   # Login in as admin and create an application account
   db.getSiblingDB('admin').auth("admin", "Your Password");

   # Create Db and user cred for the application
   use numberTracker
   db.createUser(
    {
        user: "numberTracker",
        pwd: "App Password",
        roles:[
            {
                role: "readWrite",
                db: "numberTracker"
            }
        ]
    }
   );

   ```

```

6.
```
