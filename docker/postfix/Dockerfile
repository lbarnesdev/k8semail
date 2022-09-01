FROM ubuntu:latest
RUN apt update && apt upgrade -y
RUN apt install -y postfix

ENTRYPOINT touch log && tail -f log