# Deploy Ollama to Kubernetes

## Prerequisites

- Ollama: https://ollama.ai/download
- Kubernetes cluster. This example will use Google Kubernetes Engine.

## Steps

1. Create the Ollama namespace, daemon set, and service

    ```
    kubectl apply -f cpu.yaml
    ```

1. Port forward the Ollama service to connect and use it locally

    ```
    kubectl -n ollama port-forward service/ollama 11434:80
    ```

1. Pull and run `orca-mini:3b`

    ```bash
    OLLAMA_HOST=https://<name>.fly.dev ollama run orca-mini:3b
    ```

## (Optional) External Endpoint

This example configures a ClusterIP service. If you wish to provide a externally available endpoint, you can configure either a LoadBalancer service, Ingress, or Gateway.

### LoadBalancer

Update Service `spec.type` to `LoadBalancer` and configure as needed. Specific configurations differ based on platform.

> Note: you may need to install additional resources to enable external load balancer. Refer to your platform provider for more details.

### Ingress

In order to use Kubernetes Ingress, you must first install an ingress controller. There are many to choose from and the best option will depend on your specific platform. Here's a comprehensive [list](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) of available controllers.

For this example, we'll use [`ingress-nginx`](https://github.com/kubernetes/ingress-nginx). Follow the steps in the [quick start](https://kubernetes.github.io/ingress-nginx/deploy/#quick-start) to install the ingress controller. You can skip this step if you already have an ingress controller.

Now that you have an ingress controller, configure an ingress for Ollama. Replace `ollama.example.com` with your own domain. If you're not using `ingress-nginx`, replace `spec.ingressClassName` with your ingress class.

```
kubectl apply -f ingress.yaml
```

Additional configurations for TLS can be configured in `spec.tls`. See [here](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) for more details.

## (Optional) Hardware Acceleration

Hardware acceleration in Kubernetes requires NVIDIA's [`k8s-device-plugin`](https://github.com/NVIDIA/k8s-device-plugin). Follow the link for more details.

Once configured, create a GPU enabled Ollama deployment.

```
kubectl apply -f gpu.yaml
```
