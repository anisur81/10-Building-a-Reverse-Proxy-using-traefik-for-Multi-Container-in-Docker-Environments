# Here are the steps for connecting multiple containers:

---

**13. Connect Multiple Containers (Optional)**

- Create a Docker network for inter-container communication.

```
docker network create app-network
```

- Run the database container and connect it to the network.

```
docker run -d --name database \
  --network app-network \
  -e MYSQL_ROOT_PASSWORD=rootpassword \
  -e MYSQL_DATABASE=myapp \
  mysql:8.0
```

- Run the application container and connect it to the same network.

```
docker run -d -p 8080:80 --name my-app-container \
  --network app-network \
  -e DB_HOST=database \
  -e DB_USER=root \
  -e DB_PASSWORD=rootpassword \
  -e DB_NAME=myapp \
  your-app-name:v1.0.0
```

- Verify the containers are connected to the same network.

```
docker network inspect app-network
```

- Test communication between containers using container names instead of IP addresses.

```
docker exec my-app-container ping database
```

- To disconnect a container from the network.

```
docker network disconnect app-network my-app-container
```

- To remove the network when no longer required.

```
docker network rm app-network
```
