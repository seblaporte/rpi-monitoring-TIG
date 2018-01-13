# Monitoring sur Raspberry Pi avec la stack Telegraf InfluxDB Grafana

> Ce projet contient les fichiers docker-compose et de configuration pour la mise en place d'une supervision d'un Raspberry Pi fonctionnant avec Docker

## Pré-requis

- Docker installé sur le Raspberry Pi (OS Hypriot par exemple)
- Fonctionne par défaut avec un reverse-proxy Traefik pour que l'interface Grafana soit accessible depuis Internet

## Présentation de la stack technique

- __InfluxDB__ : InfluxDB est système de gestion de base de données temporelle. Ce type de base est tout à fait adaptée pour le stockage de mesures.
- __Grafana__ : Propose une interface web permettant d'interpréter les données stockées dans InfluxDB. Cet outil permet également de mettre en place de l'alerting.
- __Telegraf__ : Agent permettant de récupérer des mesures au moyen de plugins. Il est ici utilisé pour remonter les metrics système et du démon Docker. Il est également utilisé pour vérifier l'état des sites en mesurant le temps de réponse.

## Contenu du projet

- __telegraf__ : Contient un exemple de configuration pour _Telegraf_ (Au minimum, le mot de passe InfluxDB doit être configuré)
- __docker-compose.yml__ : Permet le déploiement de la stack complète (InfluxDB, Grafana et Telegraf)
- __.env.sample__ : Configuration du projet (mots de passe, utilisateur, ...)

> __Remarque__ : La configuration s'effectue en renommant le fichier `.env.sample` en `.env` et en valorisant chaque paramètre.

## Mise en place

> A effectuer uniquement lors de la première installation du serveur

Lors de l'installation du serveur (InfluxDB + Grafana) il est nécessaire de modifier la durée de rétention qui est créée par défaut. En effet, de base, les données ont une durée de rétention illimitée. Il est possible de modifier celle-ci de la manière suivante :

```bash
# On entre dans le conteneur InfluxDB
docker exec -it monitoring_influxdb_1 bash

# On démarre un client InfluxDB en administrateur
influx -username admin -password <password>

# On sélectionne la base telegraf
use telegraf

# On modifie la durée de rétention (ici à 4 semaines)
ALTER RETENTION POLICY "autogen" ON "telegraf" DURATION 4w REPLICATION 1
```