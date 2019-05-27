---
title: A biztonságot az Azure Security Center javaslatokat |} A Microsoft Docs
description: " Ismerje meg a biztonsági szabályzatok és javaslatok az Azure Security Center használata a biztonsági támadások történésekről. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 60bb1c3b81ef990993a2ce659a2b189c9d8a0eba
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967971"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>A biztonságot az Azure Security Center-javaslatok
Jelentős biztonsági esemény esélyét csökkentheti biztonsági szabályzat konfigurálásával, majd az Azure Security Center által nyújtott ajánlások megvalósítása. Ez a cikk bemutatja, hogyan biztonsági szabályzatok és javaslatok használata a Security Centerben a biztonsági támadások történésekről. 

A Security Center automatikusan futtatja a folyamatos vizsgálatok elemzése az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges biztonsági vezérlők konfigurálásának folyamatán hoz létre. A Security Center javaslatait frissíti a 24 órán belül, a következő kivételekkel:

- Operációs rendszer biztonsági konfigurációs javaslatokat frissülnek 48 órán belül
- Az Endpoint Protection problémák javaslatai frissülnek 8 órán belül

## <a name="scenario"></a>Forgatókönyv
A forgatókönyv bemutatja, hogyan használhatja a Security Center csökkentik a biztonsági incidensek és a műveletek figyelése a Security Center javaslatait. A forgatókönyv használja a fiktív vállalat, a Contoso és a szerepkörök a Security Center megjelenő [tervezéséhez és útmutató](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Ebben a forgatókönyvben arra összpontosítunk, az alábbi szerepkörök:

![A forgatókönyv-szerepkörök](./media/security-center-using-recommendations/scenario-roles.png)

A Contoso nemrég áttelepített néhány, a helyszíni erőforrások az Azure-bA. A Contoso biztosítani szeretné, hogy erőforrások megfelelő védelméhez és a felhőbeli erőforrások sebezhetőség csökkentése.

## <a name="use-azure-security-center"></a>Az Azure Security Center használata
David, a Contoso a számítástechnikai biztonsági, úgy döntött, hogy az Azure Security Center biztonsági rések észlelése és a Contoso-előfizetések felvétele a Security Center már. 

A Security Center automatikusan elemzi a Contoso Azure-erőforrások biztonsági állapotát, és alkalmazza a alapértelmezett biztonsági szabályzatokat. A Security Center azonosítja a potenciális biztonsági réseket, amikor létrehozza **javaslatok** a vezérlőket, állítsa be a biztonsági szabályzat alapján. 

David fut az Azure Security standard szintű, a javaslatok és biztonsági funkciók rendelkezésre álló teljes csomag beolvasni az összes saját előfizetés. Jeff is támogatja az előkészítő minden a meglévő helyszíni kiszolgálók, amely még nincs áttelepítve a felhőbe, hogy ő kihasználhatják a Security Center hibrid között annak [Windows](quick-onboard-windows-computer.md) és [Linux](quick-onboard-linux-computer.md) a kiszolgálók.

Jeff a felhőbeli munkaterhelés tulajdonosával. Jeff felelős biztonsági ellenőrzések megfelelően a Contoso biztonsági házirendek alkalmazása. 

Jeff a következő feladatokat hajtja végre:

- Monitorozása a Security Center által biztosított biztonsági javaslatok
- Biztonsági javaslatok értékeli, és döntse el, ha azt kell alkalmazni, vagy hagyja figyelmen kívül
- Biztonsági javaslatok alkalmazása

### <a name="remediate-threats-using-recommendations"></a>Javaslatok használata elhárításban
Napi figyelési tevékenysége részeként a Jeff jelentkezik be az Azure-ba, és megnyílik a Security Center. 

1. Jeff a számítási feladatok előfizetések választja ki.

2. Jeff ellenőrzi annak **pontszám biztonságos** beolvasni a teljes kép az előfizetések, mennyire vannak biztonságban vannak, és azt látja, hogy ő pontszám 548-e.

3. Jeff rendelkezik dönthet arról, hogy mely ajánlások kezeléséhez először. Így Jeff biztonságos pontszám kattint, és akkor indul, milyen mértékben javítja a alapuló kezelésére [pontszám hatás biztonságos](security-center-secure-score.md).

4. Jeff vonása a sok csatlakoztatott virtuális gépek és kiszolgálók, mert Jeff úgy dönt, hogy arra koncentrálhasson, **számítási és az alkalmazások**.

5. Amikor rákattint a Jeff **számítási és az alkalmazások**, ő javaslatok listáját látja, és a biztonságos megfelelően kezeli őket hatás pontozása.

6. Jeff számos olyan internetkapcsolattal rendelkező virtuális gépeket, és a portok érhetők el, mert tudja, hogy egy támadó a kiszolgálók felett. Jeff használatát választja, így (**just-in-time VM access**) [biztonsági-center-just-a-time.md].

Jeff továbbra is a magas prioritású és a közepes prioritású javaslatok haladjon át, és a megvalósítás döntéseket. Az egyes javaslatok Jeff megvizsgálja a részletes adatokat a megadott tudni, hogy mely erőforrásokat érintő eseményekről a Security Center, milyen hatással van a biztonságos pontszám, milyen minden javaslat azt jelenti, és minden egyes probléma szervizelés lépései.

## <a name="conclusion"></a>Összegzés
Figyelési javaslatok a Security Center segítségével ki a biztonsági réseket, mielőtt a támadás akkor fordul elő. Hibajavítási javaslatok, amikor a biztonságos pontszám és a számítási feladatok biztonsági állapotáról javítása. A Security Center automatikusan felderíti az új erőforrások üzembe helyezése, valamint a biztonsági szabályzat alapján értékeli őket és az őket az új javaslatot is tartalmaz.


## <a name="next-steps"></a>További lépések
Ellenőrizze, hogy a monitorozási folyamat helyen, ahol rendszeresen ellenőrzi a javaslatok a Security Center így biztos lehet benne, az erőforrások biztonságának megőrzéséhez idővel rendelkezik.

Ebben a forgatókönyvben a biztonsági szabályzatok és javaslatok használata a Security Centerben a biztonsági támadások történésekről mutatott. Tekintse meg a [incidensmegoldási forgatókönyvhöz](security-center-incident-response.md) megtudhatja, hogyan lehet egy incidensmegoldási terv támadás megtörténése előtt.

Ismerje meg, hogyan háríthatja el a fenyegetéseket a [incidensmegoldási](security-center-incident-response.md).
