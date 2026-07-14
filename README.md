# README #

### What is this repository for? ###

This repository contains the instructions and docker-compose files to enable anyone to run a local copy of the ONS Address Index Matching Service.

The standard version comes with an Elasticsearch cluster (free sample AddressBase data preloaded), a tool to administer the cluster, a REST API and a Python Flask Web-UI. 

These components are all supplied via public Docker images stored in a Google Cloud Platform Artifact Registry. When any of these are updated, a new release of AIMS DIY will be created. Users can subscribe to the release if they want to be informed when this happens. Releases can be found here [https://github.com/ONSdigital/aims-diy/releases](https://github.com/ONSdigital/aims-diy/releases).

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

   If the UI does not load, it is most likely because the API needs to be restarted to pick up the Epoch number

5) Use of Docker alternatives: The docker-compose script works with Colima without modification. For Podman, you will need to enable podman compose (can be done from the Podman Desktop) and if this points to docker-compose rather than podman-compose, docker-compose up will work without changes.
       
### How do I load my own index? ###

This has its own README file in the [customdata](customdata/README.md) directory. 

### How do I Interpret Results? ###

# How to Interpret AIMS Results

## Confidence Score

The confidence score indicates how likely the returned address is to be correct.

| Score | Typical Accuracy* |
|---------|---------|
| 55 | ~10% |
| 60 | ~60% |
| 70+ | ~90% |

> *These are broad examples based on historical dataset performance. Actual matching success may vary.*

## Recommendation Code

| Code | Meaning | 
|--------|--------|
| **A** (Accept)| Clear best match. This has a typical accuracy of 97.5%. |
| **I** (Investigate)| No clear winner. Review the result. |

AIMS compares the top match against other potential matches and automatically assigns the recommendation.

> *Results with an 'A' have a typical accuracy of 97.5%.*

## Result Limits Matter

Returning fewer results can hide important context.

| Limit | Possible Results |
|---------|---------|
| 1 | None or one match |
| 5+ | None, one or many matches |

A result returned with a limit of **1** may have several equally good alternatives **hidden from view.**

# Common Pitfalls When Interpreting Results

## Garbage In, Garbage Out
Poor quality results may be indicative of poor quality input query data. To improve results, consider using the [Address Cleanup Functions](http://www.example.com), or check your dataset for issues.

## Assuming "A" Means Correct

**A** means "best available match", not "guaranteed correct".

## Assuming "I" Means Multiple Matches Exist

"**I**" means no _clear_ winner.

This could be:

- One weak match
- Several similar matches

# Examples

## Single Search (Limit = 1)

// For consistncy, the table headings are repeated in each example.
| Address ID | Rank | Score | Rec |
|------------|------|--------|--------|
| Rank | Score | Rec |
|------|------|------|
| 
| 1 | 62 | I |

**Interpretation:** One result shown. Other competing matches may be hidden.

## Single Search (Limit = 5)

| Address ID | Rank | Score | Rec |
|------------|------|--------|--------|
| A001 | 1 | 62 | I |
| A002 | 2 | 62 | I |
| A003 | 3 | 62 | I |
| A004 | 4 | 61 | I |
| A005 | 5 | 61 | I |

**Interpretation:** No obvious winner exists.

## Bulk Search (Limit = 1)

| Address ID | Rank | Score | Rec |
|------------|------|--------|--------|
|| A001 | 1 | 74 | A |
| A002 | 1 | 61 | I |
| A003 | 1 | No Match | - |

**Interpretation:** A002 may have hidden alternatives.

## Bulk Search (Limit = 5)

| Address ID | Rank | Score | Rec |
|------------|------|--------|--------|
| A002 | 1 | 61 | I |
| A002 | 2 | 61 | I |
| A002 | 3 | 60 | I |

**Interpretation:** Multiple similarly scored candidates explain the Investigate recommendation.
