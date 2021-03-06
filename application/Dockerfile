FROM java:8-jre

MAINTAINER Alessandro Fael Garcia <alessfg@hotmail.com>

LABEL EtherCIS-Version="1.1.1" \
      Description="Docker image for the EtherCIS application"

# Create user and group ethercis
RUN groupadd ethercis \
  && useradd ethercis -g ethercis

# Create directories to store logs
RUN mkdir -p /home/ethercis/logs \
  && mkdir -p /etc/opt/ecis/knowledge/archetypes \
  && mkdir -p /etc/opt/ecis/knowledge/operational_templates \
  && mkdir -p /etc/opt/ecis/knowledge/templates \
  && mkdir -p /var/opt/ecis

# Give ethercis user permission access various directories
RUN chown -R ethercis:ethercis /home/ethercis/logs \
  && chown -R ethercis:ethercis /etc/opt/ecis/knowledge \
  && chown -R ethercis:ethercis /var/opt/ecis

# Copy EtherCIS files into their corresponding directories
COPY ./ethercis-1.1.1/conf /etc/opt/ecis
COPY ./ethercis-1.1.1/bin /opt/ecis/bin

COPY ./ethercis-1.1.1/lib/application /opt/ecis/lib/application
COPY ./ethercis-1.1.1/lib/deploy /opt/ecis/lib/deploy
COPY ./ethercis-1.1.1/lib/system /opt/ecis/lib/system
COPY ./wait-for-it.sh /opt/ecis/bin/wait-for-it.sh
COPY ./ethercis-1.1.1/bin/ecis-server.sh /opt/ecis/bin

# Make startup scripts executable
RUN chmod +x /opt/ecis/bin/populate-concept.sh && \
  chmod +x /opt/ecis/bin/ecis-server.sh && \
  chmod +x /opt/ecis/bin/ecis-dump-opt.sh && \
  chmod +x /opt/ecis/bin/wait-for-it.sh

# Expose port 8080 outside of docker
EXPOSE 8080

# Set up working directory for all relevant scripts
WORKDIR /opt/ecis/bin

# Initialisation scripts
# Wait until database is available
# Then proceed to populate database and start server
# Output server log to console
CMD ./wait-for-it.sh postgres:5432 -t 60 && \
  ./populate-concept.sh && \
  ./ecis-server.sh start && \
  tail -f /var/opt/ecis/ethercis_test.log
