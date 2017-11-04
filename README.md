# Jenkins self-hosting configuration
## Installing and configuring Jenkins for the first time
In this 1st part, the following instructions should produce a local `${PWD}/jenkins_home` directory with everything configured for the docker-compose to Just Work.

Run the container as with

    docker run \
        -p 8080:8080 -p 50000:50000 \
        -v ${PWD}/jenkins_home:/var/jenkins_home \
        --restart unless-stopped \
        --name jenkins \
        jenkins/jenkins:lts

Watch the logs and get the hash, something like `9d3fba44b9bc4e5e85093ffeb07870c1` and copy to pasteboard.

When the container has started, navigate to `localhost:8080` and enter the hash, then

- install suggested plugins
- create an admin user (you should enter your actual email address if you want to receive emails from Jenkins)

Finally, go to Manage Jenkins / Manage Plugins, and install those plugins:

- Blue Ocean
- Warnings
- GitHub Pipeline for Blue Ocean
- GitHub Authentication
- GitHub Pull Request Coverage Status
- Cobertura
- Green Balls

Installation may take about 5 min.

Finally, from a terminal, if you wish to use Jenkins for a software stack like Python, enter the container and install python2 and python3:

    docker exec -it --user root jenkins_jenkins_1 bash
    apt update && apt install -y python-pip python3-pip

then exit the container.

You may destroy the container, the only important item is the `${PWD}/jenkins_home` directory.


## Running the container locally (no `systemd`)
As a basic working configuration, we can simply start the Jenkins container (pointing to the `${PWD}/jenkins_home` directory) and have it served by a Nginx reverse proxy.

The Nginx configuration file is `./nginx/jenkins.conf`. It is configured to reverse proxy on `http://jenkins.example.com` (create certs separately if you need).

If the address `jenkins.example.com` is not yet declared in the DNS, we can add it manually it `/etc/hosts`.

Start the Nginx and Jenkins containers:

    docker-compose up

then navigate to http://jenkins.example.com and login.

## Running as a service
Running all this as a well-behaved service requires a configuration tool like Chef, Puppet, or Docker Swarm/Nomad, which I consider outside our maturity level for the moment.
