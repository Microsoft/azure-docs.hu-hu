---
title: Az Azure Sentinel biztonsági biztonságossá tétele Microsoft Docs
description: Megtudhatja, hogyan használhatja az Azure Sentinel-be a biztonságos naplókat a biztonsággal szemben biztonságos adatösszekötő használatával. Megtekintheti a munkafüzetekbe való biztonságos adatvédelmet, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541162"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>A biztonság védelme az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A túl biztonságos biztonsági összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A biztonsági elővédelmen kívüli összekötővel egyszerűen csatlakoztathatja az összes biztonságos biztonsági megoldás naplóját az Azure Sentinelhez, az irányítópultok megtekintésére, egyéni riasztások létrehozására és a vizsgálat javítására. A biztonságos és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-besecure"></a>A Biztonság beállítása és összekapcsolása

a biztonságos integráció és a naplók exportálása közvetlenül az Azure Sentinelbe is megadható.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a biztonság megtartása **(előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Az alábbi lépéseket követve konfigurálhatja a beérkező ellenőrzési eredményeket, ellenőrizheti az állapotot és a naplózási naplókat az Azure Sentinel szolgáltatásban.

    **Hozzáférés az integrációs menühöz:**
    1. Kattintson a "további" menüpontra

    1. Kiszolgáló kiválasztása

    1. Integráció kiválasztása

    1. Az Azure Sentinel engedélyezése

    **BIZTONSÁGOS biztosítás az Azure Sentinel-beállításokkal:**

      Másolja a *munkaterület-azonosítót* és az *elsődleges kulcsot* az Azure Sentinel Connector oldaláról, ILLESSZE be őket a biztonságos konfigurációba, majd kattintson a **módosítás** gombra.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Munkaterület-azonosító és elsődleges kulcs}":::

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplók** területen, a **CustomLogs** szakaszban jelenik meg, az alábbi táblázatok közül egyet vagy többet:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Az elemzési szabályokban, a vadászati lekérdezésekben, a vizsgálatokban vagy az Azure Sentinelben bárhol máshol található, a megjelenő táblázatban szereplő nevek egyikének lekéréséhez a lekérdezési ablak tetején adja meg a fenti táblanév nevét.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése
Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
