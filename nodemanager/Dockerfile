FROM jayachander/hadoop-base:1.0.0-hadoop3.3.2-java8

LABEL maintainer="Jayachander Surbiryala <jayachander.it@gmail.com>"

HEALTHCHECK CMD curl -f http://localhost:8042/ || exit 1

ADD run.sh /run.sh
RUN chmod a+x /run.sh

EXPOSE 8042

CMD ["/run.sh"]
