---
title: Frissítés az operációs rendszer verziója, az Azure Security Centerben |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **operációs rendszer verziójának frissítése**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905862"
---
# <a name="update-os-version-in-azure-security-center"></a>Az Azure Security Centerben az operációs rendszer verziójának frissítése
Virtuális gépek (VM), a cloud services Azure Security Center javasolni fogja, hogy az operációs rendszer (OS) frissíteni kell, ha van újabb verzió.  Csak a felhőszolgáltatások tárhelyek figyelt éles üzemben futó webes és feldolgozói szerepköröket.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
> 
> 

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **operációs rendszer verziójának frissítése**.
   ![Operációs rendszer verziójának frissítése][1]
2. Ekkor megnyílik a panel **operációs rendszer verziójának frissítése**. Kövesse a lépéseket követve frissítheti az operációs rendszer verzióját ezen a panelen.

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatta, hogyan valósíthat meg a Security Center javaslatait "Update OS version." Cloud services és a egy felhőszolgáltatás operációsrendszer-verzió frissítése kapcsolatos további információkért lásd:

* [A Cloud Services áttekintése](../cloud-services/cloud-services-choose-me.md)
* [Egy felhőalapú szolgáltatás frissítése](../cloud-services/cloud-services-update-azure-service.md)
* [A Cloud Services Konfigurálása](../cloud-services/cloud-services-how-to-configure-portal.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
