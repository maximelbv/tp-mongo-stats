- Nombre total de transactions :

  Compter le nombre total de documents dans la collection sales.

  ```
  db.sales.countDocuments()
  ```

  > On utilise countDocuments() pour obtenir le nombre total de documents dans la collection sales.

- Ventes totales par jour :

  Donnez le total des ventes quotidiennes.

  ```
  db.sales.aggregate([
    {
      $group: {
        _id: {
          $dateToString: {
            format: "%Y-%m-%d",
            date: "$date"
          }
        },
        totalSales: {
          $sum: "$quantity"
        }
      }
    },
    { $sort: { _id: 1 } }
  ])
  ```

> On agrège les ventes en groupant par jour avec $group et en utilisant $sum sur la quantité vendue par date, et on trie par date croissante avec $sort.

- Ventes totales par produit :

  Donnez le total des ventes pour chaque produit.

  ```
  db.sales.aggregate([
    {
      $group: {
        _id: "$product_id",
        totalSales: {
          $sum: "$quantity"
        }
      }
    },
    { $sort: { totalSales: -1 } }
  ])
  ```

  > on utilise $group pour regrouper par product_id et on utilise $sum pour calculer la quantité totale vendue pour chaque produit, puis on trie par quantité décroissante avec $sort.

- Top 5 des produits les plus vendus :

  Identifiez les 5 produits avec le plus grand nombre de transactions.

  ```
  db.sales.aggregate([
    {
      $group: {
        _id: "$product_id",
        totalSales: {
          $sum: "$quantity"
        }
      }
    },
    { $sort: { totalSales: -1 } },
    { $limit: 5 }
  ])
  ```

  > Même chose que précédemment, mais avec $limit fixé à 5

- Revenu moyen par transaction :

  Calculez le revenu moyen par transaction.

  ```
  db.sales.aggregate([
    {
      $group: {
        _id: null,
        totalRevenue: {
          $sum: "$total_amount"
        },
        transactionCount: {
          $sum: 1
        }
      }
    },
    {
      $project: {
        _id: 0,
        averageRevenuePerTransaction: {
          $divide: [
            "$totalRevenue",
            "$transactionCount"
          ]
        }
      }
    }
  ])
  ```

  > On groupe tout les elements, puis on calcule le revenu total et le nombre de transaction, puis on utilise $divide pour diviser le revenu total par le nombre de transactions, ce qui nous donne le revenu moyen par transaction

- Nombre de clients uniques :

  Comptez le nombre de clients uniques ayant effectué au moins une transaction.

  ```
  db.sales.aggregate([
    {
      $group: {
        _id: "$customer_id"
      }
    },
    { $count: "uniqueCustomerCount" }
  ])

  ```

  > On groupe par customer_id, puis on fait un $count

- Répartition des ventes par magasin :

  Donnez la répartition des ventes pour chaque magasin.

  ```
  db.sales.aggregate([
    {
      $group: {
        _id: "$store_id",
        totalSales: {
          $sum: "$total_amount"
        },
        numberOfTransactions: {
          $sum: 1
        }
      }
    },
    {
      $project: {
        _id: 0,
        store_id: "$_id",
        totalSales: 1,
        numberOfTransactions: 1
      }
    }
  ])

  ```

  > On groupe par store_id et on calcule le total de ventes et les ventes par magasin, en suite on formate le resultat avec $project

- Écart type des montants des transactions :

  Calculez l'écart type des montants des transactions pour comprendre la variabilité des ventes.

  ```
  db.collection.aggregate([
    {
      $group: {
        _id: null,
        averageAmount: {
          $avg: "$total_amount"
        },
        standardDeviation: {
          $stdDevSamp: "$total_amount"
        }
      }
    }
  ])
  ```

  > On groupe les elements et on calcule la moyenne des montants avec $avg et on recupere l'écart type des montants des transactions avec $stdDevSamp
