ARG DEBEZIUM_VERSION
FROM debezium/connect:$DEBEZIUM_VERSION

USER kafka
ADD confluentinc-kafka-connect-elasticsearch-11.0.6.tar.xz $KAFKA_CONNECT_PLUGINS_DIR
