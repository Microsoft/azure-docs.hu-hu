---
title: Cloud App Security-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Microsoft Cloud App Security-(MCAS-) összekötőt a riasztások továbbítására, illetve a MCAS-naplók Cloud Discoveryára az Azure Sentinelbe. 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 5c67f517ffd3704644fbe8041f3ac1e7b0d1f09c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904555"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Adatok összekapcsolásának Microsoft Cloud App Security 

A [Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) összekötővel riasztásokat és [Cloud Discovery naplókat](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) továbbíthat a MCAS-ből az Azure sentinelbe. Ez lehetővé teszi a felhőalapú alkalmazások láthatóságát, a kifinomult elemzéseket a előforduló kiberfenyegetésekkel kapcsolatban azonosításához és leküzdéséhez, valamint az adattovábbítások szabályozásához.

## <a name="prerequisites"></a>Előfeltételek

- A felhasználónak olvasási és írási engedélyekkel kell rendelkeznie a munkaterületen.
- A felhasználónak globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel kell rendelkeznie a munkaterület bérlője számára.
- Cloud Discovery naplók Azure Sentinelbe való továbbításához [engedélyezze az Azure sentinelt Siem-ként Microsoft Cloud app Securityban](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Cloud Discovery naplók betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Kapcsolódás Cloud App Securityhoz

Ha már rendelkezik Cloud App Security, győződjön meg arról, hogy az engedélyezve van a [hálózaton](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Ha Cloud App Security üzembe helyezése és az adatai betöltése történik, a riasztási adatot könnyen továbbíthatja az Azure Sentinelbe.


1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők**lehetőséget. Az összekötők listájában kattintson a **Microsoft Cloud app Security** csempére, majd a jobb alsó sarokban található **összekötő megnyitása lap** gombra.

1. Válassza ki, hogy mely naplókba kívánja továbbítani az Azure Sentinel alkalmazást; a **riasztások** és a **Cloud Discovery naplók** (előzetes verzió) közül választhat. 

1. Kattintson a **módosítások alkalmazása**lehetőségre.

1. Kiválaszthatja, hogy szeretné-e, hogy az Azure Defender-riasztások automatikusan előállítsák a Azure Security Center az Azure Sentinelben. Az **incidensek létrehozása**területen válassza az **engedélyezve** lehetőséget az alapértelmezett elemzési szabály bekapcsolásához, amely automatikusan létrehozza az incidenseket a riasztásokból. Ezt a szabályt az **elemzés**területen módosíthatja az  **aktív szabályok** lapon.

1. Ha Cloud App Security riasztásokhoz a Log Analytics vonatkozó sémát szeretné használni, írja be a következőt `SecurityAlert` a lekérdezési ablakba:. A Cloud Discovery naplók sémája mezőbe írja be a következőt: `McasShadowItReporting` .

> [!NOTE]
> Cloud Discovery segít a trendek észlelésében és azonosításában azáltal, hogy összesíti az adatokat használó felhasználók kapcsolatait a felhőalapú alkalmazásokkal.
>
> Mivel Cloud Discovery adatokat összesítenek napi rendszerességgel, vegye figyelembe, hogy a legfrissebb adatokat akár 24 órányi érték nem fogja tükrözni az Azure Sentinelben. Abban az esetben, ha egy alacsony szintű vizsgálat több azonnali adatra van szüksége, közvetlenül a forrás berendezésben vagy olyan szolgáltatásban kell elvégezni, ahol a nyers adat található.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Microsoft Cloud App Security az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerkedjen meg a fenyegetések észlelésével az Azure Sentinel használatával, [beépített](tutorial-detect-threats.md) vagy [Egyéni](tutorial-detect-threats-custom.md) szabályokkal.
