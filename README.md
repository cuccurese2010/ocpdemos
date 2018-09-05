# ocpdemos
Openshift demo applications

AB deployments are a simple and effective strategy to split traffic between different applications. One common use case is to split the load between the same application using a different template or database and measure the impact with different % of each applications. You can also use it to switch off completely one version of one application by setting its load to 0%.

oc new-app wildfly~https://github.com/cuccurese2010/ocpdemos#master --context-dir=wildfly-basic --name='v1' -l name='v1' -e SELECTOR=v1

oc new-app wildfly~https://github.com/cuccurese2010/ocpdemos#version2 --context-dir=wildfly-basic --name='v2' -l name='v2' -e SELECTOR=v2

Ok now we will expose both services on the router, plus we will create a third route named "ab" which will be used to split the load between the two routes:
$oc expose service v1 --name v1 -l name='v1'
$oc expose service v2 --name v2 -l name='v2'
$oc expose service v1 --name='ab' -l name='ab'

Now let's set the policy for the ab router to use roundrobin strategy to split the load between applications:

oc annotate route/ab haproxy.router.openshift.io/balance=roundrobin

Finally, let's choose the % of traffic between the applications to be equal:

oc set route-backends ab v1=50 v2=50
This is confirmed by your routes list:      

oc get routes


