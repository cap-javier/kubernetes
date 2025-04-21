un node afinitty, generalmente es lo mismo que un selector pero se pueden hacer mas configuraciones y cambios en el, su yaml tambien es un poquito mas complicado tambien 

```yaml
ApiVersion:
kind:
metadata:
    name: myapp-pod
spec:
    containers:
        - name: data-processor
          image: data-processor
affinity:
    nodeAffinity:
        requiredDuringShedulingIgnoredDuringExecution:
            nodeSelectorTerms:
                - matchExpression:
                    - key: size 
                      operator: In
                      values:
                      - Large
```

el valor de operator puede ser NotIn. Usando el operador  Exists, podremos ver si el valor size esta indicado dentro del nodo

tambien existe el operador: Exists para poder poner el pod en un lugar en concreto
