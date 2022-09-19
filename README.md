<h1 align="center"> TP Mini Projet</h1>

<h3 align="center">Worpress/mysql K8s avec édition des différents manifestes</h3>
<h3 align="center">==========================================</h3>

---

#### Création du répertoire de travail

```bash
mkdir -p mini-projet
```

Changement de répertorie

cd mini-projet

Création des différents manifestes

<h4>WORDPRESS</h4>

**Création du template du pv Wordpress**

*Nécessite d'être fait à la main*

```yaml
kind: PersistentVolume
apiVersion: v1
metadata:
 name: pv-wordpress
 labels:
 type: local
spec:
 capacity:
 storage: 10Gi
 accessModes:
 - ReadWriteOnce
 hostPath:
 path: /wordpress
```

**Création du template du pvc Wordpress**

*Nécessite d'être fait à la main:*

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
 name: pvc-wordpress
spec:
 accessModes:
 - ReadWriteOnce
 resources:
 requests:
 storage: 5Gi
```

**Création du template du namespace Wordpress**

```bash
kubectl create namespace namespace-wordpress --dry-run=client -o yaml > 02-namespace-wordpress.yaml
```

**Création du template deployment pour Wordpress**

```bash
kubectl create deployment deployment-wordpress --image=wordpress:6 --dry-run=client --output=yaml -n namespace-wordpress  > deployment-wordpress.yaml
```

**Création du template service pour Wordpress**

```bash
kubectl create service service-wordpress --namespace=wordpress
```

**Création du secret ayant pour key mysql-password pour le namespace "Wordpress"**, après reflexion il aurait mieux valu faire un seul namespace.

```bash
kubectl create secret generic user-wordpress-secret --from-literal=user-wordpress-secret=wordpress -n namespace-wordpress --dry-run=client -o yaml > wordpress-dbuser-secret.yaml
```

**Création du secret ayant pour key mysql-password pour le namespace "mysql"**

```bash
kubectl create secret generic user-wordpress-secret --from-literal=mysql-password=wordpress -n namespace-mysql --dry-run=client -o yaml > mysql-dbuser-secret.yaml
```

** !!! Un problème subsiste avec une erreur des secret clés (a debeuger) !!! **

Lancer le projet

```bash
kubectl apply -f .
```

*Suite à de nombreux problème ou j'ai du passer plus de temps a débeuger K8S ou minikube. (Problème, blocage de la vm, perte de perf, minikube qui ne se lance plus.*

*Ma solution a été de faire dans minikube:*

```bash
minikube delete --all --purge
```

```bash
minikube start  #(les drivrers virtualbox n'étant pas installé et n'ayant pas de temps pour me plonger dans la doc)
```

*Cette action a pour effet de remonter minikube, sans pour autant supprimer les manifestes créé.*

***Cependant le StorageClass longhorn ou encore les objets: pods etc... sont détruit.***
