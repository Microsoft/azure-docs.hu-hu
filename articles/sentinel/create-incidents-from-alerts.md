---
title: Incidensek létrehozása a riasztásokból az Azure Sentinelben | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre incidenseket a riasztásokból az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 5c7c3d69bb26773171e9e0afc9f79ff25909a12a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99807292"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Incidensek automatikus létrehozása a Microsoft biztonsági értesítéseiből

Az Azure Sentinelhez csatlakozó Microsoft biztonsági megoldások, például a Microsoft Cloud App Security és a Microsoft Defender for Identity (korábbi nevén Azure ATP) által aktivált riasztások nem hoznak létre automatikusan incidenseket az Azure Sentinelben. Alapértelmezés szerint a Microsoft-megoldások az Azure Sentinelhez való összekapcsolásakor a szolgáltatásban létrehozott összes riasztást a rendszer az Azure Sentinel munkaterület biztonsági riasztások táblájában nyers adatként tárolja. Ezeket az adatlapokat használhatja, mint bármely más, az Azure Sentinel szolgáltatáshoz csatlakoztatott nyers adattal.

Az Azure Sentinel egyszerűen beállítható úgy, hogy a jelen cikk utasításait követve automatikusan hozzon létre incidenseket minden alkalommal, amikor egy csatlakoztatott Microsoft biztonsági megoldásban riasztást vált ki.

## <a name="prerequisites"></a>Előfeltételek
A biztonsági szolgáltatásokkal kapcsolatos riasztások engedélyezéséhez [kapcsolódnia kell a Microsoft biztonsági megoldásaihoz](connect-data-sources.md#data-connection-methods) .

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>A Microsoft biztonsági incidensek létrehozási elemzési szabályainak használata

Az Azure Sentinel beépített szabályainak használatával kiválaszthatja, hogy mely csatlakoztatott Microsoft biztonsági megoldások legyenek automatikusan létrehozva az Azure Sentinel-incidenseket valós időben. A szabályokat szerkesztheti úgy is, hogy a Microsoft biztonsági megoldás által generált riasztások alapján a szűrést lehetővé tegye, hogy az Azure Sentinelben incidenseket hozzon létre. Például úgy is dönthet, hogy az Azure Sentinel-incidenseket automatikusan csak a nagy súlyosságú Azure Defender (korábban Azure Security Center) riasztásokból hozza létre.

1. Az Azure Sentinel alatti Azure Portal válassza az **elemzés** lehetőséget.

1. Az összes beépített elemzési szabály megjelenítéséhez kattintson a **szabály sablonok** lapfülre.

    ![Szabályok sablonjai](media/incidents-from-alerts/rule-templates.png)

1. Válassza ki a használni kívánt **Microsoft Security** Analytics-szabály sablonját, és kattintson a **szabály létrehozása** elemre.

    ![Biztonsági elemzési szabály](media/incidents-from-alerts/security-analytics-rule.png)

1. Módosíthatja a szabály részleteit, és kiválaszthatja azokat a riasztásokat, amelyek a riasztás súlyossága vagy a riasztás nevében szereplő szöveg alapján hoznak létre incidenseket.  
      
    Ha például az **Azure Defender** (továbbra is a *Azure Security Center*) lehetőséget választja a **Microsoft biztonsági szolgáltatás** mezőjében, és a **szűrés súlyossága** mezőben a **magas** értékre kattint, akkor csak a magas súlyosságú Azure Defender-riasztások automatikusan létrehoznak incidenseket az Azure sentinelben.  

    ![Szabály létrehozása varázsló](media/incidents-from-alerts/create-rule-wizard.png)

1. Létrehozhat egy új **Microsoft biztonsági** szabályt is, amely különböző Microsoft biztonsági szolgáltatásokból származó riasztásokat szűr a **Microsoft incidens-létrehozási szabály** **létrehozása** és kiválasztása lehetőségre kattintva.

    ![Incidens-létrehozási szabály](media/incidents-from-alerts/incident-creation-rule.png)

  **Microsoft biztonsági szolgáltatásbeli** típusoknál több **Microsoft biztonsági** elemzési szabály is létrehozható. Ez nem hoz létre duplikált incidenseket, mivel minden szabály szűrőként van használatban. Még ha egy riasztás több **Microsoft biztonsági** elemzési szabálynak is megfelel, egyetlen Azure Sentinel-eseményt hoz létre.

## <a name="enable-incident-generation-automatically-during-connection"></a>Az incidens létrehozásának automatikus engedélyezése a csatlakozás során
 Microsoft biztonsági megoldás összekapcsolásakor kiválaszthatja, hogy a biztonsági megoldás riasztásai automatikusan előállítanak-e incidenseket az Azure Sentinelben.

1. Microsoft biztonsági megoldás adatforrásának összekötése. 

   ![Biztonsági incidensek előállítása](media/incidents-from-alerts/generate-security-incidents.png)

1. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett elemzési szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok** területen módosíthatja.

## <a name="next-steps"></a>Következő lépések

- Az Azure Sentinel megkezdéséhez szüksége lesz egy előfizetésre Microsoft Azure. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan hozhatja be [adatait az Azure sentinelbe](quickstart-onboard.md), és hogyan tekintheti [meg az adatait és a lehetséges fenyegetéseket](quickstart-get-visibility.md).
