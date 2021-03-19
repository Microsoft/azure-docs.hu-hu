---
title: Azure-infrastruktúra rendelkezésre állása – Azure Security
description: Ez a cikk tájékoztatást nyújt arról, hogy a Microsoft mit tesz az Azure-infrastruktúra biztonságossá tételéhez, és az ügyfelek adatainak maximális rendelkezésre állását biztosítja.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "68727218"
---
# <a name="azure-infrastructure-availability"></a>Azure-infrastruktúra rendelkezésre állása
Ez a cikk tájékoztatást nyújt arról, hogy a Microsoft mit tesz az Azure-infrastruktúra biztonságossá tételéhez, és az ügyfelek adatainak maximális rendelkezésre állását biztosítja. Az Azure robusztus rendelkezésre állást biztosít a virtualizációs technológiával megvalósított széles körű redundancia alapján.

## <a name="temporary-outages-and-natural-disaster"></a>Átmeneti kimaradások és természeti katasztrófák
A Microsoft Cloud infrastruktúra-és üzemeltetési csapat tervez, épít, működtet és javítja a felhőalapú infrastruktúra biztonságát. Ez a csapat biztosítja, hogy az Azure-infrastruktúra magas rendelkezésre állást és megbízhatóságot, nagy hatékonyságot és intelligens méretezhetőséget biztosít. A csapat biztonságosabb, privát és megbízható felhőket biztosít.

A szünetmentes áramforrások és az akkumulátorok hatalmas bankjai biztosítják, hogy a villamos energia folyamatos maradjon, ha rövid távú áramkimaradás történik. A vészhelyzeti generátorok biztonsági mentési teljesítményt nyújtanak a hosszabb kimaradások és a tervezett karbantartás számára. Ha természeti katasztrófa következik be, az adatközpont a helyszíni üzemanyag-tartalékokat is használhatja.

A nagy sebességű és robusztus száloptikai hálózatok az adatközpontokat más nagyobb hubokkal és internetes felhasználókkal kötik össze. A számítási csomópontok a késés csökkentése, a Geo-redundancia biztosítása és a szolgáltatás általános rugalmasságának fokozása érdekében a munkaterheléseket közelebb hozzák a felhasználókhoz. A szolgáltatások folyamatos rendelkezésre állásának biztosítása érdekében a mérnökök csapata éjjel-nappal működik.

A Microsoft biztosítja a magas rendelkezésre állást a fejlett monitorozási és incidens-válaszokkal, a szolgáltatások támogatásával és a feladatátvételi képességgel kapcsolatban. A földrajzilag elosztott Microsoft Operations centerek 24/7/365-at működtetnek. Az Azure-hálózat az egyik legnagyobb a világon. A száloptika és a Content Distribution Network összekapcsolja az adatközpontokat és a peremhálózati csomópontokat a nagy teljesítmény és megbízhatóság biztosítása érdekében.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Az Azure két helyen tartja az adatvédelmet. Megadhatja a biztonsági mentési hely helyét. Az Azure mindkét helyen folyamatosan három kifogástalan adatreplikát tart fenn az adataiban.

## <a name="database-availability"></a>Adatbázisok rendelkezésre állása
Az Azure biztosítja, hogy az adatbázisok elérhetők legyenek az Internet-átjárón keresztül a tartós adatbázisok rendelkezésre állásával. A monitorozás az aktív adatbázisok állapotát és állapotát öt percenként méri.

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
Az Azure egy rugalmasan méretezhető és tartós tárolási szolgáltatáson keresztül biztosítja a tárterületet, amely csatlakozási végpontokat biztosít. Ez azt jelenti, hogy az alkalmazás közvetlenül is hozzáférhet a Storage szolgáltatáshoz. A tárolási szolgáltatás hatékonyan dolgozza fel a bejövő tárolási kérelmeket tranzakciós integritással.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy mit tesz a Microsoft az Azure-infrastruktúra biztonságossá tételéhez, tekintse meg a következőt:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
