# Documentation théorique de la base de données MARKET
## 1. Présentation générale
MARKET est une plateforme qui est organisée autour de **9 services métier**. Chaque service a un rôle bien précis dans le fonctionnement de la plateforme et chaque service gère sa propre base de données.
Les 9 services sont :
* Seller & Store Service
* Catalog Service
* Inventory Service
* Cart Service
* Checkout Service
* Order Service
* Review & Trust Service
* Search & Discovery Service
* Market Operations Service
L'objectif de cette organisation est de permettre à chaque service de gérer ses propres données de manière indépendante, tout en pouvant communiquer avec les autres services lorsque cela est nécessaire.
La technologie utilisée pour la gestion des bases de données est **PostgreSQL**.
L'architecture prévoit également l'utilisation de **Redis** pour le cache et de **Kafka ou RabbitMQ** pour permettre les échanges d'événements entre les différents services.
## 2. Architecture des bases de données

Dans MARKET, nous avons choisi une organisation où **chaque service possède sa propre base de données**.

On a donc 9 services et 9 bases de données :

| Service            | Base de données          | Fonction principale                                 |
| ------------------ | ------------------------ | --------------------------------------------------- |
| Seller & Store     | `market_seller_store_db` | Gestion des vendeurs et des boutiques               |
| Catalog            | `market_catalog_db`      | Gestion des produits et du catalogue                |
| Inventory          | `market_inventory_db`    | Gestion du stock et des réservations                |
| Cart               | `market_cart_db`         | Gestion du panier client                            |
| Checkout           | `market_checkout_db`     | Préparation et validation de l'achat                |
| Order              | `market_order_db`        | Gestion des commandes et des références de paiement |
| Review & Trust     | `market_review_trust_db` | Gestion des avis, des notes et de la modération     |
| Search & Discovery | `market_search_db`       | Recherche et indexation des produits                |
| Market Operations  | `market_operations_db`   | Administration, modération et opérations            |

L'architecture peut être représentée simplement comme ceci :

```text
                         MARKET
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
     Services          BELIEVE SDK          Events
        │                   │                   │
        ▼                   ▼                   ▼
  ┌─────────────┐       BELIEVE Core       Event Bus
  │ PostgreSQL  │
  └─────────────┘
        │
        ├── market_seller_store_db
        ├── market_catalog_db
        ├── market_inventory_db
        ├── market_cart_db
        ├── market_checkout_db
        ├── market_order_db
        ├── market_review_trust_db
        ├── market_search_db
        └── market_operations_db
```
Cette organisation permet de bien séparer les responsabilités. Un service ne va pas directement chercher les données dans la base de données d'un autre service. Les échanges entre les services se font plutôt à travers les **API, le BELIEVE SDK ou les événements**.
## 3. Pourquoi utiliser plusieurs bases de données ?
Nous avons choisi de séparer les bases de données parce que chaque service possède son propre domaine de responsabilité.
Par exemple, le **Catalog Service** s'occupe des produits, des catégories, des variantes et des prix. Il utilise donc :
`market_catalog_db`
De son côté, le **Inventory Service** s'occupe du stock et des réservations. Il utilise :
`market_inventory_db`
Cela permet de mieux organiser les données et de savoir clairement quel service est responsable de chaque information.

Cette séparation permet aussi d'éviter qu'un service puisse modifier directement les données qui appartiennent à un autre service
## 4. Utilisation de PostgreSQL

Pour notre projet, nous avons choisi **PostgreSQL comme système de gestion de base de données**.

PostgreSQL est utilisé pour créer et gérer les différentes bases de données de MARKET.

Il permet notamment de gérer :

