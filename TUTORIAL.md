# Pods:
Application containers are deployed as Pods. A Pod is a group of containers that all share a unique IP address and shared storage allowing them to act as a logical host. This group of containers should only include tightly coupled dependencies as they're managed and scaled as a single unit.

# Get logs from a specific Pod
$ oc get pods
NAME                      READY     STATUS    RESTARTS   AGE
docker-registry-1-4c8nm   1/1       Running   0          32m
router-1-ha1s5            1/1       Running   0          32m
ws-app1-1-9ncpk           1/1       Running   0          13m
$ oc logs ws-app1-9ncpk

# Services:
Services are an abstraction that allows Pods with the same name selector to receive traffic. This separation allows the underlying pods to scale up and down without upstream applications needing to be reconfigured. (Services are basically internal load balancer, you specify which ports you open).

# Routes
To simplify access to services, OpenShift is configured with a Router that maps URLs to exposed services (so routes are like external load balancers). If you want your _Services_, and, by extension, your _Pods_, to be accessible to the outside world, you need to create a _Route_.
To create a route to a service, click "Create route" next to the service name.

## How to quickly expose a service (in the command line):
$ oc get all -o name
buildconfig/blog
build/blog-1
imagestream/blog
deploymentconfig/blog
replicationcontroller/blog-1
service/blog
pod/blog-1-build
pod/blog-1-qw13c
$ oc expose service/blog
route "blog" exposed
# Now check if it was correctly exposed and get the URL
$ oc get route/blog
NAME      HOST/PORT                                                        PATH      SERVICES   PORT       TERMINATION
blog  blog-myproject.2886795328-80-ollie01.environments.katacoda.comblog
8080-tcp

# SSH to a running Pod:
$ oc rsh POD_NAME

# Copy file from local machine to the Pod:
$ oc rsync

# Port forwarding
If you don't want to expose a service permanently (with a route), or it's impossible to use the route (because routes can only use the TCP protocol), you can use port forwarding. It will create a temporary tunel between your local machine and the Pod:
$ oc port-forward <pod-name> <local-port>:<remote-port>
or (if you don't know what ports are available)
$ oc port-forward <pod-name> :<remote-port>

# Debugging a POD
If the pod is not starting and you want to debug it, but can use the _oc debug command_ - it will start another pod and open the shell terminal where you can see what is wrong and after you quit, it will destroy the POD:
$ oc debug [POD-ID | DEPLOYMENT-CONFIG]

If the Pod is running but has some errors, you can see the errors in the web console, but also you can run:
$ oc log cds-proxy-1125414609-sc2nb -f
to get the logs in the console (with the "-f" option, the command works like "tail" not "cat").

Otherwise, if the Pod can't start, if might go into a "CrashLoopBackOff" - it means there is some problem with creating the Pod. If you do oc log, it won't print any usefull information, because the Pod is probably dead, but you can do:
$ oc log --previous PODNAME
which will return the logs from the previous run

You can ssh to a running Pod with:
$ oc rsh POD-ID
or just run a command in a running Pod:
$ oc exec POD-ID echo 'hello world!'


$ oc types # Returns a cheatsheet of OpenShift with explanation of how it works
$ oc explain RESOURCE# Gives details about a specific resource (like `oc types` but you can specify the type of resource here)
$ oc describe TYPE-OF-RESOURCE# Gives a detailed information about the resource (e.g. a deployment config)