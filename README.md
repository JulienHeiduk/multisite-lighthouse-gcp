# multi-sites-phare-gcp
Effectuez des audits Lighthouse sur les URL, et écrivez les résultats quotidiennement dans un tableau BigQuery.

# Comment ça marche
Lorsque vous déployez la fonction Cloud sur GCP, elle attend que des messages spécifiques soient poussés dans la file d'attente des sujets Pub/Sub de "launch-lighthouse" (ce sujet est généré automatiquement par la fonction).

Lorsqu'un message correspondant à une URL définie dans `config.json` est enregistré, la fonction lance une instance de phare et effectue l'audit de base sur l'URL.

Cet audit est ensuite analysé dans un schéma BigQuery, et écrit dans une table BigQuery nommée "report" sous l'ensemble de données que vous avez créé.

Le schéma BigQuery ne comprend actuellement que les éléments qui ont un "poids", c'est-à-dire ceux qui ont un impact sur les notes également fournies dans l'audit. 

Vous pouvez également envoyer le message "all" au sujet Pub/Sub, auquel cas la fonction Cloud exécute automatiquement une nouvelle fonction pour chaque URL de la liste, lançant ainsi les processus de phare en parallèle.

# Documentation
Fournit par LOM
