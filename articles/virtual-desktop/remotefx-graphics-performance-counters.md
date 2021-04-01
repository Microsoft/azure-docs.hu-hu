---
title: Grafikus teljesítménnyel kapcsolatos problémák diagnosztizálása Távoli asztal – Azure
description: Ez a cikk azt ismerteti, hogyan használhatók a távoli asztali protokoll munkameneteiben lévő távoli grafikus számlálók a Windows rendszerű virtuális asztalban található grafikákkal kapcsolatos teljesítményproblémák diagnosztizálásához.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc5c6499fa47a1e32a517032d5cc2a97b3f2677f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88007166"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>A grafikus teljesítménnyel kapcsolatos problémák diagnosztizálása a Távoli asztalban

A távoli munkamenetek élmény-minőségi problémáinak diagnosztizálásához a Teljesítményfigyelőben a távoli rendszerképek szakaszban található számlálók vannak megadva. Ebből a cikkből megtudhatja, hogyan erősítheti meg és javíthatja a grafikákkal kapcsolatos teljesítménybeli szűk keresztmetszeteket az ilyen számlálókat használó RDP protokoll (RDP) munkamenet

## <a name="find-your-remote-session-name"></a>Távoli munkamenet nevének megkeresése

A grafikus teljesítményszámlálók azonosításához szüksége lesz a távoli munkamenet nevére. Kövesse az ebben a szakaszban található utasításokat az egyes számlálók példányának azonosításához.

1. Nyissa meg a Windows-parancssort a távoli munkamenetből.
2. Futtassa a **qwinsta** parancsot, és keresse meg a munkamenet nevét.
    - Ha a munkamenet egy több munkamenetből álló virtuális gépen fut (VM): az egyes számlálók példánya azonos számú utótaggal rendelkezik, mint a munkamenet neve, például "RDP-TCP 37".
    - Ha a munkamenet egy olyan virtuális GÉPEN fut, amely támogatja a virtuális grafikus processzorokat (vGPU): az egyes számlálók példányát a rendszer a virtuális gép helyett a kiszolgálón tárolja. A számláló példányai között szerepel a virtuális gép neve a munkamenet neve helyett, például "Win8 Enterprise VM".

>[!NOTE]
> Míg a számlálók távolról is szerepelnek a nevükben, a távoli asztali grafikákat is tartalmazzák a vGPU-forgatókönyvekben.

## <a name="access-performance-counters"></a>Elérési teljesítményszámlálók

Miután meghatározta a távoli munkamenet nevét, kövesse az alábbi utasításokat, hogy összegyűjtse a távoli munkamenethez tartozó távoli grafikus teljesítményszámlálókat.

1. Válassza a  >  **felügyeleti eszközök**  >  **Teljesítményfigyelő** indítása elemet.
2. A **Teljesítményfigyelő** párbeszédpanelen bontsa ki a **figyelési eszközök** elemet, válassza a **Teljesítményfigyelő** lehetőséget, majd kattintson a **Hozzáadás** gombra.
3. A **számlálók hozzáadása** párbeszédpanelen, a **rendelkezésre álló számlálók** listából bontsa ki a távoli rendszerképekhez tartozó szakaszt.
4. Válassza ki a figyelni kívánt számlálókat.
5. A **kiválasztott objektumok** listájának példányai listában válassza ki a kijelölt számlálók számára figyelni kívánt példányokat, majd válassza a **Hozzáadás** lehetőséget. Az összes rendelkezésre álló számláló-példány kiválasztásához válassza a **minden példány** lehetőséget.
6. A számlálók hozzáadása után kattintson **az OK gombra**.

A kiválasztott teljesítményszámlálók megjelennek a Teljesítményfigyelő képernyőjén.

>[!NOTE]
>A gazdagép minden aktív munkamenetének saját példánya van az egyes teljesítményszámlálók számára.

## <a name="diagnose-issues"></a>Problémák diagnosztizálása

A grafikával kapcsolatos teljesítménnyel kapcsolatos problémák általában négy kategóriába sorolhatók:

- Alacsony képkockák száma
- Véletlenszerű stallek
- Magas bemeneti késés
- Gyenge keret minősége

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Alacsony képkocka-arány, véletlenszerű stallek és magas bemeneti késés kezelése

Először keresse meg a kimeneti képkockákat/a második számlálót. Az ügyfél számára elérhetővé tett keretek számát méri. Ha ez az érték kisebb, mint a bemeneti keret/második számláló, a keretek kimaradnak. A szűk keresztmetszetek azonosításához használja a képkockák kihagyott/második számlálóját.

A kereteknek három típusa van kihagyva/másodperc számláló:

