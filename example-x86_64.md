# Build Base image
    ARCHITECTURE=x86_64
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
        -t cardano_node:${VERSION_NUMBER} Dockerfiles/node

# SubmitAPI image
    VERSION_NUMBER=8.7.2
    RELEASE_PATH=8.0.0
    docker build \
        --no-cache \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg RELEASE=${VERSION_NUMBER} \
        --build-arg RELEASE_PATH=${RELEASE_PATH} \
        -t cardano_submit:latest Dockerfiles/submit

# DB-Sync image
    DB_VERSION=13.1.1.3
    docker build \
        --no-cache \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg RELEASE=${DB_VERSION} \
        -t cardano_db_sync:${DB_VERSION} Dockerfiles/db-sync
