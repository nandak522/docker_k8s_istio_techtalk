### Build hello-universe docker image
```sh
cd hello-universe
docker build --build-arg PORT=5000 -t demo/hello-universe:v1 .

# for v2
docker build -f Dockerfile-v2 --build-arg PORT=5000 -t demo/hello-universe:v2 .
```

---
### Run hello-universe container natively
```sh
# on linux
docker container run --network host -it demo/hello-universe:v1  # or v2

# on mac host networking is not available, hence ports have to be explicitly
# published.
docker container run -p 5000:5000 -it demo/hello-universe:v1  # or v2
```

---
### Run hello-universe container in a K8s Pod
```sh
cd kube-deploys
kubectl apply -f hello-universe-namespace.yaml
kubectl apply -f hello-universe-svc.yaml
kubectl apply -f hello-universe-deployment.yaml
```
And access the hello-universe service at :
```sh
curl http://localhost:$(kubectl -n hello-universe get svc \
    -o jsonpath='{.items[0].spec.ports[0].nodePort}')/
```

---
### Traffic Shifting from V1 of service to V2
```sh
cd kube-deploys
# kubectl apply -f hello-universe-namespace.yaml
# kubectl apply -f hello-universe-svc.yaml
kubectl apply -f hello-universe-deployment-v2.yaml

# Ensure that both v1 and v2 pods are running
# kubectl -n hello-universe get po --show-labels
kubectl apply -f hello-universe-gateway-traffic-shifting.yaml
```
And access the hello-universe service at :
```sh
curl http://localhost:$(kubectl -n hello-universe get svc \
    -o jsonpath='{.items[0].spec.ports[0].nodePort}')/
```
This should serve `v2` of service roughly 99% of time, leaving the rest to `v1`.