taints se ponen en los nodos y las tolerations se ponen en los pods, se puede poner un taint a un nodo de la siguiente forma:

```shell
kubectl taint nodes node-name key=value:taint-effect (puede ser cualquier valor, por ejemplo balatro)
```

hay 3 tipos de efectos de toleraciones.

- noSchedule: los pods no se desplazaran a ese nodo en su creacion

- preferNoSchedule: el sistema intentara no desplazar el pod en el nodo tainteado, pero no esta asegurado que no se quede en ese nodo

- NoExecute: los pods nuevos no se desplazaran al nodo nuevo y los pods  que existen en el nodo y no toleren el valor puesto, seran eliminados

podemos ver las taints configuradas en este comando.

```shell
kubectl taint nodes node1 app=blue:NoSchedule
```

las toleraciones se configuran en los pods, en el archivo yaml dentro del parametro de spec como un nuevo parametro:

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: myapp-pod
spec:
    containers:
        - name: nginx-container
          image: nginx
    tolerations:
        - key: "app"
          operator: "Equal"
          value: "blue"
          effect: "NoSchedule"
```

podemos ver, que las tolerations, son una forma diferente de configurar las taints pero en el pod. que seria equivalente con el comando (recordar que se tiene que hacer 2 veces, una de forma de comando en el nodo y otra en el parametro de configuracion de yaml)

si hacemos un describe con un grep -i taints, podremos saber cuales son las taints de los nodos para ver si podemos meter pods ahi o no:

```shell

```
