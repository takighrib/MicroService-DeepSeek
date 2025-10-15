# 🏗️ Architecture Microservices DeepSeek avec API Gateway

| Student Name | Class |
|---------------|--------|
| **Yesmine Zhioua** | 3 IDL 2 |
| **Ahmed Takieddine Ghrib** | 3 IDL 2 |

Ce document présente l'évolution de l'architecture microservices de DeepSeek, comparant une approche simple avec une seule API Gateway à une architecture avancée avec middleware et multiples gateways.

---

## 📋 Table des Matières
1. [Architecture Initiale](#1-architecture-initiale---1-api-gateway)
2. [Analyse de Fonctionnement](#2-analyse-de-fonctionnement)
3. [Architecture Améliorée](#3-architecture-améliorée---middleware--2-api-gateway--fallbacks-parallèle)
4. [Architecture Parallèle](#4-architecture-parallèle)
5. [Comparaison des Architectures](#5-comparaison-des-deux-architectures)
6. [Preuves Techniques](#6-preuves-techniques)

---

## 1. Architecture Initiale - 1 API Gateway

### ✅ Avantages
- **Simplicité** : Une seule porte d'entrée  
- **Facilité de déploiement et maintenance**  
- **Centralisation** des règles (authentification, quotas, monitoring)

### ❌ Limitations
- **Single Point of Failure (SPOF)**  
- **Scalabilité limitée**  
- **Couplage fort** entre services  
- **Surface d'attaque** plus large

### 🔧 Diagramme Architecture Initiale
![Architecture](image/img1.png)

---

## 2. Analyse de Fonctionnement
![Architecture](image/img2.png)

Flux de requête typique :

1. **Client** → Envoi requête  
2. **API Gateway** → Validation  
3. **Load Balancer** → Distribution  
4. **Service Auth** → Vérification API key  
5. **Service Utilisateurs** → Vérification quota  
6. **Cache Redis** → Recherche réponse  
   - ✅ Si trouvée : **Retour immédiat**  
   - ❌ Sinon : Suite du processus  
7. **Service Chat/Core AI** → Préparation contexte  
8. **Service Modèles** → Sélection modèle  
9. **Service Inference** → Génération réponse  
10. **Cache Redis** → Stockage réponse  
11. **Service Monitoring** → Enregistrement métriques  
12. **Retour au client** (streaming ou complet)

---

## 3. Architecture Améliorée - Middleware + 2 API Gateway + Fallbacks Parallèle

### 🚀 Améliorations Apportées
- **Middleware Router** analyse et route les requêtes vers la gateway appropriée  
- **Répartition de charge** + isolation par domaine  
- **2 API Gateways** séparées pour différents services  
- **Système de Fallback** dédié par gateway  
- **Architecture parallèle** pour haute disponibilité  

### 🔧 Diagramme Architecture Améliorée
![Architecture](image/img3.png)

### ✅ Avantages
- **Moins de charge** par Gateway → meilleure scalabilité  
- **Isolation des pannes** : défaillance d'une gateway n'affecte pas l'autre  
- **Sécurité renforcée** : règles spécifiques par domaine  
- **Disponibilité partielle** même en cas de panne  
- **Parallélisation** des services pour performance optimale  

### 🔄 Exemples de Fallback
- **Gateway 1** → retourne cache Redis pour Auth/User  
- **Gateway 2** → retourne message dégradé pour Orders  

### 🎯 Répartition des Services
**API Gateway 1 :** Services critiques d'authentification  
- Auth Services (parallèles)  
- User Services (parallèles)  
- Quota Services (parallèles)  

**API Gateway 2 :** Services métier  
- Orders Services (parallèles)  
- Articles Services (parallèles)  
- Analytics Services (parallèles)  

---

## 4. Architecture Parallèle
![Architecture](image/img4.png)

---

## 5. Comparaison des Deux Architectures

| Critère | 1 API Gateway | 2 API Gateway + Fallback Parallèle |
|----------|---------------|------------------------------------|
| **Disponibilité** | ❌ Point unique de défaillance (SPOF) | ✅ Haute disponibilité grâce aux fallbacks |
| **Résilience** | ❌ Faible tolérance aux pannes | ✅ Résilience accrue grâce au fallback automatique |
| **Performance** | ⚠️ Risque de goulot d'étranglement | ✅ Répartition de charge, meilleure scalabilité |
| **Scalabilité** | ❌ Limitée | ✅ Horizontale avec services parallèles |
| **Complexité** | ✅ Simple à déployer et maintenir | ⚠️ Plus complexe : synchronisation, routage et fallback |
| **Sécurité** | ⚠️ Une seule couche de sécurité | ✅ Deux points d'entrée : stratégies différenciées |
| **Isolation** | ❌ Couplage fort | ✅ Isolation par domaine |

---

## 🏆 Conclusion

L'architecture améliorée avec **middleware, multiples gateways et services parallèles** offre une **meilleure résilience, scalabilité et sécurité** grâce à :

- ✅ **Routage intelligent** via middleware  
- ✅ **Isolation des pannes** avec 2 gateways indépendantes  
- ✅ **Haute disponibilité** avec systèmes de fallback  
- ✅ **Performance optimisée** via parallélisation des services  
- ✅ **Scalabilité horizontale** naturelle  

Cette approche est particulièrement adaptée pour les applications critiques nécessitant une haute disponibilité et performance.

---

## 6. 🧠 Preuves Techniques

### 🧩 Preuve 1 : Optimisation côté client par découpage fonctionnel et multithreading

L'architecture client est conçue pour être à la fois légère et performante grâce à une division du code en fonctions indépendantes et spécifiques à une tâche donnée.  
Chaque fonction est ensuite exécutée dans un thread séparé, ce qui permet d'exploiter pleinement la puissance du processeur et d'effectuer plusieurs traitements en parallèle.  
Cette approche réduit les temps d'attente, améliore la réactivité de l'interface et optimise l'utilisation des ressources matérielles.  
En cas de défaillance d'un thread, seul le traitement concerné est impacté, ce qui renforce la fiabilité du système.  

👉 **Résultat :** exécution plus rapide et robuste côté client.

---

### 🌐 Preuve 2 : Haute disponibilité grâce à la réplication des serveurs

Afin d'assurer une disponibilité continue du service, chaque microservice est répliqué sur plusieurs serveurs.  
Cette redondance permet au système de continuer à fonctionner même si un ou plusieurs serveurs tombent en panne.  
Lorsqu'un serveur devient indisponible, une autre instance identique prend automatiquement le relais, assurant la continuité du traitement des requêtes sans interruption perceptible.  

👉 **Résultat :** meilleure répartition de charge et tolérance accrue aux pannes.

---

### ⚙️ Preuve 3 : Parallélisme global assuré par un système d'exploitation homogène

Le parallélisme à grande échelle est rendu possible par le déploiement d’un **système d’exploitation homogène** ou d’une **couche d’abstraction commune** sur tous les nœuds.  
Cela garantit que la synchronisation, la communication et la gestion des processus sont cohérentes et prévisibles sur l’ensemble du cluster.  

👉 **Résultat :** exécution parallèle stable, coordination efficace et performances homogènes sur tous les serveurs.

---

### 🔁 Preuve 4 : Résilience et débit garantis par le routage parallèle et le fallback

Les **API Gateways** sont organisées en paires avec des mécanismes de **fallback** qui assurent une continuité de service.  
En cas de panne d’une gateway ou d’un microservice, une autre instance prend instantanément le relais.  
Le **routage parallèle** permet de traiter simultanément un grand nombre de requêtes, augmentant ainsi le débit global.  

👉 **Résultat :** meilleure performance, haute résilience et expérience utilisateur sans interruption.

---

*Documentation technique DeepSeek — Architecture Microservices Avancée (Version 2.0)*

