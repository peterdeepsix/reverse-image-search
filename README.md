# reverse-image-search
downloads the latest aquiladb image and deploys it on kubernetes with persistent storage
 

# configure persistant storage
create a SSD persistent disk in GCE named aquiladb-disk

# Enable istio sidecar injection

kubectl label namespace default istio-injection=enabled

# Auto inject sidecar for istio

kubectl apply -f aquiladb-statefulset.yaml

# Set ingres IP and ports

export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')

export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')

# Create istio gateway

kubectl apply -f aquiladb-gateway.yaml

# Configure routes for traffic with virtual service

kubectl apply -f aquiladb-virtualservice.yaml

# Setup pod horizontal scaling ??? NOT SURE HOW TO INTEGRATE

kubectl apply -f aquiladb-horizontalpodautoscaler.yaml