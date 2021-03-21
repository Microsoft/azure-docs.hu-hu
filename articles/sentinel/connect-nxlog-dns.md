---
title: NXLog Windows DNS-naplókhoz kapcsolódó adatnaplók összekötése az Azure Sentinel szolgáltatással Microsoft Docs
description: Megtudhatja, hogyan használhatja a NXLog DNS-naplók adatösszekötőjét a Windows DNS-események Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek Windows DNS-szolgáltatásait, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745985"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>A NXLog Windows DNS-naplók összekötése az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A NXLog DNS-naplók összekötője jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A [NXLog DNS-naplók](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) összekötővel valós időben egyszerűen exportálhatja az összes Windows DNS-kiszolgálói eseményt az Azure Sentinel szolgáltatásba, így betekintést nyerhet a tartománynév-kiszolgáló tevékenységére a szervezeten belül. Nagy teljesítményű [Windows esemény-nyomkövetés (ETW)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) szolgáltatást alkalmaz a naplózási és analitikai DNS-kiszolgálói események valós idejű összegyűjtéséhez, és támogatja az események dúsítását egyéni mezőkkel. A NXLog DNS-naplók és az Azure Sentinel közötti integrációt REST APIon keresztül könnyíti meg.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-nxlog-dns-logs"></a>NXLog DNS-naplók konfigurálása és összekapcsolása

A NXLog beállítható úgy, hogy JSON formátumban küldje el az eseményeket közvetlenül az Azure Sentinelnek.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** elemre, és válassza a **NXLog DNS-naplók** összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse a *NXLog felhasználói útmutató* integrációs témakörének részletes utasításait, [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) segítségével konfigurálja a továbbítást a REST API használatával.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplókban** az  **egyéni naplók** szakaszban, a *DNS_Logs_CL* táblában.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan használhatja a NXLog-t a Windows DNS-naplók Azure Sentinelbe való betöltésére. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
