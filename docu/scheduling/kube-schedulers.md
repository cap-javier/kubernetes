podemos tener nosotros y crear schedulers nuevos, como por ejemplo podriamos tener 3 schedulers mas a partir del que viene por defecto, lo unico que tendriamos que hacer es llamarlo de una forma diferente al normal (default-scheduler). podemos ver el archivo de configuracion aqui: 

```yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
    - schedulerName: my-scheduler
```

para poder lanzar y hacer que funcione el scheduler, lo unico que tendremos que hacer es descargarnos el binario que descargamos de manera normal para el scheduler base y lo que hay que hacer es que en la configuracion apuntamos al yaml que nosotros hemos creado

```shell
wget https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-scheduler

//en el archivo de configuracion

--config=/etc/kubernetes/config/my-scheduler-2-config.yaml
```

si queremos hacer que nuestro scheduler actue como un pod, podemos hacer lo siguiente.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-scheduler
    namespace: kube-system
spec:
    containers:
        - command:
            - kube-scheduler
            - --address=127.0.0.1
            - --kubeconfig=/etc/kubernetes/scheduler.conf
            - --config=/etc/kubernetes/my-scheduler-config.yaml
          image: k8s.grc.io/kube-scheduler-amd64:v1.11.3
          name: kube-scheduler

//tendremos que especificar el archivo yaml donde hemos puesto el nombre del scheduler
```

en caso de que se tengan diferentes master nodes, puedes elegir un lider de scheduler añadiendo el siguiente parametro al yaml de creacion.

```yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
    - schedulerName: my-scheduler
leaderElection:
    leaderElect: true
    resourceNamespace: kube-system
    resourceName: lock-object-my-scheduler
```

para que los pods usen el scheduler que acabamos de crear, lo que tenemos que hacer seria añadir el siguiente valor en el yaml de creacion de POD

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: nginx
spec:
    containers:
        - image: nginx
          name: nginx
    schedulerName: my-scheduler
```
