# 🚀 Guide de Démarrage Rapide - Workflow Ultravox n8n

> **Mettez votre workflow en marche en moins de 10 minutes !**

## ⚡ Installation Express (5 minutes)

### 1. Prérequis Rapides
```bash
# Vérifier Docker
docker --version
docker-compose --version

# Si pas installé:
# Ubuntu/Debian: apt install docker.io docker-compose
# Mac: brew install docker docker-compose
# Windows: Télécharger Docker Desktop
```

### 2. Installation Automatique
```bash
# Télécharger et installer
wget -O install.sh https://raw.githubusercontent.com/votre-repo/workflow-ultravox/main/install-workflow.txt
chmod +x install.sh
./install.sh
```

### 3. Démarrage Immédiat
```bash
cd ~/ultravox-workflow
./start.sh
```

✅ **Terminé !** Votre workflow est maintenant accessible sur http://localhost:5678

---

## 🔑 Configuration Minimale (3 minutes)

### 1. Accéder à n8n
- **URL** : http://localhost:5678
- **Login** : admin  
- **Mot de passe** : changeme123

### 2. Importer le Workflow
1. Cliquer sur **"Workflows"** → **"Import"**
2. Sélectionner `workflow-ultravox-n8n.json`
3. Cliquer sur **"Save"**

### 3. Configurer les Credentials Essentiels

#### Ultravox API (Obligatoire)
```
Type: HTTP Header Auth
Name: X-API-Key  
Value: votre_cle_api_ultravox
```

#### PostgreSQL (Déjà configuré)
```
Host: postgres
Port: 5432
Database: ultravox_db
User: ultravox_user
Password: ultravox_password_123
```

---

## 🧪 Test Rapide (2 minutes)

### 1. Test Webhook Simple
```bash
curl -X POST http://localhost:5678/webhook/ultravox-webhook \
  -H "Content-Type: application/json" \
  -d '{"trigger": "test"}'
```

### 2. Vérifier l'Exécution
1. Dans n8n, aller sur **"Executions"**
2. Voir la dernière exécution
3. Vérifier que tous les nœuds sont verts ✅

### 3. Contrôler la Base de Données
```bash
docker exec -it postgres-ultravox psql -U ultravox_user -d ultravox_db -c "SELECT COUNT(*) FROM ultravox_stats;"
```

---

## 📱 Configuration des Notifications (Optionnel)

### SMS via Twilio (2 minutes)
1. Dans n8n → **Credentials** → **Add**
2. Type : **Twilio**
3. Remplir :
   - Account SID
   - Auth Token  
   - Phone Number

### Email via Gmail (2 minutes)
1. Dans n8n → **Credentials** → **Add**
2. Type : **Gmail OAuth2**
3. Suivre le processus OAuth

### Google Calendar (2 minutes)
1. Dans n8n → **Credentials** → **Add**
2. Type : **Google Calendar OAuth2**
3. Autoriser les permissions

---

## 🎯 Utilisation Immédiate

### Déclenchement Manuel
```bash
# Test complet avec données réelles
curl -X POST http://localhost:5678/webhook/ultravox-webhook \
  -H "Content-Type: application/json" \
  -d '{
    "trigger": "manual",
    "filters": {
      "dateFrom": "2025-01-01",
      "dateTo": "2025-01-18"
    }
  }'
```

### Programmation Automatique
1. Dans le workflow, remplacer **"Webhook Trigger"** par **"Cron"**
2. Configurer : `0 9 * * *` (tous les jours à 9h)
3. Sauvegarder et activer

### Monitoring en Temps Réel
```bash
# Suivre les logs
docker-compose logs -f n8n

# Statistiques des conteneurs  
docker stats n8n-ultravox postgres-ultravox
```

---

## 🔧 Personnalisation Rapide

### Modifier la Détection RDV
Dans le nœud **"Analyser Transcript pour RDV"** :
```javascript
// Ajouter vos mots-clés
const rdvKeywords = [
  'rendez-vous', 'rdv', 'meeting', 'appointment',
  'consultation', 'entretien', 'visite'  // ← Ajouter ici
];
```

