---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244648"
---
> [!NOTE]
> Az `az webapp up` parancs a következő műveleteket hajtja végre:
>
>- Hozzon létre egy alapértelmezett [erőforráscsoportot](/cli/azure/group#az-group-create).
>
>- Hozzon létre egy alapértelmezett [app Service-csomagot](/cli/azure/appservice/plan#az-appservice-plan-create).
>
>- [Hozzon létre egy alkalmazást](/cli/azure/webapp#az-webapp-create) a megadott névvel.
>
>- A [zip telepíti](../articles/app-service/deploy-zip.md) a fájlokat az aktuális munkakönyvtárból az alkalmazásba.
>