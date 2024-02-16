## Architecture
![schema_kafka_convergence](https://github.com/abes-esr/bacon-kafka-docker/assets/37241021/82917349-99a6-4284-aed0-11447d6b9a97) 

En jaune, les différents composants de Kafka développés par Confluent :
- Le Broker qui est l'unité de stockage du système (le bus de donné)
- Kafka-connect permettant de faire transiter des données de différents types d'applications/systèmes de base de donnée vers kafka et vice versa
- Le schema-registry s'occupant de la cohérence des données des connecteurs, de leurs validations et de la manière de les organiser
- Le control-center servant à visualiser l'état du cluster et toutes les métadonnées qui le relis : les brokers, nombre de topics présent sur le/les broker/s, les connecteurs, les schemas ...
- kafka-rest-proxy qui est une API REST donnant la possibilité d'accéder au métadonnés du cluster ou mononoeud, ainsi que la posibilité d'intéragir avec celui-ci (création de topics ...)

En vert, les interfaces utilisateurs développées par d'autres groupes :
- schema-registry-ui donne un interface graphique permettant de gérer les schemas.
- connecteur-ui correspondant à l'interface des connecteurs

*1 *2 *3 - Un exemple de connecteur sink utilisé pour récupérer des données d'une table de la base Bacon et de les envoyer sur un topic kafka. (Configuration de celui-ci abordée ci-dessous)

## Kafka connect
Il est possible de lire le contenu d'un topic en utilisant kafka Connect. Voici la marche à suivre :
- installer kafka connect sur le broker kafka
- facultatif : installer une interface utilisateur pour créer des sources de données kafka connect
- créer une source de données kafka-connect sur un topic en particulier (via l'IHM ou un appel web service en post)

Voici un exemple de configuration d'un sink kafka connect connecté à un topic et utilisant le schema registry pour effectuer le mapping topic -> table dans la base de données.
```json
{
  "connector.class": "io.confluent.connect.jdbc.JdbcSinkConnector",
  "connection.password": "",
  "tasks.max": "1",
  "max.retries": "3",
  "value.converter.enhanced.avro.schema.support": "true",
  "value.converter.schema.version": "n° version du schéma à utiliser pour ce topic",
  "value.converter": "io.confluent.connect.avro.AvroConverter",
  "dialect.name": "dialect dépendant de la bdd cible",
  "table.name.format": "schema.table destination",
  "topics": "topic à lire",
  "value.converter.schema.registry.url": "inserer_url_schema_registry",
  "connection.user": "user",
  "auto.create": "false",
  "connection.url": "inserer_url_jdbc_base_de_donnees",
  "pk.fields": "champ_cle_primaire",
  "quote.sql.identifiers": "never"
}
```
