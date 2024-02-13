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
