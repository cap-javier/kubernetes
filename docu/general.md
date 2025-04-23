```shell
alias kc="kubectl"
```

en el examen de kubernetes, nos van a pedir generar varios ficheros de yaml, lo mas facil quae podemos hacer para crear unos yamls guapos son los siguientes comandos.

lo que haria el siguiente comando seria intentar arrancar un pod con nombre e imagen nginx pero como tiene el flag (dry-run) no lo creara y con el (-o yaml) nos va a sacar el yaml a pantalla y despues nos lo llevamos a un .yaml y ya tenemos una plantilla facil

```shell
kubectl run nginx --image=nginx --dry-run=client -o yaml >> nginx.yaml
```

este mismo comando para hacer un deployment

```shell
kubectl create deployment (nombre deploy) --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
```

comando para ver los pods que estan en un nodo en concreto

```shell
kubectl get pods --all-namespaces --field-selector spec.nodeName=<NOMBRE_DEL_NODO>
```

si necesitamos crear algun tipo de etiqueta a un nodo o a un pod, podemos usar el siguiente comando

```shell
kubectl label node node01 color=blue
```

podemos ver los eventos de los pods (y de cualquier recurso) para logging 

```shell
kubectl get events -o wide
```