- Kihagyott keretek/másodperc (nincs elegendő kiszolgálói erőforrás)
- Kihagyott keretek/másodperc (nincs elegendő hálózati erőforrás)
- Kihagyott keretek/másodperc (nincs elegendő ügyféloldali erőforrás)

A kihagyott keretek bármelyikének nagy értéke, a másodpercenkénti számlálók azt jelzik, hogy a probléma a Counter tracks erőforráshoz kapcsolódik. Ha például az ügyfél nem dekódol, és a képkockákat ugyanazon a sebességgel jeleníti meg, a kiszolgáló biztosítja a kereteket, a kihagyott keretek/másodperc (az ügyfél erőforrásai nem elégségesek) számláló magas lesz.

Ha a kimeneti keretek/második számláló megegyezik a bemeneti képkockákkal/a második számlálóval, de továbbra is szokatlan késést vagy leállást észlel, az átlagos kódolási idő lehet a bűnös. A kódolás egy szinkron folyamat, amely a kiszolgálón az egymunkamenetes (vGPU) forgatókönyvben és a többmunkamenetes forgatókönyvben lévő virtuális gépen történik. Az átlagos kódolási időtartamnak 33 MS alatt kell lennie. Ha az átlagos kódolási idő 33 MS alatt van, de továbbra is teljesítménnyel kapcsolatos problémái vannak, előfordulhat, hogy probléma van a használt alkalmazással vagy operációs rendszerrel.

Az alkalmazással kapcsolatos problémák diagnosztizálásával kapcsolatos további információkért lásd: [felhasználói bemeneti késleltetési](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)teljesítményszámlálók.

Mivel az RDP a 33 ms-os átlagos kódolási időt támogatja, a bemeneti képkockákat legfeljebb 30 képkocka/másodperc értékre támogatja. Vegye figyelembe, hogy az 33 MS a támogatott képkockák maximális száma. Sok esetben a felhasználó által tapasztalt Képkockasebesség alacsonyabb lesz, attól függően, hogy milyen gyakran biztosítanak keretet az RDP számára a forrás. Például a videók megtekintésére szolgáló feladatok esetében a teljes bemeneti képkockák száma 30 képkocka/másodperc, de a számítási keretek kevésbé nagy mennyiségű, például a dokumentum gyakori szerkesztésekor a bemeneti képkockáknál sokkal alacsonyabb értéket eredményeznek, ha a felhasználó a felhasználói élmény minőségét nem csökkenti.

### <a name="addressing-poor-frame-quality"></a>Gyenge képkockák minőségének kezelése

A keret minőségével kapcsolatos problémák diagnosztizálásához használja a frame Quality számlálót. Ez a számláló a forrás keret minőségének százalékában kifejezi a kimeneti keret minőségét. A minőség elvesztése a távoli elérés oka lehet, vagy a grafikus forrás velejárója lehet. Ha a távoli a minőségi adatvesztést okozta, a probléma oka lehet a hálózati vagy a kiszolgálói erőforrások hiánya, hogy jobb minőségű tartalmat küldjön.

## <a name="mitigation"></a>Kockázatcsökkentés

Ha a kiszolgáló erőforrásai a szűk keresztmetszetet okozzák, próbálkozzon az alábbi módszerek egyikével a teljesítmény javítása érdekében:

- Csökkentse a munkamenetek számát egy gazdagépen.
- Növelje a memória és a számítási erőforrások mennyiségét a kiszolgálón.
- Dobja el a kapcsolatok feloldását.

Ha a hálózati erőforrások a szűk keresztmetszetet okozzák, próbálja meg a következő módszerek egyikét a hálózati rendelkezésre állás növeléséhez a munkamenetek során:

- Csökkentse a munkamenetek számát egy gazdagépen.
- Használjon nagyobb sávszélességű hálózatot.
- Dobja el a kapcsolatok feloldását.

Ha az ügyfél erőforrásai a szűk keresztmetszetet okozzák, próbálkozzon az alábbi módszerek egyikével a teljesítmény javítása érdekében:

- Telepítse a legújabb Távoli asztal-ügyfelet.
- Növelje a memória és a számítási erőforrások mennyiségét az ügyfélszámítógépen.

> [!NOTE]
> Jelenleg nem támogatjuk a forrás-és a második számlálót. Egyelőre a forrás-és a második számláló mindig a 0 értéket fogja megjeleníteni.

## <a name="next-steps"></a>Következő lépések

- A GPU-ra optimalizált Azure-beli virtuális gépek létrehozásával kapcsolatban lásd: [Graphics Processing Unit (GPU) gyorsításának beállítása a Windows rendszerű virtuális asztali környezethez](configure-vm-gpu.md).
- A hibaelhárítási és eszkalációs sávok áttekintését lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows asztali környezet](environment-setup.md)című témakört.
