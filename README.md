# reverse-image-search

# Deploy on kubernetes

kubectl apply -faquiladb-statefulset.yaml

# Setup pod horizontal scaling

kubectl apply -f aquiladb-horizontalpodautoscaler.yaml

# Enable istio sidecar injection

kubectl label namespace default istio-injection=enabled

# Autio inject sidecar for istio

kubectl apply -f aquiladb.yaml

# Manually inject sidecar for istio

kubectl apply -f <(istioctl kube-inject -f aquiladb.yaml)

# Set ingres IP and ports

export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')

export SECURE_INGRESS_PORT=\$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')

# Create istio gateway

kubectl apply -f aquiladb-gateway.yaml

# Configure routes for traffic with virtual service

kubectl apply -f aquiladb-virtualservice.yaml