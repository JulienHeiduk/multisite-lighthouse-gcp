# multi-sites-phare-gcp
Effectuez des audits Lighthouse sur les URL, et écrivez les résultats quotidiennement dans un tableau BigQuery.

# Etapes

1. Clone repo.
2. Install [Google Cloud SDK](https://cloud.google.com/sdk/).
3. Authenticate with `gcloud auth login`.
4. Create a new GCP project.
5. Enable Cloud Functions API and BigQuery API.
6. Create a new dataset in BigQuery.
7. Run `gcloud config set project <projectId>` in command line.
8. Edit `config.json`, update list of `source` URLs and IDs, edit `projectId` to your GCP project ID, edit `datasetId` to the BigQuery dataset ID.
9. Run `gcloud functions deploy launchLighthouse --trigger-topic launch-lighthouse --memory 2048 --timeout 540 --runtime=nodejs8`.
10. Run `gcloud pubsub topics publish launch-lighthouse --message all` to audit all URLs in source list.
11. Run `gcloud pubsub topics publish launch-lighthouse --message <source.id>` to audit just the URL with the given ID.
12. Verify with Cloud Functions logs and a BigQuery query that the performance data ended up in BQ. Might take some time, especially the first run when the BQ table needs to be created.

# Comment ça marche

Lorsque vous déployez la fonction Cloud sur GCP, elle attend que des messages spécifiques soient poussés dans la file d'attente des sujets Pub/Sub de "launch-lighthouse" (ce sujet est généré automatiquement par la fonction).

Lorsqu'un message correspondant à une URL définie dans `config.json` est enregistré, la fonction lance une instance de phare et effectue l'audit de base sur l'URL.

Cet audit est ensuite analysé dans un schéma BigQuery, et écrit dans une table BigQuery nommée "report" sous l'ensemble de données que vous avez créé.

Le schéma BigQuery ne comprend actuellement que les éléments qui ont un "poids", c'est-à-dire ceux qui ont un impact sur les notes également fournies dans l'audit. 

Vous pouvez également envoyer le message "all" au sujet Pub/Sub, auquel cas la fonction Cloud exécute automatiquement une nouvelle fonction pour chaque URL de la liste, lançant ainsi les processus de phare en parallèle.

# Coût

C'est extrêmement peu coûteux. Vous devriez pouvoir travailler avec le niveau gratuit pendant un certain temps, à condition de ne pas utiliser les fonctions des dizaines de fois par jour. 
