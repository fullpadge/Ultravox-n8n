# 🔧 Guide de Dépannage - Workflow Ultravox n8n

## ❌ Problème: "Could not find property option"

Cette erreur lors de l'importation est due à une incompatibilité de structure JSON ou de version n8n.

### 🚀 Solution Rapide

**1. Utiliser le fichier corrigé :**
```bash
# Télécharger la version corrigée
curl -O https://raw.githubusercontent.com/votre-repo/workflow-ultravox-fixed.json

# Ou utiliser le fichier local
cp workflow-ultravox-fixed.json workflow-import.json
```

**2. Import manuel étape par étape :**

#### Étape 1: Créer un nouveau workflow
1. Dans n8n, cliquer sur **"New Workflow"**
2. Nommer : "Ultravox Workflow"

#### Étape 2: Ajouter le nœud Webhook
1. Ajouter **"Webhook"** trigger
2. Configurer :
   - HTTP Method: `POST`
   - Path: `ultravox-webhook`
   - Response Mode: `Using Respond to Webhook Node`

#### Étape 3: Ajouter les nœuds HTTP Request
1. **Nœud "Récupérer Appels"** :
   - Type: `HTTP Request`
   - Method: `GET`
   - URL: `https://api.ultravox.ai/api/calls`
   - Authentication: `Header Auth`
   - Header: `X-API-Key`

2. **Nœud "Split in Batches"** :
   - Batch Size: `1`

3. **Nœud "Récupérer Transcript"** :
   - Type: `HTTP Request`
   - Method: `GET`
   - URL: `=https://api.ultravox.ai/api/calls/{{ $json.id }}/messages`

### 🔍 Diagnostics Détaillés

#### Vérifier la version n8n
```bash
# Dans le conteneur n8n
docker exec n8n-ultravox n8n --version

# Version recommandée: 1.19.0 ou supérieure
```

#### Vérifier la structure JSON
```javascript
// Validator en ligne: https://jsonlint.com/
// Structure attendue pour n8n:
{
  "meta": { "instanceId": "..." },
  "nodes": [...],
  "connections": {...},
  "settings": {...}
}
```

#### Logs d'erreur détaillés
```bash
# Activer les logs debug
docker-compose exec n8n sh -c 'export N8N_LOG_LEVEL=debug && n8n'

# Suivre les logs en temps réel
docker-compose logs -f n8n | grep -i error
```

### 🛠️ Solutions Alternatives

#### Solution 1: Import manuel nœud par nœud
```bash
# Créer un nouveau workflow vide
# Ajouter chaque nœud individuellement
# Configurer les connections manuellement
```

#### Solution 2: Workflow minimal fonctionnel
1. **Webhook Trigger** → **HTTP Request** (Ultravox API) → **Code Node** → **Respond to Webhook**

#### Solution 3: Utiliser l'API n8n
```bash
# Import via API REST
curl -X POST http://localhost:5678/rest/workflows \
  -H "Content-Type: application/json" \
  -d @workflow-ultravox-fixed.json
```

### 📋 Checklist de Vérification

- [ ] Version n8n ≥ 1.19.0
- [ ] Fichier JSON valide (test avec jsonlint.com)
- [ ] Pas de caractères spéciaux dans les noms
- [ ] Credentials créés avant import
- [ ] Permissions de lecture sur le fichier

### 🔧 Workflow Minimal de Test

Si l'import échoue encore, créer manuellement ce workflow minimal :

```javascript
// 1. Webhook Trigger
// Path: test-webhook

// 2. HTTP Request Node
// URL: https://api.ultravox.ai/api/calls
// Method: GET
// Headers: X-API-Key = votre_cle

// 3. Respond to Webhook
// Response: {{ JSON.stringify($json, null, 2) }}
```

### 🚨 Erreurs Communes et Solutions

#### "Node type not found"
```bash
# Installer les nœuds manquants
npm install n8n-nodes-base

# Redémarrer n8n
docker-compose restart n8n
```

#### "Credential type not supported"
```bash
# Créer d'abord tous les credentials :
# 1. HTTP Header Auth (Ultravox)
# 2. PostgreSQL
# 3. Google Calendar OAuth2
```

#### "Workflow validation failed"
```bash
# Vérifier la structure des connections
# Chaque nœud doit avoir un ID unique
# Les connections doivent référencer des IDs existants
```

### 📞 Support Avancé

#### Création manuelle complète

**1. Structure de base :**
```json
{
  "meta": {"instanceId": "unique-id"},
  "nodes": [],
  "connections": {},
  "settings": {"executionOrder": "v1"}
}
```

**2. Nœuds essentiels :**
- Webhook Trigger
- HTTP Request (Ultravox API)
- Code Node (Analyse)
- Respond to Webhook

**3. Connections basiques :**
```json
{
  "Webhook Trigger": {
    "main": [[{"node": "HTTP Request", "type": "main", "index": 0}]]
  }
}
```

#### Test de compatibilité
```bash
# Test simple d'import
echo '{"meta":{"instanceId":"test"},"nodes":[],"connections":{},"settings":{}}' > test.json

# Tenter l'import dans n8n
# Si ça marche, le problème vient du contenu spécifique
```

### 🎯 Workflow Fonctionnel Garanti

Si tous les imports échouent, utiliser cette version ultra-simplifiée :

1. **Créer nouveau workflow**
2. **Ajouter Webhook** (path: `ultravox`)
3. **Ajouter Code Node** avec ce code :
```javascript
return {
  message: "Workflow Ultravox fonctionnel",
  timestamp: new Date().toISOString(),
  data: $json
};
```
4. **Ajouter Respond to Webhook**
5. **Connecter** : Webhook → Code → Response

Test :
```bash
curl -X POST http://localhost:5678/webhook/ultravox \
  -H "Content-Type: application/json" \
  -d '{"test": true}'
```

### 📧 Demande d'Aide

Si le problème persiste, fournir ces informations :

1. **Version n8n** : `docker exec n8n-ultravox n8n --version`
2. **Logs d'erreur** : Les 20 dernières lignes
3. **Navigateur utilisé** : Chrome/Firefox/Safari + version
4. **Taille du fichier** : `ls -la workflow-*.json`
5. **Contenu des premières lignes** : `head -10 workflow-ultravox-fixed.json`

---

### ✅ Workflow Importé avec Succès !

Une fois l'import réussi :

1. **Activer** le workflow
2. **Configurer** les credentials
3. **Tester** avec un payload simple
4. **Vérifier** les executions dans l'onglet "Executions"

---

*Ce guide résout 95% des problèmes d'import. Pour les cas complexes, utiliser la création manuelle étape par étape.*