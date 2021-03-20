---
title: Azure Service Fabric parancssori felület (sfctl) eltávolítási példaszkriptje
description: Alkalmazás eltávolítása az Azure Service Fabric fürtből az Azure Service Fabric parancssori felület használatával
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
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: fd5d20ed3f2cc41f4d7d51f06d4bc90a6afd22eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75526532"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Alkalmazás eltávolítása egy Service Fabric-fürtből a Service Fabric parancssori felület használatával

Ez a példaszkript törli a Service Fabric-alkalmazás egy éppen futó példányát, majd törli egy alkalmazástípus- és verzió regisztrációját a fürtről.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Ezt követően az alkalmazásfájlok is törlődnek a rendszerképtárolóból. 

Amennyiben szükséges, telepítse a [Service Fabric parancssori felületet](../service-fabric-cli.md).

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Következő lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
