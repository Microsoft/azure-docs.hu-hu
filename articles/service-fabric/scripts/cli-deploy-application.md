---
title: Azure Service Fabric parancssori felület (sfctl) szkript-üzembehelyezési minta
description: Alkalmazás üzembe helyezése egy Azure Service Fabric-fürtön az Azure Service Fabric parancssori felület használatával
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/16/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: ff40dc62b4dcd622156a78518bbdcb6b9b430644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75526600"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Alkalmazás üzembe helyezése egy Service Fabric-fürtön a Service Fabric parancssori felület használatával

Ez a példaszkript egy alkalmazáscsomagot másol a fürt lemezképtárolójába, regisztrálja az alkalmazás típusát a fürtben, és létrehozza az alkalmazás egy példányát az alkalmazástípusból. Ekkor jönnek létre az alapértelmezett szolgáltatások is.

Amennyiben szükséges, telepítse a [Service Fabric parancssori felületet](../service-fabric-cli.md).

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Ha ezzel elkészült, a [remove](cli-remove-application.md) szkripttel eltávolíthatja az alkalmazást. A remove szkript eltávolítja az alkalmazáspéldányt, törli az alkalmazástípus regisztrációját, és eltávolítja az alkalmazáscsomagot a lemezképtárolóból.

## <a name="next-steps"></a>Következő lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
