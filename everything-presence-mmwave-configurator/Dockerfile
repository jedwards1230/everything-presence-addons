FROM alpine:3.19

ENV LANG C.UTF-8
ENV PIP_ROOT_USER_ACTION=ignore

RUN apk add --no-cache ca-certificates && \
    update-ca-certificates

RUN apk update && \
    apk add --no-cache nginx python3 py3-pip
    
RUN apk add py3-requests py3-flask

# Install Python packages 
RUN apk add --no-cache \
    py3-setuptools \
    py3-wheel \
    py3-websocket-client

# Install Flask-Sock for WebSocket support
RUN pip3 install --no-cache-dir --break-system-packages flask-sock

# Add s6-overlay for process supervision
ARG S6_OVERLAY_VERSION=3.1.6.2
ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-noarch.tar.xz /tmp
ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-x86_64.tar.xz /tmp
RUN tar -C / -Jxpf /tmp/s6-overlay-noarch.tar.xz && \
    tar -C / -Jxpf /tmp/s6-overlay-x86_64.tar.xz && \
    rm /tmp/s6-overlay-*.tar.xz

# Copy nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Copy your application files
COPY www /usr/share/nginx/html

# Copy backend script
COPY backend.py /app/backend.py

# Copy services
COPY services/ /etc/services.d/
RUN chmod +x /etc/services.d/backend/run
RUN chmod +x /etc/services.d/nginx/run

# Expose the ingress port
EXPOSE 8099

# Set entrypoint for s6-overlay
ENTRYPOINT ["/init"]