---
title: Megfigyelő az Azure API Managementban | Microsoft Docs
description: Az Azure API Management összes megfigyelő lehetőségének áttekintése.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096737"
---
# <a name="observability-in-azure-api-management"></a>Megfigyelhető az Azure API Management

A megfigyelés lehetővé teszi a rendszer belső állapotának megismerését az általa előállított adatokból, és lehetővé teszi az adatok feltárását, hogy a mi történt és miért is válaszoljon kérdéseire. 

Az Azure API Management segítségével a szervezetek központosítják az összes API felügyeletét. Mivel az API-forgalom egyetlen belépési pontként szolgál, ideális hely az API-k megfigyelésére. 

## <a name="observability-tools"></a>Megfigyelési eszközök

Az alábbi táblázat összefoglalja az API-k megfigyeléséhez API Management által támogatott összes eszközt, amelyek közül egy vagy több forgatókönyv esetében hasznos:

| Eszköz        | Hasznos a következőhöz:    | Adatkésés | Megőrzés | Mintavételezés | Adattípus | Engedélyezve|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API-ellenőr](api-management-howto-api-inspector.md)** | Tesztelés és hibakeresés | Azonnali | Utolsó 100 nyomkövetés | Igény szerint bekapcsolva | Kérelmek nyomkövetése | Mindig
| **Beépített Analitika** | Jelentés és monitorozás | Percek | Élettartama | 100% | Jelentések és naplók | Mindig |
| **[Azure Monitor-metrikák](api-management-howto-use-azure-monitor.md)** | Jelentés és monitorozás | Percek | 93 nap (frissítés a kiterjesztésre) | 100% | Mérőszámok | Mindig |
| **[Azure Monitor-naplók](api-management-howto-use-azure-monitor.md)** | Jelentéskészítés, figyelés és hibakeresés | Percek | 31 nap/5 GB (frissítés a kiterjesztéshez) | 100% (állítható) | Naplók | Választható |
| **[Azure-Application Insights](api-management-howto-app-insights.md)** | Jelentéskészítés, figyelés és hibakeresés | Másodperc | 90 nap/5 GB (frissítés a kiterjesztéshez) | Egyéni | Naplók, metrikák | Választható |
| **[Naplózás az Azure Event hub használatával](api-management-howto-log-event-hubs.md)** | Egyéni forgatókönyvek | Másodperc | Felhasználó által felügyelt | Egyéni | Egyéni | Választható |

### <a name="self-hosted-gateway"></a>Saját üzemeltetésű átjáró

A fent említett eszközöket a felhőben a felügyelt átjáró támogatja. A [saját](self-hosted-gateway-overview.md) üzemeltetésű átjáró jelenleg nem küld Azure monitornak diagnosztikai naplókat. A helyi átjáró üzembe helyezésekor azonban helyileg is konfigurálhatja és megtarthatja a naplókat. További információkért lásd: [felhőalapú metrikák és naplók konfigurálása saját üzemeltetésű átjáróhoz](how-to-configure-cloud-metrics-logs.md) , valamint [helyi mérőszámok és naplók konfigurálása a saját üzemeltetésű átjáróhoz](how-to-configure-local-metrics-logs.md).

## <a name="next-steps"></a>Következő lépések

* [Kövesse az oktatóanyagokat, és ismerkedjen meg API Management](import-and-publish.md)
