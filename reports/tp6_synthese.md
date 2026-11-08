# Orchestrateur ou integration continue ? (TP06)

| Critere                           | Prefect (orchestrateur) | GitHub Actions (CI) |
|-----------------------------------|-------------------------|---------------------|
| Ce qui declenche l'execution      | Une date : planification cron `0 6 * * 1` (chaque lundi a 6 h), ou un Quick run lance depuis l'interface, ou `python flow_mlops.py` | Un evenement Git : chaque `git push` (et le bouton "Run workflow", `workflow_dispatch`) |
| Ou s'execute le traitement        | Sur notre poste : le processus `serve` (terminal 3) ou le worker du work pool `pool-tp`, avec le `.venv` et les donnees DVC locales | Sur une machine Linux ephemere de GitHub (`ubuntu-latest`, Python 3.12), recreee a chaque run, sans acces a `dvc-stockage` |
| Qui surveille, et ou              | L'interface Prefect (http://127.0.0.1:4200) : etat, duree et journaux de chaque tache, historique des runs, runs "Late" si aucun processus ne les execute | L'onglet Actions du depot : coche verte ou croix rouge sur chaque commit, journal de chaque etape, annotations ("exit code 1") |
| Ce qui se passe en cas d'echec    | `valider` leve une `ValueError` (F1 0.25 < 0.82) : le flow passe a Failed et `notifier` n'est pas executee ; `rejouer_pipeline` est d'abord reessayee 2 fois | `verifier_qualite.py` renvoie le code 1 : l'etape et le run passent au rouge, le commit est marque en echec ; on corrige par un nouveau commit (`git revert`) |
| Type de tache adapte              | Traitements recurrents de production : reentrainement planifie, chaines de taches avec reessais, execution sur l'infrastructure de l'equipe | Verification rapide et automatique de chaque modification du code (tests, regle de qualite) avant qu'elle n'atteigne la production |
| Ce qui est versionne dans Git     | `flow_mlops.py`, `planifier.py`, `prefect.yaml` (deploiement, work pool, cron, fuseau) ; l'historique des runs reste dans la base du serveur Prefect | `.github/workflows/ci.yml`, `verifier_qualite.py`, `requirements-ci.txt` ; l'historique des runs est conserve par GitHub |

## Conclusion (5 lignes)

1. GitHub Actions valide le code a chaque modification : le commit qui degradait le modele (F1 = 0.25) a ete signale en rouge en moins d'une minute, puis le `git revert` l'a fait repasser au vert (F1 = 0.8423).
2. Prefect execute et surveille le reentrainement recurrent : planification hebdomadaire, Quick run sans ligne de commande, arret automatique sur la regle de qualite (`seuil_f1` = 0.82).
3. L'orchestrateur a besoin d'un processus actif (`serve` ou worker) : sans lui, les runs restent en attente (etat Late).
4. La meme regle de qualite, lue dans `params.yaml`, est appliquee des deux cotes : les deux outils prennent la meme decision.
5. Les deux sont complementaires : la CI protege le code avant la production, l'orchestrateur fait tourner la production ; aucun ne remplace l'autre.
