---
title: "Créer une collection et une base de données DocumentDB | Microsoft Docs"
description: "Apprenez à créer des bases de données NoSQL et des collections de documents JSON avec le portail du service en ligne pour Azure DocumentDB, base de données de documents basée sur le cloud. Essayez gratuitement dès aujourd’hui."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: f480b8155c7bee797f1fed0f80200eec500e95a2
ms.openlocfilehash: f8e0b05301b83223a7cb15d55be7001f3299213f


---
# <a name="how-to-create-a-documentdb-collection-and-database-using-the-azure-portal"></a>Guide pratique pour créer une base de données et une collection DocumentDB à l’aide du portail Azure
Pour utiliser Microsoft Azure DocumentDB, vous devez posséder un [compte DocumentDB](documentdb-create-account.md), une base de données, une collection et des documents. Cette rubrique montre comment créer une collection DocumentDB dans le portail Azure.

Vous ne savez pas vraiment ce qu’est une collection ? Consultez [Qu’est-ce qu’une collection DocumentDB ?](#what-is-a-documentdb-collection)

1. Dans le [portail Azure](https://portal.azure.com/), dans la barre de lancement, cliquez sur **DocumentDB (NoSQL)**, puis, dans le panneau **DocumentDB (NoSQL)**, sélectionnez le compte auquel ajouter une collection. Si vous n'avez aucun compte répertorié, vous devrez [créer un compte DocumentDB](documentdb-create-account.md).

   ![Capture d'écran montrant l'option Comptes DocumentDB dans la barre de lancement, le compte dans le panneau Comptes DocumentDB et la base de données dans le panneau Compte DocumentDB, dans le filtre Bases de données](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   Si l’option **DocumentDB (NoSQL)** n’est pas visible dans la barre de lancement, cliquez sur **Plus de services**, puis sur **DocumentDB (NoSQL)**. Si vous n'avez aucun compte répertorié, vous devrez [créer un compte DocumentDB](documentdb-create-account.md).
2. Dans le panneau **Compte DocumentDB** pour le compte sélectionné, cliquez sur **Ajouter une collection**.

    ![Capture d'écran montrant l'option Comptes DocumentDB dans la barre de lancement, le compte dans le panneau Comptes DocumentDB et la base de données dans le panneau Compte DocumentDB, dans le filtre Bases de données](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. Dans le panneau **Ajouter une collection**, entrez l’ID de votre nouvelle collection dans la zone **ID**. Les noms de collection doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?` ni d’espace de fin. Lorsque le nom est validé, une coche verte s'affiche dans la case ID.

    ![Capture d’écran montrant le bouton Ajouter la collection dans le panneau Base de données, les paramètres dans le panneau Ajouter la collection et le bouton OK. Portail Azure pour DocumentDB - Créateur de bases de données basées sur le cloud pour les bases de données JSON NoSQL.](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. Par défaut, **Niveau de tarification** est défini sur **Standard** afin que vous puissiez personnaliser le débit et le stockage de votre collection. Pour plus d’informations sur le niveau de tarification, consultez [Niveaux de performances dans DocumentDB](documentdb-performance-levels.md).  
5. Sélectionnez un **Mode de partitionnement** pour la collection : **Partition unique** ou **Partitionné**.

    Une **partition unique** possède une capacité de stockage réservée de 10 Go et peut atteindre des niveaux de débit compris entre 400 et 10 000 unités de requête par seconde (RU/s). Une unité de requête (RU) correspond au débit de lecture d’un document de 1 Ko. Pour plus d’informations sur les unités de requête, consultez [Unités de requête](documentdb-request-units.md).

    Une **collection partitionnée** peut être mise à l’échelle afin de gérer une quantité illimitée de stockage sur plusieurs partitions, avec des niveaux de débit à partir de 10 100 unités de requête par seconde. Dans le portail, le plus grand espace de stockage que vous pouvez réserver est de 250 Go, et le débit le plus élevé est 250 000 RU/s. Pour augmenter un quota, envoyez une demande, comme décrit dans [Demander l’augmentation des quotas de compte DocumentDB](documentdb-increase-limits.md). Pour plus d’informations sur les collections partitionnées, consultez [Collections à partition unique et collections partitionnées](documentdb-partition-data.md#single-partition-and-partitioned-collections).

    Par défaut, le débit pour une nouvelle collection à partition unique est défini sur 1 000 RU/s avec une capacité de stockage de 10 Go. Pour une collection partitionnée, le débit de la collection est défini sur 10100 RU/s avec une capacité de stockage de 250 Go. Une fois la collection créée, vous pouvez modifier le débit et le stockage de la collection.
6. Si vous créez une collection partitionnée, sélectionnez la **Clé de partition** pour la collection. Il est important de sélectionner la clé de partition correcte pour obtenir une collection performante. Pour plus d’informations sur la sélection d’une clé de partition, consultez [Conception du partitionnement](documentdb-partition-data.md#designing-for-partitioning).
7. Dans le panneau **Base de données**, créez une base de données ou utilisez une base de données existante. Les noms de base de données doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?` ni d’espace de fin. Pour valider le nom, cliquez en dehors de la zone de texte. Quand le nom est validé, une coche verte s’affiche dans la case.
8. Cliquez sur **OK** en bas de l'écran pour créer la nouvelle collection.
9. La nouvelle collection s’affiche maintenant dans le filtre **Collections** du panneau **Vue d’ensemble**.

    ![Capture d’écran montrant la nouvelle collection dans le panneau Base de données. Portail Azure pour DocumentDB - Créateur de bases de données basées sur le cloud pour les bases de données JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **Facultatif :** pour modifier le débit de la collection dans le portail, cliquez sur **Mettre à l’échelle** dans le menu Ressource.

    ![Capture d’écran du menu Ressource, avec sélection de l’option Mettre à l’échelle](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

## <a name="what-is-a-documentdb-collection"></a>Qu’est-ce qu’une collection DocumentDB ?
Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Une collection est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le débit approvisionné de la collection. Les collections peuvent couvrir une ou plusieurs partitions/serveurs et peuvent être mises à l’échelle pour gérer des volumes de stockage ou de débit quasi-illimités.

Les collections sont automatiquement partitionnées en un ou plusieurs serveurs physiques par DocumentDB. Lorsque vous créez une collection, vous pouvez spécifier le débit approvisionné en unités de demande par seconde et définir une propriété de clé de partition. La valeur de cette propriété sera utilisée par DocumentDB pour distribuer des documents entre les différentes partitions et acheminer les demandes à la manière de requêtes. La valeur de la clé de partition sert également de limite de transaction pour les procédures stockées et les déclencheurs. Chaque collection a un volume réservé de débit qui n’est pas partagé avec les autres collections du même compte. Vous pouvez donc étendre votre application aussi bien en termes de débit que de stockage.

Les collections diffèrent des tables dans les bases de données relationnelles. Les collections n’appliquent pas de schémas. En fait, DocumentDB n’en applique pas non plus, car il s’agit d’une base de données sans schéma. Par conséquent, vous pouvez stocker différents types de documents avec différents schémas dans la même collection. Vous pouvez choisir d’utiliser des collections pour stocker les objets d’un type unique, comme vous le feriez avec des tables. Le meilleur modèle dépend uniquement de la manière dont les données apparaissent ensemble dans les requêtes et les transactions.

## <a name="other-ways-to-create-a-documentdb-collection"></a>Autres méthodes de création d'une collection DocumentDB
Les collections ne doivent pas nécessairement être créées avec le portail. Vous pouvez également les créer avec les [SDK DocumentDB](documentdb-sdk-dotnet.md) et avec l’API REST.

* Pour obtenir un exemple de code C#, consultez [Exemples de collections C#](documentdb-dotnet-samples.md#collection-examples).
* Pour obtenir un exemple de code Node.js, consultez [Exemples de collections Node.js](documentdb-nodejs-samples.md#collection-examples).
* Pour obtenir un exemple de code Python, consultez [Exemples de collections Python](documentdb-python-samples.md#collection-examples).
* Pour obtenir un exemple d’API REST, consultez [Créer une collection](https://msdn.microsoft.com/library/azure/mt489078.aspx).

## <a name="troubleshooting"></a>Résolution de problèmes
Si **Ajouter une collection** est désactivé dans le portail Azure, cela signifie que votre compte est actuellement désactivé, ce qui se produit habituellement lorsque les crédits d’avantages du mois sont utilisés.    

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez une collection, l'étape suivante consiste à ajouter ou importer des documents dans la collection. Lorsqu'il s'agit d'ajouter des documents à une collection, vous avez le choix :

* Vous pouvez [ajouter des documents](documentdb-view-json-document-explorer.md) à l’aide de l’Explorateur de documents, dans le portail.
* Vous pouvez [importer des documents et des données](documentdb-import-data.md) à l'aide de l'outil de migration de données DocumentDB qui vous permet d'importer des fichiers JSON et CSV ainsi que des données depuis SQL Server, MongoDB, le stockage de table Azure et d’autres collections DocumentDB.
* Vous pouvez également ajouter des documents en vous aidant des [kits de développement logiciel (SDK) DocumentDB](documentdb-sdk-dotnet.md). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. Pour obtenir des exemples de code C# montrant comment utiliser des documents avec le SDK .NET DocumentDB, consultez les [exemples de documents C#](documentdb-dotnet-samples.md#document-examples). Pour obtenir des exemples de code Node.js montrant comment utiliser des documents avec le SDK Node.js DocumentDB, consultez les [exemples de documents Node.js](documentdb-nodejs-samples.md#document-examples).

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-sql-queries) sur vos documents à l’aide de [l’Explorateur de requête](documentdb-query-collections-query-explorer.md) du portail, de [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [SDK](documentdb-sdk-dotnet.md). 



<!--HONumber=Nov16_HO3-->


