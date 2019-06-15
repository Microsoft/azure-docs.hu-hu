---
title: Tároló javaslatok az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan védheti meg a tárolókat az Azure Security Center javaslatok.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60705273"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Az Azure Security Center tároló kapcsolatos javaslatok megértése

A migrálás az monolit alkalmazások futtatása az alapvető fontosságú, tárolóalapú natív alkalmazások éles környezetben kihasználhatja a tárolók, beleértve az egyszerű és gyors üzembe helyezés és frissítés funkcióit. Üzembe helyezett tárolók száma továbbra is nő, ahogy biztonsági megoldásokat nyújt betekintést a tárolókat biztonsági állapotát, és azokat fenyegetésekkel szembeni hatékony védelmét biztosítani kell, kell.

Az Azure Security Center segítségével biztosíthatja a tárolókat az alábbi képességeket biztosítja:

- **IaaS-on üzemeltetett tárolók betekintést Linux rendszerű gépeken**<br>Az Azure Security Centerben a tárolók megjeleníti a docker használatával telepített összes virtuális gép. Ha a biztonsági problémák feltárása a virtuális gépen, a Security Center a gépet, mint például a Docker verziója és a gazdagépen futó képek száma a tárolókkal kapcsolatos további információkat biztosít.

    ![tároló lap](./media/security-center-container-recommendations/docker-recommendation.png)


- **Biztonsági javaslatok alapján CIS referenciaalap dockerhez**<br>A Security Center megvizsgálja a Docker-konfigurációkat, és egy listát készít az összes átvizsgált hibás szabályról, így láthatóvá teszi a konfigurációs hibákat. A Security Center segít a problémák gyors megoldásához, és ezzel időt takaríthat vonatkozó útmutatást tartalmazza. A Security Center folyamatosan értékeli a Docker-konfigurációkat, és tájékoztatja a legfrissebb állapotukról.

    ![tároló lap](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Valós idejű tároló fenyegetésészlelés**<br> A Security Center AuditD összetevővel Linuxos gépeken a tárolókhoz a valós idejű fenyegetésészlelés biztosít. A riasztások azonosítják a Docker gyanús tevékenységek például a gazdagépen, arra utalhat, hogy a Secure Shell (SSH) kiszolgáló belüli egy Docker-tárolót, vagy a titkosítási használja használatát egy emelt szintű tároló létrehozása. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

    ![tároló lap](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Javaslatok
Használja az alábbi táblázatokban referenciaként segítenek megérteni a rendelkezésre álló tárolók üzemeltetett IaaS Linuxos gépeken és a biztonsági értékelése a Docker-konfigurációját.

| Ajánlás | Leírás | Szervizelés |
| --- | --- | --- |
|A biztonsági rések tároló biztonsági konfigurációk |A biztonsági rések tároló biztonsági konfigurációk alapján konfigurálási eljárásoknak megfelelő beállításában.| A biztonsági rések a tároló biztonsági konfigurációk:<br>1. Tekintse át a nem teljesített szabályok listáját.<br>2. Javítsa ki a szabályban megadott utasítások szerint.|


## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.

