---
title: Azure Service Fabric parancssori felület (sfctl) eltávolítási példaszkriptje
description: Alkalmazás eltávolítása az Azure Service Fabric fürtből az Azure Service Fabric parancssori felület használatával
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 47f0636437fb903d43c0aaa439bb41309b56dcc9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804416"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Alkalmazás eltávolítása egy Service Fabric fürtről

Ez a példaszkript törli a Service Fabric-alkalmazás egy éppen futó példányát, majd törli egy alkalmazástípus- és verzió regisztrációját a fürtről.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Ezt követően az alkalmazásfájlok is törlődnek a rendszerképtárolóból. 

Amennyiben szükséges, telepítse a [Service Fabric parancssori felületet](../service-fabric-cli.md).

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>További lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
