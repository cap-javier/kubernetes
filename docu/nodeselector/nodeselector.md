un yaml para poner un node selector se vera asi, 

```yaml
apiVersion:
kind: Pod
metadata:
    name: myapp-pod
spec:
    containers:
        - name: data-processor
          image: data-processor
    nodeSelector:
        size: Large
```

como podemos ver, añadimos un tipo de etiqueta al yaml del pod para restringir que se vaya a otros nodos y que se vaya a los que tengan la etiqueta largue en concreto
