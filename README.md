<div align="center">

# 🎯 Système de Gestion Automatisée des Réclamations Clients

### Pipeline intelligent de traitement, classification et résolution des réclamations par email

[![n8n](https://img.shields.io/badge/n8n-Workflow-EA4B71?style=for-the-badge\&logo=n8n\&logoColor=white)](https://n8n.io/)
[![Google Gemini](https://img.shields.io/badge/Google-Gemini_2.5_Flash-4285F4?style=for-the-badge\&logo=google\&logoColor=white)](https://ai.google.dev/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-4169E1?style=for-the-badge\&logo=postgresql\&logoColor=white)](https://www.postgresql.org/)
[![pgvector](https://img.shields.io/badge/pgvector-Vector_Search-4169E1?style=for-the-badge)](https://github.com/pgvector/pgvector)
[![Notion](https://img.shields.io/badge/Notion-Ticketing-000000?style=for-the-badge\&logo=notion\&logoColor=white)](https://www.notion.so/)
[![Telegram](https://img.shields.io/badge/Telegram-Alerts-26A5E4?style=for-the-badge\&logo=telegram\&logoColor=white)](https://telegram.org/)

**Automatiser le cycle complet d'une réclamation : de la réception de l'email jusqu'à la réponse ou l'escalade vers un agent.**

</div>

---

## 📌 Présentation

Ce projet met en œuvre un système automatisé de **gestion intelligente des réclamations clients** à l'aide de **n8n**, d'un modèle de langage et d'une base de connaissances vectorielle.

Le système récupère automatiquement les réclamations reçues par email, identifie le client, analyse le contenu du message, détermine la catégorie du problème, recherche une solution similaire dans une base de connaissances et décide ensuite du mode de traitement approprié.

Selon le niveau de similarité obtenu, la réclamation peut être :

* 🤖 traitée automatiquement ;
* 👤 soumise à validation humaine ;
* 🚨 escaladée vers un responsable.

Le workflow assure également la traçabilité des réclamations et permet de notifier les responsables via Telegram.

---

## 🎯 Objectifs

Le projet a été conçu autour de plusieurs objectifs :

* automatiser la réception et le traitement des emails ;
* réduire les tâches répétitives effectuées par les agents ;
* classifier automatiquement les réclamations ;
* exploiter une base de connaissances existante ;
* utiliser la recherche vectorielle pour identifier les problèmes similaires ;
* générer des réponses personnalisées ;
* conserver une trace des réclamations traitées ;
* distinguer automatiquement les traitements sûrs des cas nécessitant une intervention humaine ;
* notifier les responsables lorsqu'une intervention est nécessaire.

---

# ✨ Fonctionnalités

## 📥 1. Réception automatique des réclamations

Le workflow surveille une boîte email via **IMAP**.

Lorsqu'une nouvelle réclamation est reçue, le système récupère notamment :

* l'adresse email du client ;
* le nom et le prénom lorsqu'ils sont disponibles ;
* le sujet ;
* le contenu du message ;
* les informations nécessaires au traitement.

Le contenu est ensuite nettoyé et normalisé afin de faciliter les étapes d'analyse.

---

## 🏷️ 2. Classification intelligente

Le système utilise **Google Gemini 2.5 Flash** pour analyser le contenu de la réclamation et lui attribuer une catégorie.

Les catégories actuellement définies comprennent :

| Domaine      | Catégories                                                  |
| ------------ | ----------------------------------------------------------- |
| 📦 Livraison | `LIVRAISON-NON-RECUE`, `LIVRAISON-RETARD`, `ERREUR-ADRESSE` |
| 🎁 Produit   | `PRODUIT-ENDOMMAGE`, `PRODUIT-NON-CONFORME`                 |
| 💳 Paiement  | `REMBOURSEMENT-RETARD`, `PAIEMENT-REFUSE`                   |
| 👤 Compte    | `COMPTE-BLOQUE`                                             |
| 🛒 Commande  | `ANNULATION-COMMANDE`                                       |
| ☎️ Support   | `SERVICE-CLIENT-INJOIGNABLE`                                |

Cette classification permet ensuite d'orienter la recherche vers les solutions correspondant au problème détecté.

---

# 🧠 3. Recherche sémantique avec embeddings

L'un des composants centraux du projet est la **recherche sémantique**.

Les descriptions des problèmes et les solutions de la base de connaissances sont transformées en vecteurs à l'aide d'un modèle d'embedding.

Ces vecteurs sont stockés dans PostgreSQL grâce à l'extension **pgvector**.

La recherche repose ensuite sur une mesure de distance cosinus afin d'identifier la solution la plus proche sémantiquement de la réclamation.

### Processus

```text
Réclamation
     │
     ▼
Nettoyage du texte
     │
     ▼
Génération de l'embedding
     │
     ▼
Recherche dans pgvector
     │
     ▼
Solution la plus similaire
     │
     ▼
Score de similarité
```

---

## 🎚️ 4. Mécanisme de décision

Le score de similarité détermine le niveau d'automatisation.

|         Score | Mode                | Traitement                           |
| ------------: | ------------------- | ------------------------------------ |
|      `≥ 0.85` | 🟢 AUTOMATIQUE      | Génération et envoi de la réponse    |
| `0.65 – 0.85` | 🟡 SEMI-AUTOMATIQUE | Proposition soumise à validation     |
|      `< 0.65` | 🔴 MANUEL           | Création d'un ticket et notification |

Cette approche permet d'éviter de traiter automatiquement des demandes lorsque la correspondance avec la base de connaissances est insuffisante.

---

# ✉️ 5. Génération de réponses

Lorsqu'une solution pertinente est identifiée, Gemini génère une réponse adaptée au contexte de la réclamation.

La réponse peut intégrer des informations dynamiques telles que :

```text
{{client_prenom}}
{{numero_commande}}
```

Le système vise à produire une réponse :

* personnalisée ;
* contextuelle ;
* professionnelle ;
* empathique ;
* directement exploitable par email.

Les réponses sont générées au format **HTML**.

---

# 📋 6. Gestion des tickets

Lorsque le traitement automatique n'est pas suffisamment fiable, le système crée automatiquement un ticket dans **Notion**.

Le ticket permet notamment de conserver :

* la réclamation ;
* le client concerné ;
* la catégorie ;
* le niveau de traitement ;
* la date de création ;
* la date d'échéance ;
* les informations nécessaires à l'intervention de l'agent.

---

# 🚨 7. Notifications Telegram

Les situations nécessitant une intervention peuvent déclencher une notification via un **bot Telegram**.

Cela permet notamment d'alerter rapidement un responsable lorsqu'une réclamation :

* ne correspond pas suffisamment à la base de connaissances ;
* nécessite une intervention humaine ;
* doit être traitée manuellement.

---

# 🏗️ Architecture

## Vue globale

```text
                         ┌─────────────────────┐
                         │    Client / Email   │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │    IMAP Trigger     │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Extraction Client   │
                         │ + Nettoyage Texte   │
                         └──────────┬──────────┘
                                    │
                    ┌───────────────┴───────────────┐
                    │                               │
                    ▼                               ▼
          ┌─────────────────┐             ┌──────────────────┐
          │ Classification  │             │    Embedding     │
          │ Gemini 2.5 Flash│             │   768 dimensions │
          └────────┬────────┘             └────────┬─────────┘
                   │                               │
                   │                               ▼
                   │                     ┌──────────────────┐
                   │                     │ PostgreSQL       │
                   │                     │ + pgvector       │
                   │                     └────────┬─────────┘
                   │                              │
                   └──────────────┬───────────────┘
                                  ▼
                       ┌────────────────────────┐
                       │ Similarity Search      │
                       │ + Decision Engine      │
                       └────────────┬───────────┘
                                    │
                  ┌─────────────────┼──────────────────┐
                  │                 │                  │
                  ▼                 ▼                  ▼
          ┌─────────────┐   ┌──────────────┐   ┌───────────────┐
          │ AUTOMATIQUE │   │ SEMI-AUTO    │   │    MANUEL     │
          └──────┬──────┘   └──────┬───────┘   └───────┬───────┘
                 │                 │                   │
                 ▼                 ▼                   ▼
          ┌─────────────┐   ┌──────────────┐   ┌───────────────┐
          │ Email SMTP  │   │ Validation   │   │ Notion Ticket │
          └─────────────┘   │   Agent      │   └───────┬───────┘
                            └──────────────┘           │
                                                      ▼
                                             ┌────────────────┐
                                             │ Telegram Alert │
                                             └────────────────┘
```

---

# 📁 Structure du dépôt

```text
systeme-reclamations/
│
├── init-knowledge-base.json
├── process-complaints.json
├── README.md
│
└── docs/
    └── screenshots/
```

### Workflows

| Fichier                    | Description                         |
| -------------------------- | ----------------------------------- |
| `init-knowledge-base.json` | Initialise la base de connaissances |
| `process-complaints.json`  | Workflow principal de traitement    |

---

# 🔄 Workflows n8n

## Workflow d'initialisation

Le workflow `init-knowledge-base.json` permet de préparer la base de connaissances.

```text
Manual Trigger
      │
      ▼
Clean + Format
      │
      ▼
Generate Embedding
      │
      ▼
PostgreSQL / pgvector
      │
      ▼
Knowledge Base Ready
```

---

## Workflow principal

Le workflow `process-complaints.json` constitue le cœur du système.

```text
Email IMAP
   │
   ▼
Extract Client
   │
   ├──────────────► PostgreSQL : Client
   │
   ▼
Clean Message
   │
   ▼
Generate Embedding
   │
   ├──────────────► PostgreSQL : Réclamation
   │
   ▼
Gemini Classification
   │
   ▼
Vector Similarity Search
   │
   ▼
Decision
   │
   ├──► AUTO ──────► Generate Response ───► SMTP
   │
   ├──► SEMI ──────► Agent Validation
   │
   └──► MANUAL ────► Notion Ticket
                            │
                            ▼
                      Telegram Alert
```

---

# 🛠️ Stack technique

## Orchestration

| Technologie | Utilisation                                   |
| ----------- | --------------------------------------------- |
| **n8n**     | Orchestration et automatisation des workflows |

## Intelligence artificielle

| Technologie                 | Utilisation                              |
| --------------------------- | ---------------------------------------- |
| **Google Gemini 2.5 Flash** | Classification et génération de réponses |
| **text-embedding-004**      | Transformation des textes en vecteurs    |

## Data & recherche

| Technologie    | Utilisation                        |
| -------------- | ---------------------------------- |
| **PostgreSQL** | Stockage relationnel               |
| **pgvector**   | Stockage et recherche vectorielle  |
| **SQL**        | Requêtes et traitement des données |

## Communication & intégrations

| Technologie          | Utilisation              |
| -------------------- | ------------------------ |
| **IMAP**             | Réception des emails     |
| **SMTP**             | Envoi des réponses       |
| **Notion API**       | Gestion des tickets      |
| **Telegram Bot API** | Notifications et alertes |

---

# 🗄️ Modèle de données

Le système repose notamment sur trois ensembles de données principaux.

## `clients`

Stocke les informations relatives aux clients.

```sql
CREATE TABLE clients (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    nom VARCHAR(255),
    prenom VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW()
);
```

---

## `reclamations`

Stocke les réclamations et leurs informations d'analyse.

```sql
CREATE TABLE reclamations (
    id SERIAL PRIMARY KEY,
    client_id INTEGER REFERENCES clients(id),
    message TEXT NOT NULL,
    sujet VARCHAR(500),
    embedding vector(768),
    categorie VARCHAR(100),
    statut VARCHAR(50) DEFAULT 'nouvelle',
    solution_id INTEGER,
    score_similarite NUMERIC(5,2),
    created_at TIMESTAMP DEFAULT NOW()
);
```

---

## `base_connaissance`

Contient les problèmes connus et leurs solutions.

```sql
CREATE TABLE base_connaissance (
    id SERIAL PRIMARY KEY,
    code_template VARCHAR(100) UNIQUE NOT NULL,
    description_probleme TEXT NOT NULL,
    categorie VARCHAR(100) NOT NULL,
    embedding vector(768),
    template_reponse TEXT NOT NULL,
    utilisation_count INTEGER DEFAULT 0,
    actif BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Activation de pgvector

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

---

# 🚀 Installation

## Prérequis

Avant de démarrer, installer/configurer :

* **n8n** — cloud ou self-hosted ;
* **PostgreSQL ≥ 14** ;
* **pgvector** ;
* une clé API Google Gemini ;
* une intégration Notion ;
* un bot Telegram ;
* un serveur IMAP ;
* un serveur SMTP.

---

## 1. Cloner le projet

```bash
git clone https://github.com/BSK-202/Syst-me-de-Gestion-Automatis-e-des-R-clamations-Clients.git

cd Syst-me-de-Gestion-Automatis-e-des-R-clamations-Clients
```

---

## 2. Préparer PostgreSQL

Créer la base :

```bash
createdb reclamations_db
```

Activer pgvector :

```bash
psql reclamations_db -c "CREATE EXTENSION IF NOT EXISTS vector;"
```

Créer ensuite les tables nécessaires.

---

## 3. Importer les workflows n8n

Dans n8n :

1. ouvrir **Workflows** ;
2. sélectionner **Import from File** ;
3. importer `init-knowledge-base.json` ;
4. importer `process-complaints.json`.

Il est recommandé d'initialiser la base de connaissances avant d'activer le workflow principal.

---

## 4. Configurer les credentials

Les credentials nécessaires sont :

| Credential    | Service                   |
| ------------- | ------------------------- |
| PostgreSQL    | Base de données           |
| Google Gemini | Intelligence artificielle |
| IMAP          | Réception des emails      |
| SMTP          | Envoi des emails          |
| Notion        | Gestion des tickets       |
| Telegram      | Notifications             |

Les secrets doivent être configurés dans le système de credentials de n8n et **ne doivent pas être stockés directement dans les workflows exportés**.

---

## 5. Initialiser la base de connaissances

Ouvrir :

```text
init-knowledge-base.json
```

Puis exécuter le workflow depuis le trigger manuel.

Vérifier ensuite que les templates de la base de connaissances ont bien été insérés dans PostgreSQL.

---

## 6. Activer le workflow

Ouvrir :

```text
process-complaints.json
```

Vérifier les credentials et les connexions, puis activer le workflow.

À partir de ce moment, les nouveaux emails peuvent être automatiquement traités.

---

# ⚙️ Configuration

Il est recommandé d'utiliser des variables d'environnement ou les **Credentials n8n** plutôt que des valeurs sensibles directement dans les workflows.

Exemple :

```env
GOOGLE_GEMINI_API_KEY=

DB_HOST=localhost
DB_PORT=5432
DB_NAME=reclamations_db
DB_USER=postgres
DB_PASSWORD=

NOTION_API_KEY=
NOTION_DATABASE_ID=

TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=

IMAP_HOST=
IMAP_PORT=993
IMAP_USER=
IMAP_PASSWORD=

SMTP_HOST=
SMTP_PORT=465
SMTP_USER=
SMTP_PASSWORD=
```

---

# 🔍 Fonctionnement détaillé

## Étape 1 — Réception

Le trigger IMAP détecte un nouvel email.

```text
Email entrant
      │
      ▼
IMAP Trigger
```

---

## Étape 2 — Extraction

Le système extrait :

* identité du client ;
* adresse email ;
* sujet ;
* contenu du message.

Le texte est ensuite normalisé.

---

## Étape 3 — Classification

Gemini analyse le contenu et sélectionne une catégorie parmi les catégories définies dans le système.

```text
Réclamation
     │
     ▼
Gemini
     │
     ▼
Catégorie
```

---

## Étape 4 — Embedding

Le contenu est transformé en représentation vectorielle.

```text
Texte
  │
  ▼
Embedding Model
  │
  ▼
Vector 768 dimensions
```

---

## Étape 5 — Recherche vectorielle

Le vecteur est comparé aux vecteurs présents dans la base de connaissances.

La recherche utilise la distance cosinus via pgvector.

```sql
ORDER BY embedding <=> query_embedding
```

Le système récupère la solution présentant la meilleure similarité.

---

## Étape 6 — Décision

Le score obtenu est comparé aux seuils configurés :

```text
                    Score
                      │
          ┌───────────┼───────────┐
          │           │           │
        >= 0.85    0.65–0.85    < 0.65
          │           │           │
          ▼           ▼           ▼
        AUTO        SEMI         MANUEL
```

---

## Étape 7 — Traitement

### Traitement automatique

Gemini génère une réponse contextualisée qui est envoyée via SMTP.

### Traitement semi-automatique

Une proposition de réponse est soumise à validation humaine.

### Traitement manuel

Un ticket est créé dans Notion et une notification Telegram peut être envoyée au responsable.

---

# 🧪 Tests

## Test fonctionnel

Pour tester le workflow :

1. envoyer un email de test ;
2. vérifier le déclenchement du workflow n8n ;
3. inspecter les données extraites ;
4. vérifier la classification ;
5. vérifier la recherche vectorielle ;
6. contrôler la décision ;
7. vérifier l'envoi ou l'escalade.

---

## Vérification PostgreSQL

### Dernières réclamations

```sql
SELECT
    r.id,
    c.email,
    r.categorie,
    r.score_similarite,
    r.statut,
    r.created_at
FROM reclamations r
JOIN clients c
    ON c.id = r.client_id
ORDER BY r.created_at DESC
LIMIT 10;
```

### Templates les plus utilisés

```sql
SELECT
    code_template,
    utilisation_count
FROM base_connaissance
ORDER BY utilisation_count DESC;
```

---

# 📊 Indicateurs de suivi

Les objectifs définis pour le projet comprennent notamment :

| Indicateur                       |        Objectif |
| -------------------------------- | --------------: |
| Temps de traitement              | `< 30 secondes` |
| Précision de classification      |        `> 90 %` |
| Taux d'automatisation            |        `> 60 %` |
| Réduction du temps de traitement |         `~95 %` |

> **Important :** ces valeurs correspondent à des **objectifs/valeurs cibles du projet** et ne doivent pas être présentées comme des résultats expérimentaux mesurés sans campagne de tests permettant de les vérifier.

---

# 🗺️ Roadmap

Les évolutions envisagées comprennent :

* [ ] Dashboard de supervision avec Metabase ou Grafana
* [ ] Amélioration de la classification
* [ ] Support multilingue : français, arabe et anglais
* [ ] Feedback loop basé sur les corrections des agents
* [ ] Intégration WhatsApp Business
* [ ] Détection automatique du sentiment client
* [ ] Enrichissement de la base de connaissances
* [ ] Historisation et analyse des performances du système

---

# 🔐 Sécurité

La sécurité des credentials est essentielle dans un workflow d'automatisation connecté à plusieurs services externes.

### Règles à respecter

* ❌ Ne jamais publier de clé API dans Git ;
* ❌ Ne jamais stocker de mot de passe dans un workflow exporté ;
* ❌ Ne jamais publier de token Telegram ;
* ❌ Ne jamais publier de credentials IMAP/SMTP ;
* ❌ Ne jamais exposer de secrets PostgreSQL ;
* ✅ Utiliser les **Credentials n8n** ;
* ✅ Utiliser des variables d'environnement lorsque nécessaire ;
* ✅ Ajouter les fichiers sensibles au `.gitignore` ;
* ✅ Révoquer immédiatement toute clé ayant été accidentellement publiée.

### `.gitignore`

```gitignore
.env
.env.*
*.env

credentials.json
secrets.json

node_modules/
.n8n/
```

> Si des credentials ont déjà été publiés dans l'historique Git, les supprimer du fichier actuel ne suffit pas : les secrets concernés doivent être **révoqués et régénérés**.

---

# 📸 Documentation visuelle

Pour documenter le projet, il est recommandé d'ajouter des captures dans :

```text
docs/
└── screenshots/
    ├── n8n-workflow.png
    ├── email-input.png
    ├── generated-response.png
    ├── notion-ticket.png
    └── telegram-alert.png
```

Ces captures permettent de comprendre rapidement le fonctionnement du système sans devoir importer les workflows n8n.

---

# 🤝 Contribution

Les contributions sont les bienvenues.

```bash
# Créer une branche
git checkout -b feature/nom-de-la-feature

# Ajouter les modifications
git add .

# Créer un commit
git commit -m "feat: ajout de la fonctionnalité"

# Envoyer la branche
git push origin feature/nom-de-la-feature
```

Puis ouvrir une **Pull Request**.

---

# 📄 Licence

Projet réalisé dans un cadre **académique et personnel**.

Tous droits réservés © 2026 **BSK-202 / Ikrame BASKANE**.

---

# 👩‍💻 Auteur

<div align="center">

### Ikrame BASKANE

**Ingénieure en Génie Logiciel & Intégration des Systèmes Informatiques**

[![GitHub](https://img.shields.io/badge/GitHub-BSK--202-181717?style=for-the-badge\&logo=github)](https://github.com/BSK-202)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Ikrame_BASKANE-0A66C2?style=for-the-badge\&logo=linkedin)](https://linkedin.com/in/ikrame-baskane-781629279)

</div>

---

<div align="center">

### 🎯 Intelligent Complaint Management

**Automatisation • IA • Recherche sémantique • Workflow • Human-in-the-loop**

⭐ Si ce projet vous semble intéressant, n'hésitez pas à consulter le dépôt et à laisser une étoile.

</div>
