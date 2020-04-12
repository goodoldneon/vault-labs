FROM vault:1.4.0

RUN apk add --update-cache \
    bash \
    curl \
    jq \
    && rm -rf /var/cache/apk/*

RUN touch $HOME/.bashrc

RUN vault -autocomplete-install
