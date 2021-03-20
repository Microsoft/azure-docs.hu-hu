---
title: Azure-beli virtuális hubok védelme a Cloudguard-kapcsolódási ponttal
description: További tudnivalók a CloudGuard-hez való kapcsolódásról a biztonságos Azure-beli virtuális hubokhoz
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146858"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Virtuális központok védelme a Check Point Cloudguard Connecttel

A CloudGuard-kapcsolódási pont megbízható biztonsági partner a Azure Firewall Managerben. Védelmet nyújt a globálisan elosztott fiókirodák számára az internetre (B2I) vagy a Virtual Network to Internet (V2I) kapcsolatokra a fejlett veszélyforrások elleni megelőzéssel. 

A Azure Firewall Managerben egyszerű konfigurációval a CloudGuard-kapcsolat biztonsága (SECaaS) segítségével átirányíthatja a fiókirodát és a virtuális hálózati kapcsolatokat az internethez. A forgalom védelme a hub és az IPsec VPN-alagutakban lévő ellenőrzési pont Felhőbeli szolgáltatásával történik.

Ha engedélyezi az automatikus szinkronizálást a pipa-portálon, a Azure Portal *biztonságosként* megjelölt összes erőforrás automatikusan védett. Nem kell kétszer kezelnie az eszközöket. Egyszerűen csak egyszer kell megvédenie őket a Azure Portalban.

Az ellenőrzési pont egyetlen esernyővel egyesít több biztonsági szolgáltatást. A rendszer egyszer visszafejti az integrált biztonsági forgalmat, és egyetlen menetben ellenőrzi azokat. Az alkalmazások, az URL-szűrés és a tartalom ismerete (DLP) kikényszeríti a biztonságos webes használatot és az adatai védelmét. Az IP-címek és a víruskeresők védik a felhasználókat az ismert hálózati biztonsági rések ellen. A anti-bot blokkolja a parancs-és vezérlési kiszolgálókkal létesített kapcsolatokat, és riasztást küld, ha a gazdagép fertőzött.

A veszélyforrások emulációja (Sandboxing) megvédi a felhasználókat az ismeretlen és a nulla napi veszélyforrásokkal szemben. A pipa-alapú Zero-Day Protection egy felhőben üzemeltetett homok-boksz technológia, amelyben gyorsan karanténba helyezhetők és ellenőrizhetők a fájlok. Egy virtuális homokozóban fut, hogy felderítse a kártékony viselkedést, mielőtt belép a hálózatba. Ez megakadályozza a fenyegetések elleni fenyegetést, így a személyzet értékes időt takaríthat meg a fenyegetésekre való reagálás során. 

## <a name="deployment-example"></a>Üzembe helyezési példa

Tekintse meg a következő videót, amelyből megtudhatja, hogyan helyezheti üzembe a CloudGuard-hez való kapcsolódást megbízható Azure biztonsági partnerként.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>Következő lépések

- [Biztonságipartner-szolgáltató üzembe helyezése](deploy-trusted-security-partner.md)