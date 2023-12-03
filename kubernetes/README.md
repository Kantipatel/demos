### Simple Pod to run a container inside a Kubernetes cluster

Create the Pod against a running cluster and verify its running:
```
apiVersion: v1
kind: Pod
metadata:
  name: shell-demo
spec:
  volumes:
  - name: shared-data
    emptyDir: {}
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
  hostNetwork: true
  dnsPolicy: Default
```

```
kubectl apply -f shell-demo.yaml
kubectl get pod shell-demo

```
Jump inside the container using this command:
```
kubectl exec --stdin --tty shell-demo -- /bin/bash
```
Once inside the running contaner, enter a few commnds to verify:

```
# You can run these example commands inside the container
# You can run these example commands inside the container
ls /
cat /proc/mounts
cat /proc/1/maps
apt-get update
apt-get install -y tcpdump
tcpdump
apt-get install -y lsof
lsof
apt-get install -y procps
ps aux
ps aux | grep nginx
```

The Pod has an emptyDir volume, and the container mounts the volume at /usr/share/nginx/html.

To verify nginx can serve web file 'index.html', use the following command inside the container shell:
```
# Run this inside the container
echo 'Hello shell demo' > /usr/share/nginx/html/index.html
```
In your shell, send a GET request to the nginx server:
```
# Run this in the shell inside your container
# curl command will call localhost on default port 80 of nginx and access the index.html page
apt-get update
apt-get install curl
curl http://localhost/
```
Response should be:
```
Hello shell demo
```
```
exit # To quit the shell in the container
```
Back to you local machine run a few commands in a normal terminal using kubectl:
```
kubectl exec shell-demo -- ps aux
kubectl exec shell-demo -- ls /
kubectl exec shell-demo -- cat /proc/1/mounts
```
The above commnds will execute the actions inside the container and show results.