### Changer les Templates de Message
Dans le nœud **"Envoyer Confirmation SMS"** :
```javascript
// Personnaliser le message
const message = `
🕐 RDV confirmé !
📅 ${rdvDetails.date} à ${rdvDetails.time}
📍 Votre adresse ici
📞 Contact: 01 23 45 67 89
`;
```

### Ajouter des Intégrations
```bash
# Slack
curl -X POST https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK \
  -d '{"text": "Nouveau RDV confirmé via Ultravox!"}'

# Zapier  
curl -X POST https://hooks.zapier.com/hooks/catch/YOUR/HOOK \
  -d '{"event": "rdv_confirmed", "data": {...}}'
```

---

## 📊 Dashboard Instantané

### Requêtes SQL Utiles
```sql
-- Statistiques du jour
SELECT * FROM ultravox_stats WHERE DATE(timestamp) = CURRENT_DATE;

-- RDV détectés aujourd'hui
SELECT COUNT(*) FROM ultravox_transcripts 
WHERE rdv_detected = true AND DATE(processed_at) = CURRENT_DATE;

-- Derniers rendez-vous confirmés
SELECT title, start_date, participant_email 
FROM confirmed_appointments 
ORDER BY created_at DESC 
LIMIT 5;
```

### Interface Web Simple
```bash
# Accès direct à la base via Adminer
docker run --rm -p 8080:8080 --network ultravox-workflow_ultravox-network adminer

# Connexion: 
# Serveur: postgres
# Utilisateur: ultravox_user
# Base: ultravox_db
# Mot de passe: ultravox_password_123
```

---

## 🚨 Dépannage Express

### Problèmes Courants

#### ❌ "Cannot connect to n8n"
```bash
# Vérifier les services
docker-compose ps

# Redémarrer si nécessaire
docker-compose restart n8n
```

#### ❌ "Webhook not found"
```bash
# Vérifier l'URL
curl http://localhost:5678/webhook/ultravox-webhook

# Réactiver le workflow dans n8n
```

#### ❌ "Database connection failed"
```bash
# Vérifier PostgreSQL
docker-compose logs postgres

# Recréer si nécessaire
docker-compose down
docker-compose up -d
```

#### ❌ "Ultravox API 401"
1. Vérifier la clé API dans n8n Credentials
2. Tester manuellement :
```bash
curl -H "X-API-Key: votre_cle" https://api.ultravox.ai/api/calls
```

---

## 🎯 Checklist de Vérification

- [ ] ✅ Services Docker démarrés
- [ ] ✅ n8n accessible (localhost:5678)
- [ ] ✅ Workflow importé et activé
- [ ] ✅ Credentials Ultravox configurés
- [ ] ✅ Base PostgreSQL connectée
- [ ] ✅ Test webhook réussi
- [ ] ✅ Première exécution complète
- [ ] ✅ Données en base de données

---

## 📞 Support Rapide

### Documentation Complète
- 📖 **Guide détaillé** : `instructions-workflow-ultravox.md`
- 🧪 **Tests** : `test-workflow.json`
- ⚙️ **Configuration** : `config-exemple.env`

### Liens Utiles
- 🌐 **Interface n8n** : http://localhost:5678
- 🗄️ **Base de données** : localhost:5432
- 📊 **Adminer** : http://localhost:8080 (si installé)

### Commandes de Gestion
```bash
./start.sh      # Démarrer
./stop.sh       # Arrêter  
./backup.sh     # Sauvegarder
./update.sh     # Mettre à jour
```

---

## 🎉 Félicitations !

Votre workflow Ultravox n8n est maintenant opérationnel ! 

**Prochaines étapes :**
1. 📧 Configurer les notifications (SMS/Email)
2. 📅 Tester la création d'événements calendrier
3. 📊 Explorer les rapports et statistiques
4. 🔄 Programmer l'exécution automatique
5. 🎨 Personnaliser selon vos besoins

**Need help?** Consultez la documentation complète ou contactez le support.

---

<div align="center">

**🎯 Votre assistant IA transforme maintenant vos appels en rendez-vous confirmés automatiquement ! 🎯**

*Happy automation! 🤖*

</div>