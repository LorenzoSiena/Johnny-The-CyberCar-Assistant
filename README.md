# LINK DOWNLOAD TESI IN ITALIANO 
[**CLICCA QUI**](https://raw.githubusercontent.com/LorenzoSiena/Johnny-The-CyberCar-Assistant/main/Tesi_ita.pdf)

# VIDEO DEMO FROM PC CLIENT



https://github.com/user-attachments/assets/d688e4d2-7bd9-4638-b752-de8a21699676




# THIS REPO IS STILL A WORK IN PROGRESS

This repository is the main part of the project and thesis for deploying ****Johnny**** a full AI agent assistant running on your pc and reachable from your car with a dedicated hardware.

![img-ZZRZglqFedHgg9lLELXRt](https://github.com/user-attachments/assets/d5b674d8-e604-4692-8e8c-10bf5fc962b7)

It is part of the following 

# Repositories
## [Cheshire Cat](https://github.com/cheshire-cat-ai/core), the framework
## [Local whisper cat](https://github.com/LorenzoSiena/local_whisper_cat) his local transcriber Plugin
## [Chattino](https://github.com/LorenzoSiena/chattino) The Arduino/Esp32 Client for embebbed and for the car application
## [Chatty](https://github.com/LorenzoSiena/chatty) The Python Client for pc usage and more


# A CYBERCAR STUDY FROM THE EDGE TO THE CLOUD  
**(The original Thesis)** [TESI_IN_PDF_ITALIAN ONLY](https://raw.githubusercontent.com/LorenzoSiena/Johnny-The-CyberCar-Assistant/main/Tesi_ita.pdf)

## Abstract
The thesis explores and verifies the state of the art for IoT, experimenting with and prototyping a Cybercar with a voice assistant on the vehicle. 
It uses edge computing for keyword spotting and cloud computing for hosting an open-source, multilingual, and customizable AI chatbot on a consumer GPU (GTX 1080 Ti). 
This system can interact with the user vocally and, when connected to the car's internal bus, can perceive the car's status.

A proof of concept, a prototype and a case study are presented, in addition to all
the necessary hardware and software documentation. 
The study shows that it is possible with current technologies to make a common car smart, up to a
level and depth that depends on the access to the internal dynamics of the car,
accessible from the OBD-II debug port and using the protocols available on the bus.  

##  Introduction
Ideally this project aims to be the real version of kitt from Knight Rider tv show, the goal is to have an agent to interact with vocally able to read the internal state of the car (CAN BUS and similar) and to respond remotely to user requests with the personality of a cat named Jhonny.
The project is deployable on a common local PC equipped with a video card and requires hardware well described in the next section.

![Agent](https://github.com/user-attachments/assets/aa4df7fa-6a69-4e08-9c44-cb00b836e68e)


## Architecture
Description of the overall system architecture, including hardware and software components.

### The Edge

![5987884611008577933](https://github.com/user-attachments/assets/e9a4430c-3215-4107-bd1f-12c0f5a0ef1a)

### The Cloud

![5987884611008577929](https://github.com/user-attachments/assets/5d0d6e99-1ba7-474b-a537-e256405b2eaa)



## Documentation
Complete documentation can be found in the [downloadable pdf thesis](https://raw.githubusercontent.com/LorenzoSiena/Johnny-The-CyberCar-Assistant/main/Tesi_ita.pdf).
N.B. the thesis is not in English (it was written in italian), but using NotebookLM by google you can converse with the file in your own language (or you can simply translate it with other tools).

## HOW TO DEPLOY
### SETUP

### Docker-Compose Script on the Cloud Machine
```yaml
networks:
    fullcat-network:
services:
    cheshire-cat-core:
        build:
            context: ./core
        container_name: cheshire_cat_core
        depends_on:
            - cheshire-cat-vector-memory
            - ollama
            - openai-whisper-asr-webservice
        environment:
            - PYTHONUNBUFFERED=1
            - WATCHFILES_FORCE_POLLING=true
            - CORE_HOST=${CORE_HOST:-localhost}
            - CORE_PORT=${CORE_PORT:-1865}
            - QDRANT_HOST=${QDRANT_HOST:-cheshire_cat_vector_memory}
            - QDRANT_PORT=${QDRANT_PORT:-6333}
            - CORE_USE_SECURE_PROTOCOLS=${CORE_USE_SECURE_PROTOCOLS:-}
            - API_KEY=${API_KEY:-}
            - LOG_LEVEL=${LOG_LEVEL:-DEBUG}
            - DEBUG=${DEBUG:-true}
            - SAVE_MEMORY_SNAPSHOTS=${SAVE_MEMORY_SNAPSHOTS:-false}
        ports:
            - ${CORE_PORT:-1865}:80
        volumes:
            - ./cat/static:/app/cat/static
            - ./cat/public:/app/cat/public
            - ./cat/plugins:/app/cat/plugins
            - ./cat/metadata.json:/app/metadata.json
        restart: unless-stopped
        networks:
            - fullcat-network
    
    cheshire-cat-vector-memory:
        image: qdrant/qdrant:latest
        container_name: cheshire_cat_vector_memory
        expose:
            - 6333
        volumes:
            - ./cat/long_term_memory/vector:/qdrant/storage
        restart: unless-stopped
        networks:
            - fullcat-network
            
    ollama:
        container_name: ollama_cat
        image: ollama/ollama:latest
        volumes:
            - ./ollama:/root/.ollama
        expose:
            - 11434
        environment:
            - gpus=all
        deploy:
            resources:
                reservations:
                    devices:
                        - driver: nvidia
                          count: 1
                          capabilities:
                              - gpu
        networks:
            - fullcat-network
            
    openai-whisper-asr-webservice:
        deploy:
            resources:
                reservations:
                    devices:
                        - driver: nvidia
                          count: all
                          capabilities:
                              - gpu
        ports:
            - 9000:9000
        expose:
            - 9000
        environment:
            - ASR_MODEL=base
            - ASR_ENGINE=openai_whisper
        image: onerahmet/openai-whisper-asr-webservice:latest-gpu
        networks:
            - fullcat-network
```
### RUN
```yaml
#To start services
docker compose up -d
#To stop services
docker compose down
```


## License
This repository is licensed under the GPL 3.0 License.
