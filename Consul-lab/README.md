To start the enviornment enter "vagrant up".
The enviornment sets up 2 virtual machines with Consul running.
The second machine has an apache server set up which can be accessed on port 8080.

To complete the enviornment the next commands need to be entered 
manually because multiple terminal tabs need to be open and I couldn't 
find a way to do this via scripting:

1. vagrant ssh consul-server
2. consul agent -server -bootstrap-expect=1 -bind=172.20.20.10 -data-dir=/tmp/consul -config-dir=/etc/consul.d

Now the consul server is running therefore to run more commands open a new terminal 
tab and cd to the directory with the vagrant file. Enter the next commands:

3. vagrant ssh apache-server-and-consul-agent
4. consul agent -data-dir=/tmp/consul -bind=172.20.20.11 -config-dir=/etc/consul.d

Now the consul client is running therefore open another terminal tab and cd to the
same directory. Enter the next commands:

5. vagrant ssh consul-server
6. consul join 172.20.20.11

Now both consul agents are registered to the same datacenter and running "consul members"
will display them. 

To register a service on the Apache vm:

7. exit

Exit the consul-server vm

8. vagrant ssh apache-server-and-consul-agent
9. consul services register -name=service1

To create a health check:

10. mkdir ./consul.d
11. echo '{ "service": { "name": "web", "port": 80}}' > ./consul.d/web.json

To run the health check:

12. consul agent -dev -enable-script-checks -config-dir=./consul.d
