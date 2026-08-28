1. Présentation générale
MARKET est conçu comme une architecture composée de 9 services métier indépendants :
Seller & Store Service
Catalog Service
Inventory Service
Cart Service
Checkout Service
Order Service
Review & Trust Service
Search & Discovery Service
Market Operations Service
Le SGBD principal prévu est PostgreSQL. L'architecture prévoit également Redis pour le cache et Kafka/RabbitMQ pour le messaging.
**2. Architecture des bases de données**
                        **MARKET
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
        └── market_operations_db**
**| Service          | Base de données          | Fonction principale                 |
| ------------------ | ------------------------ | ----------------------------------- |
| Seller & Store     | `market_seller_store_db` | Vendeurs et boutiques               |
| Catalog            | `market_catalog_db`      | Produits et catalogue               |
| Inventory          | `market_inventory_db`    | Stock et réservations               |
| Cart               | `market_cart_db`         | Panier client                       |
| Checkout           | `market_checkout_db`     | Préparation et validation d'achat   |
| Order              | `market_order_db`        | Commandes et références de paiement |
| Review & Trust     | `market_review_trust_db` | Avis, notation et modération        |
| Search & Discovery | `market_search_db`       | Recherche et indexation             |
| Market Operations  | `market_operations_db`   | Administration et opérations        |**

cette base de donne est une base de donnes d'une plate forme qui contient 9 services et chaque service gere sa base de donne 
**la technologie utilise pour la base de donne est postgesql**
pour permettre de faire de tester de cardinalise nous avons utilise UML pour modelise la base de donne et faire
pour l'ide nous avons utilise pgAdmin 4 pour effectguer des test grace a son interface 
