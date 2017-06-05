## Running nginx even if upstream containers are offline

Let's say you are setting up a reverse proxy on a Docker bridge network with several backends (such as Jenkins, GitLab, and other).

The container names of the backends can be used as host names as they will be resolvable from within all containers bound to that virtual network.

However if any of the containers is not running or does not exist when nginx container boots, nginx will refuse to start with the following error:

    2017/06/05 14:52:02 [emerg] 1#1: host not found in upstream "your_upstream_container_name" in /etc/nginx/nginx.conf:19
    nginx: [emerg] host not found in upstream "your_upstream_container_name" in /etc/nginx/nginx.conf:19

This is circumventable by putting the name of the container inside a variable, then using this variable in place of the upstream host name3
