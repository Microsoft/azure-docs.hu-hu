---
title: Azure DDoS Protection funkciók
description: A Azure DDoS Protection funkcióinak megismerése
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 602bb98f2cdc8a96874eba8dadfa33f3267d19ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746558"
---
# <a name="azure-ddos-protection-standard-features"></a>Az Azure DDoS Protection Standard szolgáltatásai

A következő részekben a Azure DDoS Protection standard szolgáltatás fő funkcióit vázoljuk.

## <a name="always-on-traffic-monitoring"></a>Folyamatos forgalom figyelése

DDoS Protection standard figyeli a tényleges forgalom kihasználtságát, és folyamatosan összehasonlítja a DDoS-szabályzatban meghatározott küszöbértékekkel. Ha túllépi a forgalom küszöbértékét, a DDoS-megoldás automatikusan indul el. Ha a forgalom a küszöbértékek alatti értéket adja vissza, a rendszer leállítja a megoldást.

![Azure DDoS Protection standard enyhítés](./media/ddos-protection-overview/mitigation.png)

A kockázatcsökkentő folyamat során a védett erőforrásnak továbbított forgalmat a DDoS Protection szolgáltatás átirányítja, és számos ellenőrzést végeznek, például a következőket:

- Győződjön meg arról, hogy a csomagok megfelelnek az Internet-specifikációknak, és nem hibásak.
- Az ügyféllel együttműködve megállapíthatja, hogy a forgalom potenciálisan hamis csomag-e (pl.: SYN Auth vagy SYN cookie, vagy egy csomag eldobása a forrás számára az újraküldéshez).
- Díjszabás – korlátozza a csomagokat, ha nem hajtható végre más kényszerítési módszer.

A DDoS Protection elveszíti a támadási forgalmat, és továbbítja a fennmaradó forgalmat a kívánt célra. Az észlelt támadásokról az Azure Monitor metrikái néhány percen belül értesítést küldenek. Ha a naplózást DDoS Protection standard telemetria konfigurálja, a naplókat a jövőbeli elemzésekhez elérhető lehetőségek közül is megírhatja. DDoS Protection standard esetén a Azure Monitor metrikus adatokat 30 napig őrzi meg a rendszer.

## <a name="adaptive-real-time-tuning"></a>Adaptív valós idejű hangolás

A Azure DDoS Protection alapszintű szolgáltatás segíti az ügyfeleket, és megakadályozza a más ügyfelekkel szembeni hatásokat. Ha például egy szolgáltatás kiépítése egy olyan, a megbízható bejövő forgalomra jellemző kötetre vonatkozik, amely kisebb, mint az infrastruktúra-szintű DDoS Protection házirend *kiváltási aránya* , akkor előfordulhat, hogy az ügyfél erőforrásainak DDoS-támadása nem lesz észrevehetetlen. Általánosságban elmondható, hogy a legutóbbi támadások (például a többvektoros DDoS) összetettsége és a bérlők alkalmazásspecifikus viselkedése az ügyfelekre szabott, testreszabott védelmi szabályzatokat hívja meg. A szolgáltatás a következő két bepillantást hajtja végre:

- Az ügyfélen belüli (felhasználónkénti IP-) forgalom automatikus megtanulása a 3. és a 4. rétegben.

- A téves pozitív érték minimalizálása, figyelembe véve, hogy az Azure mérete lehetővé teszi, hogy jelentős mennyiségű forgalmat vegyen fel.

