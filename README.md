# EFK
How to use and deploy efk

## Set up password
```
docker exec -it efk-elasticsearch-1 bash
elasticsearch-setup-passwords auto
# Put password to compose.yaml
```