* les tables ;
* les relations entre les tables ;
* les clés primaires ;
* les clés étrangères ;
* les différents types de données ;
* les contraintes ;
* les requêtes SQL.
Dans notre cas, PostgreSQL permet donc de mettre en place la structure des 9 bases de données définies pour MARKET
## 5. Modélisation avec UML
Avant de créer les bases de données, nous avons utilisé **UML pour modéliser la structure de la base de données**.
La modélisation nous permet de représenter les différentes entités et de voir comment elles sont liées entre elles.
Par exemple, pour le catalogue, nous pouvons avoir :
```text
Category
    │
    └──── Product
              │
              ├──── Product Variant
              ├──── Product Price
              ├──── Product Media
              └──── Product Attribute
```
La modélisation UML nous aide donc à comprendre les relations entre les différentes données avant de passer à leur création dans PostgreSQL.
Elle permet également de réfléchir aux **cardinalités** entre les différentes entités.
Par exemple, une catégorie peut avoir plusieurs produits, tandis qu'un produit appartient à une catégorie.
La modélisation est donc une étape importante avant de créer réellement les tables dans la base de données.
## 6. Utilisation de pgAdmin 4
Pour travailler avec PostgreSQL, nous avons utilisé **pgAdmin 4** comme interface de gestion.
pgAdmin 4 nous permet de travailler plus facilement avec PostgreSQL sans avoir besoin de faire toutes les opérations uniquement avec le terminal.
Avec pgAdmin 4, nous pouvons notamment :
* créer les bases de données ;
* créer les tables ;
* ajouter les colonnes ;
* définir les types de données ;
* créer les clés primaires ;
* créer les clés étrangères ;
* exécuter des requêtes SQL ;
* vérifier les données ;
* tester le fonctionnement de la base.
Nous avons donc utilisé pgAdmin 4 pour effectuer différents tests et vérifier que la structure de nos bases de données fonctionne correctement.
## 7. Organisation générale des données

Chaque base de données contient les tables qui correspondent à son service.

Par exemple :

### Seller & Store

```text
market_seller_store_db
        │
        ├── sellers
        ├── seller_profiles
        ├── seller_status_history
        ├── stores
        ├── store_settings
        ├── store_hours
        └── store_status_history
```
### Catalog
```text
market_catalog_db
        │
        ├── categories
        ├── products
        ├── product_variants
        ├── product_prices
        ├── product_media
        ├── product_attributes
        └── product_status_history
```
### Inventory
```text
market_inventory_db
        │
        ├── inventory_items
        ├── inventory_reservations
        ├── inventory_movements
        └── inventory_adjustments
```
Chaque service possède donc les tables nécessaires pour gérer son propre domaine.
## 8. Relation entre les services
Même si chaque service possède sa propre base de données, les services doivent quand même travailler ensemble.
Par exemple, lorsqu'un client veut acheter un produit, plusieurs services interviennent :
**text
Catalog
   ↓
Inventory
   ↓
Cart
   ↓
Checkout
   ↓
Order
   ↓
Payment
   ↓
Delivery
   ↓
Review****

Le catalogue permet de trouver le produit, l'inventory permet de vérifier le stock, le panier permet de conserver les articles sélectionnés, le checkout vérifie les informations avant l'achat et le service Order permet de gérer la commande.
Le paiement et certaines autres fonctionnalités sont gérés par les services du **BELIEVE Core** et ne sont pas recréés directement dans MARKET.
## 9. Séparation entre MARKET et BELIEVE Core
Un point important dans notre architecture est de ne pas recréer dans MARKET des fonctionnalités qui existent déjà dans le BELIEVE Core.
MARKET gère principalement le **métier commercial** :
* vendeur ;
* boutique ;
* produit ;
* catalogue ;
* stock ;
* panier ;
* checkout ;
* commande ;
* avis ;
* recherche ;
* opérations.
Le BELIEVE Core possède de son côté certaines fonctionnalités communes comme :
* Identity ;
* Payment ;
* Wallet ;
* Ledger ;
* Compliance ;
* Notification ;
* Map ;
* Pricing ;
* Campaign ;
* Recommendation.
MARKET utilise donc ces capacités à travers les interfaces prévues, notamment le **BELIEVE SDK et les APIs Core**
## 10. Principe important de la base de données
Le principe principal que nous avons suivi est :
**Chaque service est propriétaire de sa base de données.**
Cela veut dire que le Catalog Service, par exemple, ne doit pas aller directement dans `market_inventory_db` pour modifier le stock.
Chaque service reste responsable de ses propres données.
Les communications se font à travers :
* les API ;
* le BELIEVE SDK ;
* les événements.
Cela permet d'avoir une architecture plus claire et de bien séparer les responsabilités entre les différents services.
## 11. Conclusion
La base de données MARKET est donc organisée autour de **9 bases PostgreSQL**, chacune correspondant à un service de la plateforme.
Cette organisation permet de séparer les différentes parties du système et de donner à chaque service la responsabilité de ses propres données.
Pour construire cette base de données, nous avons utilisé **UML pour faire la modélisation**, notamment pour représenter les entités, les relations et les cardinalités.
Ensuite, nous avons utilisé **PostgreSQL pour créer et gérer les bases de données**, avec **pgAdmin 4 comme interface** pour effectuer les différentes opérations et les tests.
L'objectif final est d'avoir une base de données bien organisée, où chaque service sait quelles données il possède et comment il peut communiquer avec les autres services sans accéder directement à leurs bases de données.
