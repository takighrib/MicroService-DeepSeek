# Architecture Microservices DeepSeek - Documentation

## 📋 Table des Matières
- [Aperçu](#aperçu)
- [Architectures Comparées](#architectures-compared)
- [Flux de Requêtes](#flux-de-requêtes)
- [Composants](#composants)
- [Décisions d'Architecture](#décisions-darchitecture)
- [Sécurité](#sécurité)
- [Monitoring](#monitoring)
- [Déploiement](#déploiement)

## 🏗️ Aperçu


Ce document décrit l'évolution de l'architecture microservices de DeepSeek d'une architecture simple à 1 API Gateway vers une architecture résiliente avec multiple gateways et fallbacks.

![Architecture  (/img1.png)]

## 📊 Architectures Comparées

### Architecture Initiale - 1 API Gateway








**Avantages:**
- ✅ Simplicité de déploiement et maintenance
- ✅ Point unique de configuration
- ✅ Centralisation du monitoring

**Limites:**
- ❌ Single Point of Failure (SPOF)
- ❌ Scalabilité limitée
- ❌ Couplage fort entre services
- ❌ Surface d'attaque étendue

### Architecture Améliorée - 2 API Gateways + Fallbacks



**Améliorations:**
- 🚀 Haute disponibilité avec élimination du SPOF
- 🚀 Isolation des pannes par domaine
- 🚀 Meilleure scalabilité horizontale
- 🚀 Sécurité renforcée par segmentation
- 🚀 Résilience avec systèmes de fallback

## 🔄 Flux de Requêtes

### Workflow Détaillé
1. **Client** envoie une requête
2. **Middleware Router** analyse et route vers la gateway appropriée
3. **API Gateway** valide et authentifie la requête
4. **Service Auth** vérifie les credentials et API keys
5. **Service Utilisateurs** valide les quotas et permissions
6. **Cache Redis** recherche les réponses en cache
7. **Si cache miss** → traitement complet:
   - Service Chat prépare le contexte
   - Service Modèles sélectionne le modèle
   - Service Inference génère la réponse
8. **Cache** stocke la réponse pour requêtes futures
9. **Monitoring** enregistre les métriques
10. **Retour** au client (streaming ou complet)

## 🧩 Composants

### 🎯 Core Services
| Service | Rôle | Base de Données |
|---------|------|-----------------|
| **Auth Service** | Authentification & autorisation | DB Auth |
| **User Service** | Gestion utilisateurs & quotas | DB Users |
| **Chat/Core AI** | Traitement conversations | DB Chat |
| **Model Service** | Gestion des modèles AI | DB Models |
| **Inference Service** | Génération des réponses | - |
| **Training Service** | Entraînement modèles | DB Training |
| **Monitoring Service** | Métriques & logging | - |

### 🏗️ Infrastructure
| Composant | Rôle | Technologie |
|-----------|------|-------------|
| **API Gateway** | Point d'entrée, routing, auth | Kong/Apisix |
| **Load Balancer** | Distribution charge | Nginx/Haproxy |
| **Message Queue** | Communication async | Kafka |
| **Cache** | Stockage temporaire | Redis |
| **Middleware Router** | Routing intelligent | Custom |

### 🛡️ Fallback Systems
- **Fallback Gateway 1**: Cache Redis pour Auth/User
- **Fallback Gateway 2**: Messages dégradés pour Orders
- **Timeout management** avec retry logic
- **Circuit breaker** patterns

## 🎯 Décisions d'Architecture

### 1. Séparation par Domaines

### 2. Stratégie de Cache
- **Redis** pour cache distribué
- **TTL** adaptatif par type de données
- **Invalidation** proactive sur updates

### 3. Communication Services
- **Sync**: HTTP/REST pour requêtes immédiates
- **Async**: Kafka pour traitement batch
- **Events**: Webhooks pour notifications

## 🔒 Sécurité

### Couches de Sécurité
1. **Middleware**: Rate limiting, IP filtering
2. **API Gateway**: JWT validation, API keys
3. **Services**: Authentication context propagation
4. **Database**: Encryption at rest, connection pooling

### Best Practices
- ✅ Secrets management avec Vault
- ✅ TLS/SSL mutual authentication
- ✅ Regular security audits
- ✅ Input validation à tous les niveaux

## 📈 Monitoring

### Métriques Clés
```yaml
performance:
  - response_time_p95
  - requests_per_second
  - error_rate
  
business:
  - active_users
  - inference_requests
  - cache_hit_ratio
  
infrastructure:
  - cpu_memory_usage
  - database_connections
  - queue_depth

  
Ce README fournit:

1. **📖 Documentation complète** - Tous les aspects techniques couverts
2. **🎯 Comparaison claire** - Avantages/inconvénients des deux architectures  
3. **🔧 Guide pratique** - Implémentation et bonnes pratiques
4. **🚀 Vision stratégique** - Roadmap d'évolution
5. **🛡️ Focus sécurité** - Bonnes pratiques et recommandations
6. **📈 Monitoring** - Métriques essentielles et outils

Il est structuré pour être utile aux nouveaux développeurs comme aux architectes expérimentés !