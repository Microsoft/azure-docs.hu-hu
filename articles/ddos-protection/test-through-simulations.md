---
title: Azure DDoS Protection szimuláció tesztelése
description: Tudnivalók a szimulációk teszteléséről
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 4e6c6b441684d3877a8b85f40b650b257a5159da
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106071"
---
# <a name="test-through-simulations"></a>Tesztelés szimulációk használatával

Érdemes tesztelni a feltételezéseket arról, hogy a szolgáltatások hogyan reagálnak a támadásokra az időszakos szimulációk végrehajtásával. A tesztelés során ellenőrizze, hogy a szolgáltatások vagy alkalmazások továbbra is a várt módon működnek-e, és nincs-e fennakadás a felhasználói élményben. Azonosítsa a technológiai és a feldolgozási szempontból mutatkozó hiányosságokat, és foglalja bele őket a DDoS-válasz stratégiájába. Javasoljuk, hogy hajtsa végre az ilyen teszteket átmeneti környezetekben vagy nem csúcsidőben, hogy csökkentse az éles környezetre gyakorolt hatást.

Partnerkapcsolatra léptünk a [BreakingPoint Clouddal](https://www.ixiacom.com/products/breakingpoint-cloud), amely egy önkiszolgáló forgalomgenerátor, hogy létrehozzunk egy olyan felületet, ahol az Azure-ügyfelek a DDoS Protectionnel ellátott nyilvános végpontok forgalmát szimulálhatják. A szimulációt a következőkhöz használhatja:

- Megtapasztalhatja, hogy az Azure DDoS Protection hogyan segít az Azure-erőforrások DDoS-támadások elleni védelmében.
- Optimalizálhatja az incidensekre való válaszadást DDoS-támadás közben.
- Dokumentálhatja a DDoS-megfelelőséget.
- Betaníthatja a hálózati biztonsági csapatokat.

> [!NOTE]
> A BreakingPoint Cloud csak a nyilvános felhőben érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md) a védett nyilvános IP-címekkel.
- Először létre kell hoznia egy fiókot a [BreakingPoint-felhővel](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>DDoS-teszt támadás konfigurálása

1. Adja meg vagy válassza ki a következő értékeket, majd válassza a **teszt indítása** lehetőséget:

    |Beállítás        |Érték                                              |
    |---------      |---------                                          |
    |Cél IP-címe           | Adja meg a tesztelni kívánt nyilvános IP-cím egyikét.                     |
    |Portszám   | Adja meg a _443_ értéket.                       |
    |DDoS-profil | A lehetséges értékek a következők:,,,,,, `DNS Flood` `NTPv2 Flood` ,, `SSDP Flood` `TCP SYN Flood` `UDP 64B Flood` `UDP 128B Flood` `UDP 256B Flood` `UDP 512B Flood` `UDP 1024B Flood` , `UDP 1514B Flood` , `UDP Fragmentation` , `UDP Memcached` .|
    |Teszt mérete       | A lehetséges értékek a következők:,, `100K pps, 50 Mbps and 4 source IPs` `200K pps, 100 Mbps and 8 source IPs` `400K pps, 200Mbps and 16 source IPs` , `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Teszt időtartama | A lehetséges értékek a következők:,, `10 Minutes` `15 Minutes` `20 Minutes` , `25 Minutes` , `30 Minutes` .|

Ekkor az alábbihoz hasonlónak kell megjelennie:

![DDoS Attack – szimulációs példa: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Figyelés és ellenőrzés

1. Jelentkezzen be, https://portal.azure.com és nyissa meg az előfizetését.
1. Válassza ki azt a nyilvános IP-címet, amelyen a támadást tesztelte.
1. A **Figyelés** területen kattintson a **Metrikák** elemre.
1. A **metrika** beállításnál válassza a _DDoS Attack vagy a not (nem_) lehetőséget.

Ha az erőforrás támadás alatt áll, látnia kell, hogy az érték **0** és **1** között változik, az alábbi képhez hasonlóan:

![DDoS Attack – szimulációs példa: portál](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint Cloud API-szkript

Ezzel az [API-szkripttel](https://aka.ms/ddosbreakingpoint) automatizálhatja a DDoS-teszteket, ha egyszer vagy a cron használatával futtatja a normál teszteket. Ez akkor hasznos, ha ellenőrizni szeretné, hogy a naplózás megfelelően van-e konfigurálva, és hogy az észlelési és a reagálási eljárások érvényesek-e A szkriptek Linux operációs rendszerre (Ubuntu 18,04 LTS) és Python 3-ra van szükség. Telepítse az előfeltételeket és az API-ügyfelet a mellékelt parancsfájllal vagy a [BreakingPoint Cloud](http://breakingpoint.cloud/) webhelyén található dokumentáció használatával.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan tekintheti meg és konfigurálhatja a DDoS Protection telemetria](telemetry.md).
- Megtudhatja, hogyan [tekintheti meg és konfigurálhatja a DDoS diagnosztikai naplózást](diagnostic-logging.md).
- Ismerje meg, hogyan [vehet fel DDoS gyors választ](ddos-rapid-response.md).
