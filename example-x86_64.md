# Setup Enviroment Variables
    ARCHITECTURE=x86_64
    GHC_VERSION=9.6.4
    CABAL_VERSION=3.10.1.0
    NODE_TAG=8.7.3
    CLI_PATH=8.17.0.0
    API_VERSION=3.2.1
    DB_VERSION=13.1.1.3

# Build Base image
    docker build \
        --no-cache \
        --build-arg GHC_VERSION=${GHC_VERSION} \
        --build-arg CABAL_VERSION=${CABAL_VERSION} \
        -t cardano_env:${NODE_TAG} \
        ./Dockerfiles/build_env/${ARCHITECTURE}

# Cardano Node image
    docker build \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg GHC_VERSION=${GHC_VERSION} \
        --build-arg NODE_TAG=${NODE_TAG} \
        --build-arg CLI_PATH=${CLI_PATH} \
        -t cardano_node:${NODE_TAG} Dockerfiles/node

# SubmitAPI image
    docker build \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg NODE_TAG=${NODE_TAG} \
        --build-arg GHC_VERSION=${GHC_VERSION} \
        --build-arg API_VERSION=${API_VERSION} \
        -t cardano_submit:${API_VERSION} Dockerfiles/submit

# DB-Sync image
    docker build \
        --build-arg ARCHITECTURE=${ARCHITECTURE} \
        --build-arg GHC_VERSION=${GHC_VERSION} \
        --build-arg RELEASE=${DB_VERSION} \
        -t cardano_db_sync:${DB_VERSION} Dockerfiles/db-sync

# Tag builds as latest
     docker tag cardano_env:${NODE_TAG} cardano_env:latest
     docker tag cardano_node:${NODE_TAG} cardano_node:latest
     docker tag cardano_submit:${API_VERSION} cardano_submit:latest
     docker tag cardano_db_sync:${DB_VERSION} cardano_db_sync:latest
