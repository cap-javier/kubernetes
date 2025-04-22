este objeto se asegura de que siempre haya minimo una copia del pod en cada nodo.  se podria usar para tener un pod de un log collector en cada nodo o un agente de monitorizacion por ejemplo.

el componente kube-proxy puede ser desplegado como daemonset para que este disponible en cada nodo (porque tiene que estarlo) 

tambien puede usarse para aplicaciones o plugins por asi decirlo de red y de conectividad en el cluster, ya que se asegura 1 replica en cada nodo

la estructura de los daemonsets es similar a la de los replicaset:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
    name: monitoring-daemon
spec:
    selector:
        matchLabels:
            app: monitoring-agent
    template: 
        metadata:
            labels:
                app: monitoring-agent
        spec: 
            containers:
                - name: monitoring-agent
                  image: monitoring-agent
```


