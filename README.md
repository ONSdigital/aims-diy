# README #

### What is this repository for? ###

This repository contains the instructions and docker-compose files to enable anyone to run a local copy of the ONS Address Index Matching Service.

The standard version comes with the free sample AddressBase data preloaded, and includes a REST API and a Python UI. The API is built using the Play Framework and the UI is built using Flask.

There is also a slightly different docker-compose file intended for those who have access to AddressBase Premium data and want to load their own index. 

The code for, and further details of the API, UI and Spark job to transform AddressBase data into an Elasticsearch index are in the repos below:

[API](https://github.com/ONSdigital/aims-api) - Play Framework application (Scala)  
[UI](https://github.com/ONSdigital/address-index-ui) - Python / Flask application
[Spark](https://github.com/ONSdigital/aims-spark) - Apache Spark job (Scala)

### How do I run the Dockerised AIMS? ###

You will need Docker (or equivalent such as Colima or Podman) installed. Then to get started:

1) Run ```docker-compose up``` on https://github.com/ONSdigital/aims-diy/blob/main/docker-compose.yml

2) The cluster status can be viewed with either Cerebro or Kibana:

        Cerebro: http://localhost:1234
        and then http://es:9200
    
        Kibana: http://localhost:5601
    (the compose file has kibana commented out, you can uncomment it if you want to use it)
    
3) To run API calls open a browser or API testing app, Postman etc.

        http://localhost:9001/
        
    Endpoints can be found in the Swagger definition /openapi/swagger.json. View as HTML with examples here: 

   https://github.com/ONSdigital/aims-api/tree/main/api-definitions
    
    The ```ai-swagger.json``` can be copied into the Swagger Editor to view it: 
    
    https://editor.swagger.io/

4) The UI points to the local API and is available on

        http://localhost:5000/

5) Use of Docker alternatives: The docker-compose script works with Colima without modification. For Podman, you will need to enable podman compose (can be done from the Podman Desktop) and if this points to docker-compose rather than podman-compose, docker-compose up will work without changes.
       
### How do I load my own index? ###

This has its own README file in the [customdata](customdata/README.md) directory. 
