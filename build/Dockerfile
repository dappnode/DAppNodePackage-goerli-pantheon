# Build Pantheon in a stock openjdk builder container

FROM openjdk:8-jdk as builder

RUN git clone https://github.com/PegaSysEng/pantheon.git && cd pantheon && ./gradlew installDist -x test

RUN git clone https://github.com/goerli/testnet.git && mkdir -p /goerli && \ 
    cp /testnet/geth/goerli.genesis /goerli/goerli.genesis && \
    cp /testnet/bootnodes.txt /goerli/bootnodes.txt 

# Pull Pantheon into a second stage deploy alpine container
FROM openjdk:8-jre-alpine

RUN apk add --update libc6-compat

# copy application (with libraries inside)
COPY --from=builder /pantheon/build/install/pantheon /opt/pantheon/
COPY --from=builder /goerli /goerli 

# List Exposed Ports
EXPOSE 8546 8545 30303

# specify default command
ENTRYPOINT /opt/pantheon/bin/pantheon --genesis=/goerli/goerli.genesis --datadir=/goerli/pantheon --network-id=6284 --rpc-enabled --rpc-cors-origins "all" --rpc-listen=0.0.0.0:8545 --ws-enabled --ws-listen=0.0.0.0:8546  --bootnodes=$(cat /goerli/bootnodes.txt | tr '\n' ',')
