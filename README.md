# Système de Gestion Automatisée des Réclamations Clients

Ce projet est un workflow n8n complet pour automatiser le traitement des réclamations clients par email, utilisant l'IA pour la classification, la recherche sémantique et la génération de réponses personnalisées.

## 📋 Vue d'ensemble

Le système capture automatiquement les emails de réclamation, extrait les informations clients, classifie le problème, trouve des solutions similaires dans une base de connaissances, et génère des réponses personnalisées avec différents niveaux d'automatisation.

## 🏗️ Architecture

### Workflows principaux

1. **`process-complaints.json`** - Traitement principal des réclamations
2. **`init-knowledge-base.json`** - Initialisation de la base de connaissances

### Composants clés

- **Email Trigger (IMAP)** - Capture des nouveaux emails
- **Extraction client** - Parsing des informations clients
- **Classification IA** - Catégorisation des problèmes
- **Embedding vectoriel** - Conversion sémantique des textes
- **Recherche de similarité** - Matching avec base de connaissances
- **Génération de réponses** - Réponses personnalisées par IA
- **Notifications** - Alertes Telegram et création tickets

## 🚀 Fonctionnalités

### 1. Capture automatique des emails
- Lecture IMAP des emails entrants
- Extraction des métadonnées (nom, prénom, email)
- Nettoyage et préparation du texte

### 2. Classification intelligente
- Catégorisation automatique en 10 types de problèmes
- Utilisation de Google Gemini pour l'analyse
- Format standardisé en MAJUSCULES avec tirets

### 3. Recherche sémantique
- Embeddings vectoriels avec modèle `text-embedding-004`
- Stockage PostgreSQL avec extension vector
- Calcul de similarité cosinus
- Décision automatique/manuelle basée sur le score

### 4. Base de connaissances
- Templates de réponses pré-définis
- Catégories organisées (livraison, produit, paiement, etc.)
- Compteur d'utilisation pour optimisation

### 5. Génération de réponses
- Personnalisation contextuelle avec Gemini
- Format HTML professionnel
- Placeholders dynamiques pour informations spécifiques

### 6. Notifications et suivi
- Création automatique de tickets Notion
- Alertes Telegram pour les managers
- Envoi automatique d'emails de réponse

## 🗄️ Structure de base de données

### Table `clients`
```sql
id | email | nom | prenom | created_at
