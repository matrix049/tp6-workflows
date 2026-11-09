# Orchestrateur ou integration continue ? (TP06)

| Critere                           | Prefect (orchestrateur) | GitHub Actions (CI) |
|-----------------------------------|-------------------------|---------------------|
| Ce qui declenche l'execution      | Planification (lundi 6 h) ou Quick run | Chaque git push |
| Ou s'execute le traitement        | Sur notre poste (serve ou worker) | Machine Linux de GitHub |
| Qui surveille, et ou              | L'equipe, dans l'interface Prefect | L'equipe, dans l'onglet Actions |
| Ce qui se passe en cas d'echec    | Run Failed, notifier ne s'execute pas | Run rouge, commit marque en echec |
| Type de tache adapte              | Traitements recurrents (reentrainement) | Verification du code a chaque modification |
| Ce qui est versionne dans Git     | flow_mlops.py, planifier.py, prefect.yaml | ci.yml, verifier_qualite.py |

## Conclusion (5 lignes)

1. La CI verifie le code a chaque push : le modele degrade (F1 = 0.25) a ete bloque.
2. Prefect lance et surveille le reentrainement chaque semaine.
3. Sans serve ou worker actif, les runs restent en attente.
4. La meme regle (seuil_f1 = 0.82) est appliquee des deux cotes.
5. Les deux outils sont complementaires : aucun ne remplace l'autre.
