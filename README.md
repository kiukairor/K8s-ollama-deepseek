# On deploying Ollama framework in K8s

This project is a simple attempt to deploy Ollama in a K8s cluster. In addition we are also deploying open-webui to ease how to talk to the chatbot.

So we have:
- **Ollama** platform deployed (ollama-dep.yaml) to run an LLM
  - the ollama pod will pull deepseek-coder:1.3b LLM if it was never pulled and stored onto the volume which consists in a hostpath pvc.
  - this choice was made for resources reasons as deepseek-coder:1.3b is one of the lighter LLM around.
- **Open-webui** is an interface that allows to interact with ollama deployment instead of using a terminal to talk to the AI chat.
- Open-webui shall be accessed by a K8s Gateway API; but we have also commented a service that would expose open-webui as a NodePort (Refer to details in openwebui-svc.yaml)

# Pre-reqs
- a local K8s cluster, kustomized enabled, and with kubectl properly configured.
- we have used 2 PVs of combined space equal to 23 Gi (respectively 20Gi for ollama and 3Gi for open-webui); ensure you have sufficient disk space on your nodes.
- Either a gateway API properly configured; we are using nginx gateway api here. One may modify file 'httproute.yaml' accordingly if using another Gateway API techno.
- Or simply, the capability to access openweb-ui workload via a NodePort.
- if using the Gateway API, we assume your clients are able to resolve to the hostname you would have given in the httproute.

# HOWTO
As mentioned below, most of the env variables are hardcoded at the moment, modify the files accordingly if needed.

Create ollama namespace:
```
kubectl create ns ollama
````

At root folder, run:
```` 
kubectl apply -k .
````
After a few min, either browser to your workload hostname via the gateway:
```` 
https://<http-route-hostname>:<k8s-gateway-api-port>
````

Or if you chose the NodePort method (see openvwebui-svc.yaml for more details):
```` 
http://<worker-IP>:<NodePort>
````


# Note - Next steps.
- Kustomize is not really needed for now as everything is hardcoded.
- Parametrize the config.