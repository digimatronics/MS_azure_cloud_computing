---
title: "Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers SQL Database | Microsoft Docs"
description: "Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure, SSDT"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7ff52877-5b63-4adc-aa1a-689669a1146e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: e09c60111286681928ee1dd0b08fade7a102d6f2


---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Utilisation de SQL Server Data Tools pour Visual Studio afin de migrer une base de données SQL Server vers une base de données SQL Azure
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

Dans cet article, vous apprendrez à détecter et résoudre les problèmes de compatibilité de base de données SQL Server à l’aide deSQL Server Data Tools pour Visual Studio avant la migration vers une base de données SQL Azure.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Utilisation de SQL Server Data Tools pour Visual Studio
Utilisez SQL Server Data Tools pour Visual Studio (SSDT) pour importer le schéma de base de données dans un projet de base de données Visual Studio à des fins d’analyse. Pour l’analyse, vous devez désigner la plate-forme cible du projet en tant que base de données V12, puis réalisez le projet. Si la version est réussie, la base de données est compatible. Si la génération échoue, vous pouvez résoudre les erreurs dans SSDT (ou l’un des autres outils décrits dans cette rubrique). Une fois le projet correctement généré, il est republié en tant que copie de la base de données source. Vous pouvez ensuite utiliser la fonction de comparaison des données de SSDT pour copier les données de la base de données source vers la base de données compatible Azure SQL V12. Vous pouvez alors migrer cette base de données mise à jour. Pour utiliser cette option, téléchargez la [version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Schéma de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Si seule la migration de schéma est requise, vous pouvez publier le schéma directement depuis Visual Studio vers la base de données SQL Azure. Utilisez cette méthode lorsque le schéma de base de données nécessite un plus grand nombre de modifications qui peuvent être gérées par l’Assistant seul.
> 
> 

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Détection des problèmes de compatibilité à l’aide de SQL Server Data Tools pour Visual Studio
1. Ouvrez l’ **Explorateur d’objets SQL Server** dans Visual Studio. Utilisez l’option **Ajouter SQL Server** pour vous connecter à l’instance SQL Server qui contient la base de données en cours de migration. Localisez la base de données dans l’Explorateur d’objets, cliquez dessus avec le bouton droit, puis sélectionnez **Créer un projet...**     
   
   ![Nouveau projet](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
2. Configurez les paramètres d’importation sur la valeur **Importer uniquement les objets de portée application**. Décochez les options d’importation des éléments suivants : connexions référencées, autorisations et paramètres de base de données.    
   
   ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    
3. Cliquez sur **Démarrer** pour importer la base de données et créer le projet qui contient un fichier de script T-SQL pour chaque objet de cette base de données. Les fichiers de script sont imbriqués dans des dossiers au sein du projet.    
   
   ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    
4. Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur le projet de base de données, puis sélectionnez Propriétés. Sur la page **Paramètres du projet**, configurez le champ Plateforme cible sur Microsoft Azure SQL Database V12.    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
5. Cliquez avec le bouton droit sur le projet, puis sélectionnez **Générer** pour générer le projet.    
   
   ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
6. La **liste d’erreurs** affiche chaque incompatibilité. Dans ce cas, le nom d’utilisateur NT AUTHORITY\NETWORK SERVICE est incompatible. Vous pouvez donc le mettre en commentaire ou le supprimer (et gérer les conséquences de la suppression de cet identifiant et de ce rôle de la solution de base de données).     
   
   ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    

## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Résolution des problèmes de compatibilité à l’aide de SQL Server Data Tools pour Visual Studio
1. Double-cliquez sur le premier script pour l’ouvrir dans une fenêtre de requête, mettez-le en commentaire, puis exécutez le script.     
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)
2. Répétez ce processus pour chaque script contenant des incompatibilités jusqu’à résoudre toutes les erreurs.    
   ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
3. Lorsque la base de données ne contient plus d’erreurs, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**. Une copie de la base de données source est générée et publiée (il est vivement recommandé d’utiliser une copie, au moins au départ).     
   
   * Avant d’effectuer la publication, selon la version source de SQL Server (antérieure à SQL Server 2014), vous devrez peut-être réinitialiser la plateforme cible du projet cible pour activer le déploiement.     
   * Si vous effectuez la migration d’une base de données SQL Server de version antérieure, n’introduisez dans le projet aucune fonctionnalité non prise en charge par le système SQL Server source, sauf si vous migrez la base de données vers une version plus récente de SQL Server.     
     
     ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
     
     ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
4. Dans l’Explorateur d’objets SQL Server, cliquez avec le bouton droit sur votre base de données source, puis cliquez sur **Comparaison de données**. Comparer le projet à la base de données d’origine vous permet de comprendre quelles modifications ont été apportées par l’Assistant. Sélectionnez votre version Azure SQL V12 de la base de données, puis cliquez sur **Terminer**.    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    
5. Passez en revue les différences détectées, puis cliquez sur **Mettre à jour la cible** pour migrer les données de la base de données source dans la base de données Azure SQL V12.     
   
   ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
6. Choisissez un modèle de déploiement. Consultez [Migrer une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Fonctionnalités SQL Database](sql-database-features.md)
* [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


