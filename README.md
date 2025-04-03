# chart-k8s
[Helm](https://helm.sh/) est un gestionnaire de packages pour les applications ***Kubernetes***, il gère les packages de ressources Kubernetes via les ***Charts***.<br> 
Les Charts sont essentiellement le format de package de Helm

### Installation de Helm
Il existe plusieurs façons d'installer Helm qui sont soigneusement décrites sur [la page d'installation officielle](https://helm.sh/docs/intro/install/) de Helm. Le moyen le plus rapide d'installer helm sur Linux consiste à utiliser le script shell fournis :
````sh
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
````

### Création d'un chart
Créer un nouveau chart avec un nom donné :
````sh
helm create kkherrazi
````

Le nom du chart fourni kkherrazi sera le nom du répertoire dans lequel le chart est créé et stocké.
Structure de répertoire créée   :

````sh
kkherrazi
├──.helmignore
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
````

Les fichiers YAML de déploiement font partie du répertoire de modèles appelé ***templates*** et il existe des fichiers et des dossiers spécifiques à helm. Examinons chaque fichier et répertoire à l'intérieur d'un chart helm et comprenons son importance.

- ***.helmignore*** : Il est utilisé pour définir tous les fichiers que nous ne voulons pas inclure dans le chart helm. Ce fichier fonctionne de la même manière que le fichier.gitignore au sein de git.

- ***Chart.yaml***: Il contient des informations sur le chart helm comme la version, le nom, la description, etc.

- ***values.yaml***: dans ce fichier, nous définissons les valeurs des modèles YAML. Par exemple, le nom de l'image, le nombre de répliques, les valeurs HPA, etc. Comme nous l'avons expliqué précédemment, seul le values.yamlfichier change dans chaque environnement. De plus, vous pouvez remplacer ces valeurs dynamiquement ou au moment de l'installation du chart à l'aide de la commande ***--values*** ou ***--set***.

- ***charts***: nous pouvons ajouter la structure d'un autre chart dans ce répertoire si nos principaux charts dépendent des autres. Par défaut ce répertoire est vide.

- ***templates***: ce répertoire contient tous les fichiers manifestes Kubernetes qui forment une application. Ces fichiers manifestes peuvent être modélisés pour accéder aux valeurs du fichier ***values.yaml***. Helm crée des modèles par défaut pour les objets Kubernetes tels que ***deployment.yaml***, ***service.yaml***, etc., que nous pouvons utiliser directement, modifier ou remplacer avec nos fichiers.

- ***templates/NOTES.txt*** : il s'agit d'un fichier en texte brut qui est imprimé après le déploiement réussi du chart.

- ***templates/_helpers.tpl***: ce fichier contient plusieurs méthodes et un sous-modèle de chart qui peuvent êtres adaptées et réutilisé selon les besoins.

- ***templates/tests/***: pour définir les tests dans des charts pour valider que le chart fonctionne comme prévu lors de son installation.

### Helm Lint
La commande prends en argument le chemin d'un chart et exécute une batterie de tests pour s'assurer que le chart est bien écrit :
````sh
helm lint ./kkherrazi
````

### Helm template
Exécuter cette commande pour avoir un aperçu du résultat une fois les valeur remplacée par Helm :
````sh
helm template ./kkherrazi
````

### helm install
Exécuter cette commande pour installer le chart dans le cluster Kubernetes :
````sh
mkdir .kube # nous créons un répertoire.kube
kubectl config view --raw > ~/.kube/config # nous exportons la configuration qu'utilisera helm pour se connecter au cluster kubernetes
helm install  kkherrazi-chart ./kkherrazi --values=./kkherrazi/values.yaml # nous installation notre chart kkherrazi en lui donnant un nom kkherrazi-chart et en précisant le fichier à utiliser pour fournir les valeurs qui seront utilisées pour remplacer les variables dans les templates
````

Vérifer les services et les Pods disponibles :
````sh
kubectl get svc,Pod | grep kkherrazi
````

### Afficher la liste des Charts

Voir quels charts sont installés dans quelle version. Cette commande permets d'interroger les versions nommées :
````sh
helm ls --all
````

### Mettre à Jour des CHARTS
Après avoir modifier le fichier kkherrazi/values.yaml, Mettre à jour le déploiement :
````sh
helm upgrade kkherrazi-chart ./kkherrazi --values=./kkherrazi/values.yaml
````

### Retour en arrière du chart
Afficher la liste des charts présents sur notre système :
````sh
helm ls
````
Ensuite rollback à la version précedente :
````sh
helm rollback kkherrazi-chart 1
````

### Helm Package
Ajouter un fichier robots.txt à l'emplacement racine du référentiel , Cela évitera l'exploration de robots sur le référentiel Helm.
````sh
cd kkherrazi
echo -e “User-Agent: *\nDisallow: /” > robots.txt 
````

Créer un fichier .tgz de la chart Helm à l'emplacement racine qui nous permettra d'empaqueter les charts que nous avons créés pour pouvoir les distribuer. 
````sh
helm package .
````
### Désinstallation de Helm
désinstaller une version de notre cluster Kubernetes :
````sh
helm uninstall kkherrazi-chart
````

