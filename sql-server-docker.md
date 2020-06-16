# SQL Server Docker

Neste artigo vou mostrar como podemos criar uma imagem do SQL Server e executar ela via Docker em poucos minutos!

## Docker
Para executar estes passos você precisa instalar o Docker antes, 
https://balta.io/blog/docker-instalacao-configuracao-e-primeiros-passos

### Container
Container é a forma como executamos uma imagem no Docker

```
// https://hub.docker.com/_/microsoft-mssql-server
docker pull mcr.microsoft.com/mssql/server
docker images
docker run --name sqlserver -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=1q2w3e4r@#$" -p 1433:1433 -d mcr.microsoft.com/mssql/server
```