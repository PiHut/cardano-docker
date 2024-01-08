# Setup Enviroment Variables
    ARCHITECTURE=x86_64
    NODE_TAG=8.7.2
    CLI_PATH=8.17.0.0
    API_VERSION=8.7.2
    DB_VERSION=13.1.1.3

# Build Base image
    docker build \
        -t cardano_env:latest \
        ./Dockerfiles/build_env/${ARCHITECTURE}

# Cardano Node image
    NODE_TAG=8.7.2
    CLI_PATH=8.17.0.0
    docker build \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg NODE_TAG=${NODE_TAG} \
        --build-arg CLI_PATH=${CLI_PATH} \
        -t cardano_node:${NODE_TAG} Dockerfiles/node

# SubmitAPI image
    docker build \
        --no-cache \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg NODE_TAG=${NODE_TAG} \
        --build-arg RELEASE_PATH=${API_VERSION} \
        -t cardano_submit:${API_VERSION} Dockerfiles/submit

# DB-Sync image
    docker build \
        --no-cache \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg RELEASE=${DB_VERSION} \
        -t cardano_db_sync:${DB_VERSION} Dockerfiles/db-sync

# Tag your image with the latest tag:
    docker tag cardano_node:${NODE_TAG} cardano_node:latest
    docker tag cardano_submit:${API_VERSION} cardano_node:latest 
    docker tag cardano_db_sync:${DB_VERSION} cardano_node:latest
