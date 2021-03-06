---
id: using-packages
title: A partir des paquets
---

## Étapes pré-installation

### Désactiver SELinux

SELinux doit être désactivé. Pour se faire, vous devez éditer le fichier
**/etc/selinux/config** et remplacer **enforcing** par **disabled**, ou en
exécutant la commande suivante :

```shell
sed -i s/^SELINUX=.*$/SELINUX=disabled/ /etc/selinux/config
```

> Redémarrez votre système d'exploitation pour prendre en compte le changement.

Après le redémarrage, une vérification rapide permet de confirmer le statut de
SELinux :

```shell
$ getenforce
Disabled
```

### Configurer ou désactiver le pare-feu

Paramétrer le pare-feu système ou désactiver ce dernier. Pour désactiver ce
dernier exécuter les commandes suivantes :

```shell
systemctl stop firewalld
systemctl disable firewalld
```

### Installer les dépôts

#### Dépôt Redhat Software Collections

Pour installer Centreon, vous devrez configurer le référentiel officiel des collections
de logiciels pris en charge par Redhat.

Installez le référentiel de collections de logiciels à l'aide de cette commande :

```shell
yum install -y centos-release-scl
```

#### Dépôt Centreon

Afin d’installer les logiciels Centreon à partir des dépôts, vous devez au
préalable installer le fichier lié au dépôt.

Exécutez la commande suivante à partir d’un utilisateur possédant les droits
suffisants :

```shell
yum install -y http://yum.centreon.com/standard/20.10/el7/stable/noarch/RPMS/centreon-release-20.10-2.el7.centos.noarch.rpm
```

## Installation

Pour installer le moteur de supervision, exécutez la commande :

```shell
yum install -y centreon-poller-centreon-engine
```

Pour activer le démarrage automatique des services de supervision au démarrage
du serveur, exécuter la commande suivant :

```shell
systemctl enable centengine centreontrapd snmptrapd
```

Les services de supervision passive peuvent être démarrés :

```shell
systemctl start centreontrapd snmptrapd
```

> Le service de supervision active sera démarré suite à la génération de sa
> configuration.

## Enregistrer le serveur

Pour l'enregistrer sur le serveur Centreon Central ou un serveur distant, exécutez la commande suivante :

``` shell
/opt/rh/rh-php72/root/bin/php /usr/share/centreon/bin/registerServerTopology.php -u <API_ACCOUNT> \
-t Poller -h <IP_TARGET_NODE> -n <POLLER_NAME>
```

Exemple:

``` shell
/opt/rh/rh-php72/root/bin/php /usr/share/centreon/bin/registerServerTopology.php -u admin -t Poller -h 192.168.0.1 -n poller-1
```

> Remplacer **<IP_TARGET_NODE>** par l'adresse IP du serveur Centreon Central ou du Remote Server vue par votre
> collecteur.

> Le compte **<API_ACCOUNT>** doit avoir accès à l'API de configuration. Vous pouvez utiliser le compte **admin**.

> Vous pouvez changer le port et la méthode HTTP, le format de l'option **-h** est le suivant :
> HTTPS://<IP_TARGET_NODE>:PORT

Suivre ensuite les instructions

1. Saisir le mot de passe :

``` shell
192.168.0.1: please enter your password
```

2. Définir les accès au proxy pour atteindre le serveur Centreon Central ou le Remote Server :

``` shell
Are you using a proxy ? (y/n)n
```

Si vous utilisez un proxy, veuillez définir les informations d'identification :

``` shell
Are you using a proxy ? (y/n)y

proxy host: myproxy.example.com

proxy port: 3128

proxy username (press enter if no username/password are required): myuser

please enter the proxy password:
```

3. Sélectionner l'adresse IP :

```shell
Found IP on CURRENT NODE:
   [1]: 192.168.0.2
Which IP do you want to use as CURRENT NODE IP ?1
```

4. Valider les informations:

``` shell
Summary of the informations that will be send:

Api Connection:
username: admin
password: ******
target server: 192.168.0.1

Pending Registration Server:
name: poller-1
type: poller
address: 192.168.0.2

Do you want to register this server with those informations ? (y/n)y
```

Vous recevrez la validation du serveur Centreon central ou du serveur Remote Server :

``` shell
2020-10-16T17:19:37+02:00 [INFO]: The CURRENT NODE 'poller': 'poller-1@192.168.0.2' linked to TARGET NODE: '192.168.0.1' has been added
```

### Principaux messages d'erreur

``` shell
2020-10-20T10:23:15+02:00 [ERROR]: Invalid credentials
```

> Vos informations d'identification sont incorrectes pour le compte **<API_ACCOUNT>**.

``` shell
2020-10-20T10:24:59+02:00 [ERROR]: Access Denied.
```

> L'utilisateur **<API_ACCOUNT>** n'a pas accès à l'API de configuration.

``` shell
Failed connect to 10.30.3.11:444; Connection refused
```

> Impossible d'accéder à l'API. Contrôler les valeurs **<IP_TARGET_NODE>**, méthode et port.

``` shell
2020-10-20T10:39:30+02:00 [ERROR]: Can’t connect to the API using: https://192.169.0.1:443/centreon/api/latest/login
```

> L'URL d'accès n'est pas complète ou invalide. Utilisez l'option **-root** pour définir le chemin de l'URL de l'API.
> Par exemple : **--root monitoring**.

``` shell
2020-10-20T10:42:23+02:00 [ERROR]: No route found for “POST /centreon/api/latest/platform/topology”
```

> La version Centreon du serveur distant est invalide. Elle doit être supérieur ou égale à 20.10.

## Ajouter le Poller à la configuration

Rendez-vous au chapitre [Ajouter un Poller à la configuration](../../monitoring/monitoring-servers/add-a-poller-to-configuration.html).
