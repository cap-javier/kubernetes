# Escalar un ReplicaSet en Kubernetes

Para escalar un ReplicaSet, podemos utilizar diferentes métodos:

```shell
kubectl replace -f replicaset-definition.yaml
kubectl scale --replicas=6 -f replicaset-definition.yaml
kubectl scale --replicas=6 replicaset myapp
```

para crear el ReplicaSet, tendremos que hacer como hacemos normalmente para crear a traves de yaml.

```shell
kubectl create -f replicaset-01.yaml
```

para modificar los cambios de un replicaset podemos usar los siguientes comandos, este primer comando sirve para que cuando modifiques un yaml, puedas aplicar los cambios sin la necesidad de borrar el rs y crear uno nuevo

```shell
kubectl apply -f replicaset.yaml
```

para poder cambiar la imagen con un comando desde la terminal podemos hacer,

```shell
kubectl set image replicaset/new-replica-set <nombre del contenedor>=imagen que quieras
```

si no tenemos el nombre del contenedor podemos hacer lo siguiente.

```shell
kubectl get rs new-replica-set -o=jsonpath='{.spec.template.spec.containers[*].name}'
```
