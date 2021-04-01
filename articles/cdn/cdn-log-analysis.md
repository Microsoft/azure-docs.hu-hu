---
title: Az Azure CDN használati mintáinak elemzése
description: Ez a cikk a Azure CDN termékekhez elérhető elemzési jelentések különböző típusait ismerteti.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483988"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Az Azure CDN használati mintáinak elemzése

Miután engedélyezte a CDN-t az alkalmazáshoz, figyelheti a CDN használatát, ellenőrizheti a kézbesítés állapotát, és elháríthatja a lehetséges problémákat. A Azure CDN a következő módokon biztosítja ezeket a funkciókat: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Nyers naplók a Microsoft Azure CDN
Szabványos Microsoft-profillal engedélyezheti a nyers naplókat, és kiválaszthatja a naplók továbbítását a következőre:

* Azure Storage
* Event Hubs
* Azure Log Analytics

Az Azure Log Analytics megtekintheti a figyelési metrikákat, és beállíthatja a riasztásokat. 

További információ: [Azure CDN http nyers naplók](monitoring-and-access-log.md).


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Alapvető elemzések az Azure diagnosztikai naplóin keresztül

Az alapszintű Analitika minden díjszabási szinten elérhető a CDN-végpontokhoz. Az Azure Diagnostics-naplók lehetővé teszik az alapvető elemzési szolgáltatás exportálását az Azure Storage-ba, az Event hubokba vagy a Azure Monitor-naplókba. A Azure Monitor-naplók megoldást biztosítanak a felhasználó által konfigurálható és testreszabható gráfokkal. További információ az Azure diagnosztikai naplóiról: [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon Core-jelentések

**Azure CDN a** Verizon vagy **a prémium szintű Azure CDN a Verizon** -profilok alapszintű jelentéseket biztosítanak. Az alapvető jelentéseket a Verizon kiegészítő portálon tekintheti meg. A Verizon Core-jelentések a Azure Portal **kezelés** lehetőségével érhetők el, és különböző gráfokat és nézeteket biztosítanak. További információ: [a Verizon alapvető jelentései](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Egyéni Verizon-jelentések

**Azure CDN a** verizontól vagy **a prémium szintű Azure CDNtól a Verizon** -profiloktól származó, egyéni jelentéseket biztosít. Egyéni jelentéseket a Verizon kiegészítő portálon tekinthet meg. A Verizon egyéni jelentései a Azure Portal **kezelés** lehetőségével érhetők el. 

Az egyéni jelentések megjelenítik a találatok vagy az átvitt adatmennyiségek számát az egyes peremhálózati CNAME-rekordokban. Az adat a HTTP-válasz kódja vagy a gyorsítótár állapota időszakon belül van csoportosítva. További információ: [egyéni jelentések a verizonban](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Prémium szintű Azure CDN Verizon-jelentésekben

A **Verizon Azure CDN Premium** csomaggal a következő jelentések is elérhetők:
   * [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)
   * [Valós idejű statisztikák](cdn-real-time-stats.md)
   * [Azure CDN Edge-csomópont teljesítménye](cdn-edge-performance.md)

## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan használhatók a Azure CDN elemzési jelentéseinek különböző lehetőségei.

A Azure CDNről és a jelen cikkben említett egyéb Azure-szolgáltatásokról további információt a következő témakörben talál:

* [Mi az Azure CDN?](cdn-overview.md)
* [HTTP nyers naplók Azure CDN](monitoring-and-access-log.md)
