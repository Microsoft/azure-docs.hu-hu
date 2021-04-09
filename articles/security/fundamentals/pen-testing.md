---
title: Behatolási teszt | Microsoft Docs
description: A cikk áttekintést nyújt a penetráció tesztelési folyamatáról, valamint az Azure-infrastruktúrában futó alkalmazáson belüli tollas tesztek végrehajtásáról.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550201"
---
# <a name="penetration-testing"></a>Behatolás tesztelése

Az Azure az alkalmazások teszteléséhez és üzembe helyezéséhez való használatának egyik előnye, hogy gyorsan lekérheti a környezetek létrehozását. Nem kell aggódnia az igényléssel, a beszerzéssel és a saját helyszíni hardverek üzemeltetésével és halmozásával kapcsolatban.

A környezetek gyors létrehozása nagyszerű – de még mindig meg kell győződnie arról, hogy a szokásos biztonsági átvilágítás szükséges. Az egyik legvalószínűbb, hogy a penetráció teszteli az Azure-ban üzembe helyezett alkalmazásokat.

Nem végzünk bevezetési tesztelést az alkalmazásában, de tisztában vagyunk vele, hogy a saját alkalmazásaiban szeretné elvégezni a tesztelést. Ez jó dolog, mert az alkalmazások biztonságának növelése révén biztonságosabbá teheti a teljes Azure-ökoszisztémát.

2017. június 15-től a Microsoft nem köti előzetes jóváhagyáshoz az Azure-erőforrások behatolástesztelésének elvégzését. Ez a folyamat csak Microsoft Azurehoz kapcsolódik, és nem alkalmazható más Microsoft Cloud szolgáltatásra.

>[!IMPORTANT]
>A Microsoft a toll-tesztelési tevékenységek bejelentése után már nem szükséges, hogy az ügyfeleknek továbbra is meg kell felelniük az összevonáshoz [Microsoft Cloud egységes penetráció tesztelési szabályainak](https://technet.microsoft.com/mt784683).

A végrehajtható szabványos tesztek a következők:

* Tesztek a végpontokon az [Open Web Application Security Project (OWASP) Top 10 biztonsági rések](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) felfedéséhez
* [Fuzz tesztelés](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) a végpontokon
* [Portkeresés](https://en.wikipedia.org/wiki/Port_scanner) a végpontokon

Az egyfajta tollas teszt nem hajtható végre, mert a [szolgáltatásmegtagadási (DOS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadások egyike. Ez a teszt magában foglalja a DoS-támadás kezdeményezését, vagy olyan kapcsolódó tesztek elvégzését, amelyek bármilyen típusú DoS-támadást meghatározhatnak, bemutatnak vagy szimulálnak.

>[!Note]
>A Microsoft a BreakingPoint-felhővel együttműködve olyan felületet hoz létre, amely a szimulációk számára DDoS Protection-kompatibilis nyilvános IP-címekkel való adatforgalmat is létrehoz. A BreakingPoint Cloud szimulációs szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [szimulációk tesztelését](../../ddos-protection/test-through-simulations.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések

* További információ az [engagement-tesztelési szabályokról](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
