al hacer un comando con kubectl pasa por una autenticacion, en el que pilla los certificados que estan instalados en el kubectl, despues pasa por una autorizacion en el que sigue un yaml donde hay configurado roles donde se especifica que se puede hacer y que no. 

aqui podemos ver un yaml que permite crear y ver pods usando RBAC

```yaml
apiVersion: rbac.autorization.k8s.io/v1
kind: Role
metadata:
    name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: [``create``, ``list``]
```

podemos añadir el valor "resourceName" para que solo se puedan crear pods con un nombre en concreto por ejemplo.

tambien puedes hacer que revise un pod que se haya creado y que no permita que use la ultima version de la imagen, que siempre tenga que tener una etiqueta y que solo puedas modificar algunos parametros de su configuracion

los admission controllers basicamente es añadir una medida de seguridad mas.  viene despues de la autorizacion y funciona en forma de plugins (por ejemplo si creo un pod y especifico un namespace que no existe este admission controller tiene un plugin que revisa eso y me saltara un error).  tiene un plugin para poder levantar namespaces automaticamente si no 

para ver los admission controlers que estar habilitados podemos tirar este comando

```shell
kube-apiserver -h | grep enable-admission-plugins
```

si quieres verlo en un setup que usa kubeadm puedes usar

```shell
kubectl exec kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep enable-admission-plugins
```

para añadir nuevos plugins, si estas usando kubeadm tendras que actualizar el archivo de configuracion en el que se encuentran (el flag enable-admission-plugins) y si esta en un pod sera lo mismo pero cambiandolo en el  yaml en vez de en la configuracion

/etc/kubernetes/manifests/kube-apiserver.yaml  aqui se encuentran los parametros donde podemos habilitar/desactivar admission controllers

hay un plugin que esta deprecado y que si queremos hacer cambios en los namespaces deberiamos de estar usando NamespaceLifecycle

### validando y mutando admission controlers

un validating admission controller es el que valida si hay un recurso creado o no, como el que revisa que el namespace esta creado 

un mutating admission controller es el que modifica las cosas en la creacion, como puede ser el que añade un storage class por default. 

- validating ac: son los que pueden validar request

- mutating ac: son los que pueden modificar las request y pueden permitirlas o rechazarlas

existen webhooks tanto para validate como para mutate, los cuales conectamos con nuestro servidor (dentro o fuera de k8s) en el  que esta nuestro admission controller configurado por nosotros mismos.

cuando la request llega a estos webhooks, estos envian un json con todos los datos de la request al server.  despues este server devuelve el objeto AdmissionReview diciendo si se permite o deniega

para tener nuestro propio server de admission, tendremos que desplegar primer el servidor y despues configurar los webhooks

- el servidor webhook puede estar escrito en cualquier lenguaje y generalmente suele ser una API, en la documentacion de kubernetes hay un ejemplo escrito en GO ;)     
- lo unico que tiene que hacer es aceptar las mutaciones y validaciones y responderle con un json al webhook
- lo podemos desplegar como un deployment en el propio cluster de kubernetes, necesita tambien la creacion de un servicio para que puedan acceder a el

para que el servidor y el admission controller puedan hablar necesitamos configurar los admission webhooks, que los hacemos mediante el siguiente objeto yaml.

```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
    name: "pod-policy.example.com"
webhooks:
- name: "pod-policy.example.com"
  clientConfig:
        service:
            namespace: "webhook-namespace"
            name: "webhook-service"
        caBundle: "XXXXX"
  rules:
    - apiGroups: [""]
      apiVersions: ["v1"]
      operations: ["CREATE"]
      resources: ["pods"]
      scope: "Namespaced"
```
