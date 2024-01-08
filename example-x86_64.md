# Setup Enviroment Variables
    ARCHITECTURE=x86_64
    GHC_VERSION=8.10.7
    CABAL_VERSION=3.8.1.0
    NODE_TAG=8.7.2
    CLI_PATH=8.17.0.0
    API_VERSION=8.7.2
    DB_VERSION=13.1.1.3

# Build Base image
    docker build \
        --build-arg GHC_VERSION=${GHC_VERSION} \
        --build-arg CABAL_VERSION=${CABAL_VERSION} \
        -t cardano_env:latest \
        ./Dockerfiles/build_env/${ARCHITECTURE}

# Cardano Node image
    docker build \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg GHC_VERSION=${GHC_VERSION} \
        --build-arg CABAL_VERSION=${CABAL_VERSION} \
        --build-arg NODE_TAG=${NODE_TAG} \
        --build-arg CLI_PATH=${CLI_PATH} \
        -t cardano_node:latest Dockerfiles/node

# SubmitAPI image
    docker build \
        --no-cache \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg NODE_TAG=${NODE_TAG} \
        --build-arg RELEASE_PATH=${API_VERSION} \
        -t cardano_submit:latest Dockerfiles/submit

# DB-Sync image
    docker build \
        --no-cache \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg RELEASE=${DB_VERSION} \
        -t cardano_db_sync:latest Dockerfiles/db-sync
