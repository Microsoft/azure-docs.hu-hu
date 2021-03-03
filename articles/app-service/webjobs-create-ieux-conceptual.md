---
title: Webjobs, háttérbeli feladatok az Azure-ban
description: További információ a webjobs-feladatokról.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1cb5e99558d6bf1a5baa21d05d45415855c61cb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745680"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>A webjobs-feladatok háttérben futtatják a Azure App Service

Ez a cikk bemutatja, hogyan telepíthet webjobs-feladatokat a [Azure Portal](https://portal.azure.com) használatával egy végrehajtható fájl vagy parancsfájl feltöltéséhez. További információ a webjobs-feladatok a Visual Studióval való fejlesztéséről és üzembe helyezéséről: [Webjobs-feladatok üzembe helyezése a Visual Studióval](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Áttekintés
A webjobs olyan [Azure app Service](index.yml) szolgáltatása, amely lehetővé teszi, hogy egy programot vagy parancsfájlt ugyanabban a példányban futtasson, mint egy webalkalmazást, egy API-alkalmazást vagy egy mobil alkalmazást. A webjobs használata nem jár további díjszabással.

> [!IMPORTANT]
> A webjobs-feladatok még nem támogatottak a Linux rendszeren való App Service.

A Azure WebJobs SDK a webjobs használatával számos programozási feladatot egyszerűsítheti. További információ: [Mi a Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

A Azure Functions egy másik módszert biztosít a programok és parancsfájlok futtatásához. A webjobs és a functions közötti összehasonlításért lásd: [választás a flow, a Logic apps, a functions és a webjobs között](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Webjobs-típusok

Az alábbi táblázat a *folyamatos* és az *aktivált* webjobs közötti különbségeket ismerteti.


|Folyamatos  |Kiváltott  |
|---------|---------|
| Azonnal elindul a Webjobs létrehozásakor. A feladatok befejezésének megtartásához a program vagy a szkript általában egy végtelen hurokon belül végzi a munkáját. Ha a feladattípus véget ért, újraindíthatja. | Csak akkor indul el, ha manuálisan vagy ütemezetten indítja el. |
| Minden olyan példányon fut, amelyen a webalkalmazás fut. Lehetősége van arra is, hogy a Webjobs egyetlen példányra korlátozza. |Egyetlen példányban fut, amelyet az Azure kiválaszt a terheléselosztáshoz.|
| Támogatja a távoli hibakeresést. | Nem támogatja a távoli hibakeresést.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Webjobs hozzáadása a verziókövetéshoz

Ha az alkalmazáshoz van beállítva verziókövetés, a webjobs-feladatokat a Verziókövetés integrálásának részeként kell telepíteni. Ha a verziókövetés az alkalmazással van konfigurálva, a Webjobs nem adható hozzá az Azure Portalon.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Támogatott fájltípusok parancsfájlokhoz vagy programokhoz

A rendszer a következő fájltípusokat támogatja:

* . cmd,. bat,. exe (a Windows cmd használatával)
* . ps1 (a PowerShell használatával)
* . sh (bash használatával)
* . php (PHP használatával)
* . a (Python használatával)
* . js (Node.js használatával)
* . jar (Java használatával)

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre webjobs](./webjobs-create-ieux.md)
* Webjobs-feladatok naplózási előzményeinek megtekintése] (./webjobs-Create-ieux-View-log.MD)