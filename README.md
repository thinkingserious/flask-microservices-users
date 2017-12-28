# Initial Setup

```bash
docker-machine create -d virtualbox testdriven
eval "$(docker-machine env testdriven)"
docker-compose build
docker-compose up -d
TESTDRIVEN_IP="$(docker-machine ip testdriven)"
curl http://$TESTDRIVEN_IP:5001/ping
docker-compose logs -f users-service
```

# Remove Docker Machine

docker-machine rm testdriven

# Run Tests

docker-compose run users-service python manage.py test

# Rebuild and Run Docker Container in the Background

```bash
docker-compose up -d --build
```

# Kill all Running Docker Images

```bash
docker stop $(docker ps -a -q)
```

# Check Docker Logs

```bash
docker-compose logs -f users-service
```

# Get IP

```bash
docker-machine ip testdriven
```

# Test in Browser

http://192.168.99.100:5001/users

# Setup DB

```bash
docker-compose run users-service python manage.py recreate_db
docker-compose run users-service python manage.py seed_db
```

# Create and Configure an IAM User
```bash
IAM -> Create User
User name: testdriven
Groups: Test
Group Policy: AdministratorAccess
Region: us-east-1a
[Create a ~/.aws/credentials file](http://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html)
aws configure
```

Git access keys [here](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html).

# Create Deployment Machine

```bash
docker-machine create --driver amazonec2 awstestdriven
docker-machine env awstestdriven
eval $(docker-machine env awstestdriven)
```

Spin up the containers, create the database, seed, and run the tests:

```bash
docker-compose -f docker-compose-prod.yml up -d --build
docker-compose -f docker-compose-prod.yml run users-service python manage.py recreate_db
docker-compose -f docker-compose-prod.yml run users-service python manage.py seed_db
docker-compose -f docker-compose-prod.yml run users-service python manage.py test
docker-machine ip awstestdriven
```

Check environment

```bash
docker-compose -f docker-compose-prod.yml run users-service env
```
