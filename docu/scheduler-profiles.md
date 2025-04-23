podemos asignar prioridades a los pods cuando se crean ya que al crearse entrar en un queue de creacion, para asignar alta prioridad podemos añadir esto al yaml de creacion

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: mypod
spec:
    priorityClassName: high-priority
    containers:
        - name: mypod
          image: mypod
            resources:
            requests:
            memory: "1Gi"
            cpu: 10
```

antes de poder asignar una prioridad, tendremos que crear una priority class:

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
    name: high-priority
value: 1000000
globalDefault: false
description: "Esto es una clase de prioridad que solo se debe usar para tal pod"
```

existen unos plugins que controlan los pasos que siguen los pods para poder crearse en los nodos, los podemos controlar creando schedulers en los que cambiemos el funcionamiento de los plugins (desactivando unos, creando propios plugins), es recomendable hacer eso ya que si no los schedulers que nosotros vamos a crear funcionaran como el default scheduler

```yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
    - schedulerName: my-scheduler
      plugins:
      score:
          disabled:
            - name: TaintToleration
            enabled:
            - name: MyCustomPluginA
            - name: MyCustomPluginB

    - schedulerName: my-scheduler-2
      plugins:
        preScore:
            disabled:
            - name: '*'
        score:
            disabled:
            - name: '*'
```

aqui, lo que estoy haciendo es, en el primer scheduler desactivar el tainttoleration y activar 2 plugins que yo he creado, y en el segundo scheduler estoy desabilitando todos los plugins que actuan en las extensiones de preScore y de score
