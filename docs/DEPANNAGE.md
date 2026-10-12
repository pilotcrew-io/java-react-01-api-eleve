# Dépannage — jr-01-api

Relever la première erreur, la commande, la version et le contexte. Une correction conserve le comportement demandé et ajoute ou rejoue une preuve ; retirer une assertion ne répare pas le problème.

| Symptôme | Cause plausible | Diagnostic discriminant | Résolution attendue |
|---|---|---|---|
| Le projet compile mais la validation laisse passer zéro | Le DTO ne déclenche pas la validation ou la dépendance manque | Envoyer la requête par MockMvc et inspecter le contrôleur | Ajouter le starter Validation, déclencher @Valid et conserver un test de quantité 0. |
| Une quantité 1.5 devient 1 | La désérialisation autorise une conversion décimale vers entier | Comparer valeur JSON et valeur observée dans le DTO | Désactiver cette coercition ou valider explicitement le type ; rejouer le cas décimal. |
| Le montant du navigateur apparaît dans la commande | Le DTO ou le service accepte un prix client | Envoyer un amountMinor différent de 2500 × quantité | Refuser les champs inconnus et calculer depuis le catalogue. |
| 400 contient du HTML ou une exception Java | Le traitement d’erreur ne couvre pas le cas déclenché | Relever Content-Type et la première erreur côté serveur | Normaliser ce cas avec le même contrat ProblemDetail. |
| Une requête GET sur une référence échoue après relance | La mémoire a été recréée | Créer puis consulter une nouvelle commande sans redémarrer | Documenter la volatilité ; ne pas inventer une persistance. |
| java -jar ne démarre pas | JAR non repackagé ou mauvais JDK | Comparer java -version et le nom réellement produit par Maven | Configurer le plugin Spring Boot et reconstruire clean verify. |
| Le test ne détecte pas la multiplication supprimée | Il vérifie seulement statut ou présence du champ | Lire l’assertion du montant pour quantité 2 | Comparer la valeur à 5000 et observer la régression rouge puis verte. |

Si un port est occupé, identifier le processus avant de l’arrêter. Ne jamais arrêter globalement toutes les JVM ou tous les processus Node d’un poste partagé. Si une commande dépend d’un fichier demandé dans l’atelier, le construire d’abord : les dépôts élèves sont des supports, pas des solutions prêtes à lancer.
