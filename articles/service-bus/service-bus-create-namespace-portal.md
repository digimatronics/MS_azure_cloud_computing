---
title: Créer un espace de noms Service Bus à l’aide du Portail Azure | Microsoft Docs
description: Pour commencer à utiliser Service Bus, vous devez avoir un espace de noms. Voici comment en créer un à l’aide du Portail Azure.
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub

---
# Créer un espace de noms Service Bus à l’aide du Portail Azure
Un espace de noms est un conteneur commun pour tous les composants de messagerie. Plusieurs files d’attente et rubriques peuvent résider dans un seul espace de noms, et les espaces de noms servent souvent de conteneurs d’applications. Il existe actuellement deux façons différentes de créer un espace de noms Service Bus.

1. Portail Azure (cet article)
2. [Modèles Microsoft Azure Resource Manager][create-namespace-using-arm]

## Créer un espace de noms dans le Portail Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Félicitations ! Vous venez de créer un espace de noms de messagerie Service Bus.

## Étapes suivantes
Consultez les [exemples fournis dans notre référentiel GitHub](https://github.com/Azure-Samples/azure-servicebus-messaging-samples\]\[github-samples), qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Azure Service Bus.

[create-namespace-using-arm]: ../service-bus-messaging/service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_1005_2016-->