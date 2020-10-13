---
title: Behatolási teszt | Microsoft Docs
description: A cikk áttekintést nyújt a penetráció tesztelési folyamatáról, valamint az Azure-infrastruktúrában futó alkalmazáson belüli tollas tesztek végrehajtásáról.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: dfacf124f8db0e5323c9abff56c4a78f85f6f014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816133"
---
# <a name="penetration-testing"></a>Behatolás tesztelése

Az Azure az alkalmazások teszteléséhez és üzembe helyezéséhez való használatának egyik előnye, hogy gyorsan lekérheti a környezetek létrehozását. Nem kell aggódnia az igényléssel, a beszerzéssel és a saját helyszíni hardverek üzemeltetésével és halmozásával kapcsolatban.

A környezetek gyors létrehozása nagyszerű – de még mindig meg kell győződnie arról, hogy a szokásos biztonsági átvilágítás szükséges. Az egyik legvalószínűbb, hogy a penetráció teszteli az Azure-ban üzembe helyezett alkalmazásokat.

Lehet, hogy már tudja, hogy a Microsoft [Az Azure-környezetének penetrációs tesztelését](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)végzi. Ez a teszt segít az Azure-beli fejlesztése terén.

Nem végzünk bevezetési tesztelést az alkalmazásában, de tisztában vagyunk vele, hogy a saját alkalmazásaiban szeretné elvégezni a tesztelést. Ez jó dolog, mert az alkalmazások biztonságának növelése révén biztonságosabbá teheti a teljes Azure-ökoszisztémát.

2017. június 15-től a Microsoft már nem igényel előzetes jóváhagyást az Azure-erőforrásokra vonatkozó behatolási teszt elvégzéséhez. Ez a folyamat csak Microsoft Azurehoz kapcsolódik, és nem alkalmazható más Microsoft Cloud szolgáltatásra.

>[!IMPORTANT]
>A Microsoft a toll-tesztelési tevékenységek bejelentése után már nem szükséges, hogy az ügyfeleknek továbbra is meg kell felelniük az összevonáshoz [Microsoft Cloud egységes penetráció tesztelési szabályainak](https://technet.microsoft.com/mt784683).

A végrehajtható szabványos tesztek a következők:

* Tesztek a végpontokon az [Open Web Application Security Project (OWASP) Top 10 biztonsági rések](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) felfedéséhez
* A végpontok [fuzz Testing tesztelése](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* A végpontok [portjának vizsgálata](https://en.wikipedia.org/wiki/Port_scanner)

Az egyfajta tollas teszt nem hajtható végre, mert a [szolgáltatásmegtagadási (DOS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadások egyike. Ez a teszt magában foglalja a DoS-támadás kezdeményezését, vagy olyan kapcsolódó tesztek elvégzését, amelyek bármilyen típusú DoS-támadást meghatározhatnak, bemutatnak vagy szimulálnak.

>[!Note]
>A Microsoft a BreakingPoint-felhővel együttműködve olyan felületet hoz létre, amely a szimulációk számára DDoS Protection-kompatibilis nyilvános IP-címekkel való adatforgalmat is létrehoz. További információ a töréspontok felhő-szimulációról: a [DDoS-észlelés ellenőrzése](../../virtual-network/manage-ddos-protection.md#validate-ddos-detection).

## <a name="next-steps"></a>További lépések

* További információ az [engagement-tesztelési szabályokról](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
