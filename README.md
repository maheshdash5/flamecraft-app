# Flask Employee REST API

A simple Flask REST API to manage employees.

## Setup

## API Endpoints

- `GET /employees` - List all employees
- `POST /employees` - Add new employee
- `GET /employees/<id>` - Get employee by ID
- `PUT /employees/<id>` - Update employee
- `DELETE /employees/<id>` - Delete employee

#Local Development
python -m venv venv
cd venv/bin
source activate
cd ../../
pip install -r requirements.txt

python app.py

# Run using gununicorn

gunicorn -c gunicorn.conf.py app:app

# Local Testing

# Get all employees

curl http://127.0.0.1:5500/employees

# Add a new employee

curl -X POST http://127.0.0.1:5500/employees \
 -H "Content-Type: application/json" \
 -d '{"name": "Charlie", "role": "Intern", "salary": 30000}'

# Get employee by ID

curl http://127.0.0.1:5500/employees/1

# Update employee

curl -X PUT http://127.0.0.1:5500/employees/1 \
 -H "Content-Type: application/json" \
 -d '{"role": "Senior Engineer", "salary": 185000}'

# Delete employee

curl -X DELETE http://127.0.0.1:5500/employees/1

# Docker setup for multi platform

docker buildx version
docker buildx create --name multi --use
docker run --privileged --rm tonistiigi/binfmt --install all
docker buildx inspect --bootstrap

# Build & push eg:

docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7 -t maheshkumardash/flamecraft:1.0.0 --push .

# Build & push the image to dockerHub

docker build -t maheshkumardash/flamecraft:1.0.0 .
docker push maheshkumardash/flamecraft:1.0.0

# Run the container for local testing

docker run -d -p 5500:5500 maheshkumardash/flamecraft:1.0.0

# Test the API

curl http://127.0.0.1:5500/employees

# Test in kubernetes cluster

kubectl run curl-pod --image=alpine -- /bin/sh -c "apk add --no-cache curl && sleep 3600"
kubectl exec -it curl-pod -- /bin/sh

# Excute below commands for testing

curl http://flamecraft-app-service.dev.svc.cluster.local:80/health
curl http://flamecraft-app-service.dev.svc.cluster.local:80/ready
curl http://flamecraft-app-service.dev.svc.cluster.local:80/employees

timeout 100s sh -c 'while true; do curl -s http://flamecraft-app-service.dev.svc.cluster.local:80/employees > /dev/null; done'

# Create concurrent parallel requests to generate load

timeout 10s sh -c '
for i in $(seq 1 5); do
while true; do
curl -s http://flamecraft-app-service.dev.svc.cluster.local:80/employees > /dev/null
done &
done
wait
'

# Test Endpoints

kubectl get endpointslices -n dev

# Add Kubernetes secrets

kubectl create secret docker-registry regcred \
 --docker-server=myregistry.example.com \
 --docker-username=MY_USERNAME \
 --docker-password=MY_PASSWORD \
 --docker-email=MY_EMAIL

docker-server=https://index.docker.io/v1/
docker-server=123456789012.dkr.ecr.us-east-1.amazonaws.com.
