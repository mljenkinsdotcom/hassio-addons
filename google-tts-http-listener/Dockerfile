ARG BUILD_FROM
FROM $BUILD_FROM

# Install requirements for add-on
RUN apk add --no-cache python3

# Create symlink to TTS directory to expose TTS directory via HTTP
RUN mkdir -p /http_server/api
RUN ln -s /config/tts /http_server/api/tts_proxy

# Set working directory where the Python HTTP server will serve content from
WORKDIR /http_server

# Copy data
COPY run.sh /

RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
