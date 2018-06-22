```yml
---
version: '2.4'
services:
    elasticsearch:
        image: ${ELASTICSEARCH_IMAGE}
        environment:
            - 'discovery.type=single-node'
            - 'bootstrap.memory_lock=true'
            - 'ES_JAVA_OPTS=-Xms256m -Xmx256m'
#        ports:
#            - 9200:9200
#            - 9300:9300
        volumes:
            - type: bind
              source: /var/lib/elasticsearch
              target: /usr/share/elasticsearch/data
        networks:
            - efk
        depends_on:
            - fluentd
#        logging:
#            driver: "json-file"
#            options:
#                max-size: "500M"
#                max-file: "2"
        logging:
            driver: fluentd
            options:
                fluentd-address: localhost:24224
                fluentd-async-connect: 'true'
                fluentd-retry-wait: '1s' 
                fluentd-max-retries: '30'
                tag: efk.elasticsearch

    kibana:
        image: ${KIBANA_IMAGE}
        ports:
            - 5601:5601
        networks:
            - efk
        depends_on:
            - elasticsearch
#        logging:
#            driver: "json-file"
#            options:
#                max-size: "500M"
#                max-file: "2"
        logging:
            driver: fluentd
            options:
                fluentd-address: localhost:24224
                fluentd-async-connect: 'true'
                fluentd-retry-wait: '1s' 
                fluentd-max-retries: '30'
                tag: efk.kibana

    fluentd:
        image: ${FLUENTD_IMAGE}
        ports:
            - 24224:24224
#            - 24224:24224/udp
        volumes:
            - ./fluentd/etc:/fluentd/etc
        networks:
            - efk

networks:
    efk:
        driver: bridge
```

- - -

RE: Exploring fluentd via EFK Stack for Docker Logging, [http://kelonsoftware.com/fluentd-docker-logging/](http://kelonsoftware.com/fluentd-docker-logging/)
