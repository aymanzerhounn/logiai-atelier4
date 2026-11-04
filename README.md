# LogiAI - Atelier 4

API FastAPI de prédiction du risque de retard de livraison, sécurisée par clés API et accompagnée de son client web.

## Fonctionnalités

- authentification par l'en-tête `X-API-Key`
- rôles utilisateur et administrateur
- validation Pydantic des sept variables de livraison
- CORS limité aux origines locales autorisées
- interface web accessible sur le port 5500
- tests d'API, de valeurs limites et de sécurité

## Prérequis

- Python 3.10 ou supérieur
- `pip`

## Installation Windows

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m pip install ruff
copy .env.example .env
python -m logiai.dataset
python -m logiai.train
```

Modifiez ensuite `.env` et définissez deux clés distinctes :

```env
LOGIAI_API_KEY=votre-cle-utilisateur
LOGIAI_ADMIN_KEY=votre-cle-administrateur
```

Le fichier `.env` est ignoré par Git et ne doit jamais être versionné.

## Démarrage

API sur `http://localhost:8000` :

```powershell
python -m uvicorn logiai.api.main:app --reload
```

Interface web sur `http://localhost:5500` :

```powershell
python -m http.server 5500 --directory web
```

Saisissez une clé API dans la page. La clé utilisateur appelle `/v1/predict`; la clé administrateur peut aussi appeler `/v1/model-info`.

## Endpoints

| Méthode | Route | Accès |
|---|---|---|
| GET | `/health` | Public |
| POST | `/v1/predict` | Utilisateur ou administrateur |
| GET | `/v1/model-info` | Administrateur |
| GET | `/docs` | Documentation Swagger publique |

Réponses d'accès principales :

- `401` : clé absente ou invalide
- `403` : clé valide sans le rôle administrateur
- `422` : données de livraison invalides

## Qualité et tests

```powershell
ruff check .
ruff format .
python -m pytest
```

## Sécurité CORS

Le middleware autorise uniquement :

- `http://localhost:5500`
- `http://127.0.0.1:5500`

Il autorise les méthodes `GET`, `POST` et `OPTIONS` ainsi que les en-têtes `Content-Type` et `X-API-Key`. Une clé saisie dans le navigateur reste visible dans le réseau et le code du navigateur; elle convient uniquement à une démonstration, pas à la protection d'un secret réel.

## Livraison GitHub

Le travail est développé sur la branche `tp-securite`, soumis par pull request, fusionné dans `main`, puis marqué avec le tag `tp4`.

## Dépôt

- GitHub : https://github.com/aymanzerhounn/logiai-atelier4
- Tag final demandé : `tp4`
