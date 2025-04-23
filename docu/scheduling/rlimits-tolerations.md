basicamente esto es como añadir una talla a un pod para que ocupe cierto espacio en un nodo de kubernetes, basicamente tendremos que añadir un nuevo valor como "resources, con sus respectivos valores tambien."

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: myapp
    labels:
        name: myapp
spec:
    containers:
        - name: myapp
          image: myapp
          ports:
            - containerPort: 8080
          resources:
            requests:
                memory: "4Gi"
                cpu: 2 
```

puedes tambien añadir limites de CPU y memoria a los contenedores al mismo tiempo que pide recursos con el requests, seria tal que asi un ejemplo:

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: myapp
    labels:
        name: myapp
spec:
    containers:
        - name: myapp
          image: myapp
          ports:
            - containerPort: 8080
          resources:
            requests:
                memory: "1Gi"
                cpu: 1
            limits:
                memory: "2Gi"
                cpu: 2
```

de esta forma, los contenedores del pod pillaran 1 gi y 1 cpu pero escalaran hasta llegar como maximo a 2 gi y 2 cpu. si el pod se pasa de memoria, el pod se terminara con el error de OOM (out of memory)

el mejor setup para poder tener un consumo de memoria constante y sin errores es especificar requests pero no limites, asi los pods usaran toda la cpu y la memoria disponibles todo el tiempo y se la iran repartiendo. 

cuando no pones limites con la memoria, no pasa lo mismo que con la cpu, si tienes request pero no limits con la memoria, si el pod 1 pilla toda la memoria y el pod 2 esta pidiendo 1 Gi mas (ejemplo) tendriamos que matar 1 contenedor nosotros para poder liberar esa memoria, no es el mismo caso que con la CPU

con el limit ranges, podemos tener una config defaults de limites y requests, se aplica a nivel de namespace y es un objeto con su propio yaml, un yaml para cpu y otro para memoria: 

```yaml
apiVersion: v1
kind: LimitRange
metadata:
    name: cpu-resource-constraint
spec:
    limits:
        - default: (limite)
            cpu: 500m
          defaultRequest: (request)
            cpu: 500m
          max: (limite)
            cpu: "1"
          min: (request)
            cpu: 100m
          type: Container
```

```yaml
apiVersion: v1
kind: LimitRange
metadata:
    name: cpu-resource-constraint
spec:
    limits:
        - default: 
            memory: 1Gi
          defaultRequest:
            memory: 1Gi
          max: 
            memory: 1Gi
          min:
            memory: 500Mi
          type: Container
```

si se cambia algun parametro, solo afectara a los pods que se creen despues de los cambios

si queremos limitar la cpu y la memoria a nivel de namespace, podemos usar resource quotas

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
    name: my-resource-quota
spec:
    hard:
        requests.cpu: 4
        requests.memory: 4Gi
        limits.cpu: 10
        limits.memory: 10Gi
```

para poder cambiar la memoria en caliente se pueden hacer varias cosas 

si es un deploy simplemente bastaria con el siguiente comando:

```shell
kubectl edit deployment my-deployment
```

si es un pod, tendremos que extraer el YAML y hacer los cambios en base a ese yaml, despues borrar el pod y lanzarlo con los cambios hechos 

```shell
kubectl get pod webapp -o yaml > my-new-pod.yaml

vi my-new-pod.yaml

kubectl delete pod webapp

kubectl create -f my-new-pod.yaml
```

si has creado el pod mediante apply -f, lo podrias cambiar del tiron sin tener que borrar el pod
