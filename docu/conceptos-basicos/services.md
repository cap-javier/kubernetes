### NodePort

es un servicio que permite habilitar la conectividad desde un pod hacia fuera de internet

en su configuracion cuenta con una serie de 3 puertos. (vista desde el servicio)

- targetport seria el propio puerto del pod por el que queremos que salga el servicio

- port es el puerto del servicio

- nodeport es el puerto del nodo donde accederemos desde internet (desde 30.000 a 32mil y algo)

yaml de ejemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
  selector:
    app: myapp
```

si no usamos la variable port, se usara el mismo que target port y si no usamos nodeport usara un puerto libre automaticamente

### ClusterIP

es un servicio que habilita la conexion de varios servicios dentro del propio cluster

### LoadBalancer

como el propio nombre incida, es un servicio que nos da un balanceador de carga entre nuestros componentes (compatible solo con proveedores de cloud?)
