cada archivo yaml de los pods tienen una variable que no especificamos nosotros,  si no que se crea automaticamente cuando creamos un objeto en kubernetes, que es "NodeName" 

lo que hace el scheduler es buscar los objetos que no tengan cofigurado este parametro y lo asigna a un nodo automaticamente.     

si no existiera un scheduler en nuestro nodo, el pod se quedaria en estado pending constantemente y lo tendriamos que asignar manualmente nosotros, usando el parametro de "NodeName"

si ya tienes el objeto creado y quieres asignarlo a un nodo despues de su creacion, tendrias que hacer un binding object y hacer una peticion POST al binding del objeto que quieras cambiar:

```yaml
apiVersion: v1
kind: Binding
metadata:
    name: nginx
target:
    ApiVersion: v1
    kind: Node
    name: node02
```

y la peticion seria tal que asi

```shell
curl --header "Content-Type:application/json" --request POST --data (datos en json del yaml que acabamos de crear y la direccion de nuestro pod y su bindin)
```
