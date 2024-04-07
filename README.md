# Istio testing

## 1. Create cluster

```bash
kind create cluster -n istio --config 0-kind-cluster.yaml
```

## 2. Install ingress controller

```bash
# source: https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

kubectl apply -f 1-ingress-nginx.yaml
```

Wait until is ready to process requests running:

```bash
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```

Test ingress controller:

```bash
kubectl apply -f 2-ingress-test.yaml

#  then open browser and go to http://localhost/foo/hostname or http://localhost/bar/hostname
```

## 3. Install istio

```bash
istioctl install --set profile=demo -y
```

Enable namespace auto-injection:

```bash
kubectl label namespace default istio-injection=enabled
```

> Expose istio-ingressgateway with ingress. This would simulate the real world scenario where the ingress controller is used to expose the services and the istio-ingressgateway is used to route the traffic to the services.

```bash
kubectl apply -f 5-istio-ingress.yaml
```

## 4. Install addons

```bash
kubectl apply -f 6-addons
```

show kiali dashboard:

```bash
istioctl dashboard kiali
```

> same for grafana, jaeger, prometheus, etc.