![A DDoS Protection standard működésének diagramja a "szabályzat létrehozása" körben](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetria, figyelés és riasztások

A DDoS Protection standard szintű telemetria [Azure monitoron](../azure-monitor/overview.md)keresztül teszi elérhetővé. A riasztásokat konfigurálhatja a DDoS Protection által használt Azure Monitor metrikák bármelyikéhez. A splunk (Azure Event Hubs), a Azure Monitor naplók és az Azure Storage szolgáltatással integrálhatja a naplózást a Azure Monitor Diagnostics felületén keresztül a speciális elemzésekhez.

### <a name="ddos-mitigation-policies"></a>DDoS-mérséklési szabályzatok

A Azure Portal válassza a   >  **metrikák** figyelése elemet. A **metrikák** ablaktáblán válassza ki az erőforráscsoportot, válasszon ki egy **nyilvános IP-cím** típusú erőforrás-típust, és válassza ki az Azure nyilvános IP-címét. A DDoS-metrikák az **elérhető metrikák** ablaktáblán láthatók.

DDoS Protection a standard három, a védett erőforrás minden nyilvános IP-címéhez (TCP SYN, TCP és UDP) tartozó, a DDoS-t engedélyező virtuális hálózatban található, három alapszintű kockázatcsökkentő szabályzatot alkalmaz. A szabályzat küszöbértékeit úgy tekintheti meg, ha kiválasztja a metrika **bejövő csomagjait a DDoS-mérséklés elindításához**.

![Elérhető metrikák és mérőszámok diagramja](./media/ddos-best-practices/image-7.png)

A házirend küszöbértékei automatikusan konfigurálva vannak a gépi tanuláson alapuló hálózati forgalom profilkészítése révén. A DDoS-megoldás csak akkor fordul elő a támadási IP-címeknél, ha túllépi a házirend küszöbértékét.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Egy IP-címhez tartozó metrika a DDoS-támadás alatt

Ha a nyilvános IP-cím támadás alatt áll, a rendszer a **DDoS-támadás alatt** lévő metrika értékét, vagy nem módosítja az 1 értéket, DDoS Protection a támadási forgalom mérséklését hajtja végre.

!["A DDoS-támadás vagy nem" mérőszám és diagram](./media/ddos-best-practices/image-8.png)

Javasoljuk, hogy riasztást állítson be ezen a metrikán. Ekkor értesítést kap, ha a nyilvános IP-címen aktív DDoS-megoldás történik.

További információ: [Azure DDoS Protection standard kezelése a Azure Portal használatával](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Webalkalmazási tűzfal erőforrás-támadásokhoz

Az alkalmazási rétegben az erőforrás-támadásokra adott esetben egy webalkalmazási tűzfalat (WAF) kell konfigurálnia a webalkalmazások biztonságossá tételéhez. A WAF ellenőrzi a bejövő webes forgalmat, hogy blokkolja az SQL-injektálásokat, a helyközi parancsfájlkezelést, a DDoS-t és a 7. rétegbeli támadásokat. Az Azure a [Application Gateway funkciójának](../web-application-firewall/ag/ag-overview.md) köszönhetően lehetővé teszi a webalkalmazások központosított védelmét a gyakori támadások és biztonsági rések révén. Az Azure-partnerek további WAF ajánlatokat is biztosítanak, amelyek az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)-en keresztül megfelelőbbek lehetnek az igényeihez.

Még a webalkalmazási tűzfalak is hajlamosak a térfogatmérő és az állami kimerültség elleni támadásokra. Javasoljuk, hogy a WAF virtuális hálózatban engedélyezze a DDoS Protection standard használatát a térfogatáram-és protokoll-támadások elleni védelem érdekében. További információ: [DDoS Protection hivatkozási architektúrák](ddos-protection-reference-architectures.md) szakasz.

## <a name="protection-planning"></a>Védelem tervezése

A tervezés és az előkészítés elengedhetetlen annak megismeréséhez, hogy a rendszer hogyan fog elvégezni a DDoS-támadások során. Az incidensek kezelésének megtervezése ennek a erőfeszítésnek a része.

Ha DDoS Protection standard szintű, győződjön meg arról, hogy az engedélyezve van az internetre irányuló végpontok virtuális hálózatán. A DDoS-riasztások konfigurálása segít folyamatosan figyelni az infrastruktúra esetleges támadásait. 

Az alkalmazások egymástól függetlenül figyelhetők. Egy alkalmazás normális viselkedésének megismerése. Felkészülés a műveletre, ha az alkalmazás a DDoS-támadás során nem a várt módon működik. 

Ismerje meg, hogyan reagálnak a szolgáltatások a támadásokra a [szimulációk tesztelésével](test-through-simulations.md).

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre DDoS Protection-tervet](manage-ddos-protection.md).
