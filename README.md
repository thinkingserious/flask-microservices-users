[![Build Status](https://travis-ci.org/thinkingserious/flask-microservices-users.svg?branch=master)](https://travis-ci.org/thinkingserious/flask-microservices-users)

# Initial Setup

```bash
docker-machine create -d virtualbox testdriven-dev
eval "$(docker-machine env testdriven-dev)"
docker-compose build
docker-compose up -d
TESTDRIVEN_IP="$(docker-machine ip testdriven-dev)"
curl http://$TESTDRIVEN_IP:5001/ping
docker-compose logs -f users-service
```

# Remove Docker Machine

docker-machine rm testdriven

# Run Tests

docker-compose -f docker-compose-dev.yml run users-service python manage.py test

# Run Tests with Coverage

docker-compose -f docker-compose-dev.yml run users-service python manage.py cov

# Run Flake Linter

docker-compose -f docker-compose-dev.yml run users-service flake8 project

# Rebuild and Run Docker Container in the Background

```bash
docker-compose -f docker-compose-dev.yml up -d --build
```

# Log into Environment

```bash
eval "$(docker-machine env testdriven-dev)"
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
eval $(docker-machine env testdriven-prod)
```

Spin up the containers, create the database, seed, and run the tests:

```bash
docker-compose -f docker-compose-prod.yml up -d --build
docker-compose -f docker-compose-prod.yml run users-service python manage.py recreate_db
docker-compose -f docker-compose-prod.yml run users-service python manage.py seed_db
docker-compose -f docker-compose-prod.yml run users-service python manage.py test
docker-machine ip testdriven-prod
```

Check environment

```bash
docker-compose -f docker-compose-prod.yml run users-service env
```

# Regnerate Certs if "Unable to query Docker version"

```bash
docker-machine regenerate-certs testdriven-prod
```

# Stuck in Saved State?

1. Start virtualbox - virtualbox
2. Select the VM and click "start"
3. Exit the VM and select "Power off the machine"
4. Exit virtualbox

# Stop the containers

```bash
docker-compose stop
```

# Bring down the containers

```bash
docker-compose down
```

# Force a build

```bash
docker-compose build --no-cache
```

# Remove images

```bash
docker rmi $(docker images -q)
```
# Postgres Access

```bash
docker exec -ti users-db psql -U postgres -W
\c users_dev
select * from users;
```