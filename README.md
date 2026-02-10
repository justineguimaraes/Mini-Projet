# Gametracker

Petit projet ETL pour collecter, charger et rapporter des scores de jeux.

**Description courte**
- Pipeline simple d'extraction, transformation et chargement des données de joueurs et scores puis génération d'un rapport synthétique.

**Prérequis techniques**
 Docker & Docker Compose — le projet est prévu pour s'exécuter dans des conteneurs
 MySQL (fourni via container ou accessible depuis la stack Docker)


**Instructions de lancement**

 Exécution recommandée (Docker Compose) — mode standard :
 
 ```powershell
 cd Mini-projet\gametracker
 docker compose up -d --build
 # lancer le script de rapport dans le conteneur (remplacez <service> si nécessaire)
 docker compose exec app python -m src.report
 # récupérer le rapport généré depuis l'hôte
 docker compose exec app powershell -c "Get-Content .\output\rapport.txt"
 ```
 
 Exécution locale (développement uniquement) :
 
 > Utilisez cette méthode seulement si vous développez ou dépannez localement et que vous avez une base MySQL accessible. La configuration et les chemins sont prévues pour Docker.
 
 ```powershell
 cd Mini-projet\gametracker
 # configurer les variables d'environnement pour pointer vers la base
 $env:DB_HOST='localhost'
 $env:DB_PORT='3306'
 $env:DB_NAME='projet_db'
 $env:DB_USER='root'
 $env:DB_PASSWORD='root'
 
 pip install -r requirements.txt
 python -m src.report
 Get-Content .\output\rapport.txt
 ```

Exécution via Docker Compose :

```powershell
cd Mini-projet\gametracker
docker compose up -d --build
# exécuter le pipeline ou le script de rapport dans le conteneur (remplacez <service> si besoin)
docker compose exec app python -m src.report
docker compose logs --follow
```

**Structure du projet**
- `src/` : code source (extract, transform, load, database, report, main)
- `data/raw/` : CSV d'entrée
- `output/` : sorties (ex. `rapport.txt`)
- `scripts/` : scripts d'aide (init-db, run_pipeline, wait-for-db)
- `requirements.txt` : dépendances Python
- `Dockerfile`, `docker-compose.yml` : configurations Docker

**Problèmes de qualité traités**
- Connexions robustes : `database_connection()` avec retry et gestion de commit/rollback.
- Idempotence du chargement : `ON DUPLICATE KEY UPDATE` dans les requêtes d'insertion.
- Nettoyage / validation : fonctions `extract()` et `transform.py` préparent et vérifient les données avant chargement.
- Gestion d'erreurs : exceptions capturées lors de l'exécution du rapport et rollback sur erreurs.
- Séparation des responsabilités : modules ETL distincts (extract / transform / load) et rapportage dans `src/report.py`.

---

Pour toute aide pour exécuter le rapport dans votre environnement Docker ou pour automatiser l'appel depuis `main.py`, dites-moi comment vous préférez lancer la pipeline et je m'en charge.
