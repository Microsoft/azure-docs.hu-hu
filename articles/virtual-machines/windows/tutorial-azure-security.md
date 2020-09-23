---
title: Oktatóanyag – Windows rendszerű virtuális gépekhez használható Azure Security Center használata az Azure-ban
description: Ebben az oktatóanyagban megismeri az Azure Security Centert, és megtudhatja, hogyan védheti meg és teheti biztonságossá Windows rendszerű virtuális gépeit az Azure-ban.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 057cbd5d42eafaa693abc418de62c3ae03a6b995
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984593"
---
# <a name="tutorial-use-azure-security-center-to-monitor-windows-virtual-machines"></a>Oktatóanyag: Az Azure Security Center használata Windows rendszerű virtuális gépek megfigyeléséhez

Az Azure Security Center segítségével áttekintheti az Azure-erőforrása biztonsági intézkedéseit. A Security Center integrált biztonsági monitorozást biztosít. Képes egyébként észrevétlenül maradó fenyegetések észlelésére. Ebben az oktatóanyagban megismeri az Azure Security Centert, és a következőket sajátíthatja el:

> [!div class="checklist"]
> * Adatgyűjtés beállítása
> * Biztonsági szabályzatok beállítása
> * A konfiguráció állapotára vonatkozó problémák megtekintése és javítása
> * Észlelt fenyegetések áttekintése

## <a name="security-center-overview"></a>Security Center – Áttekintés

A Security Center azonosítja a potenciális virtuálisgép-konfigurálási problémákat és a célzott biztonsági fenyegetéseket. Ezek lehetnek olyan virtuális gépek, amelyekről hiányoznak hálózati biztonsági csoportok, nem titkosított lemezek, valamint találgatásos módszeren alapuló RDP-támadások. Az információ könnyen értelmezhető diagramokon jelenik meg a Security Center irányítópultján.

A Security Center irányítópultjának eléréséhez válassza a **Security Center** elemet az Azure Portal menüjében. Az irányítópulton megtekintheti az Azure-környezet biztonsági állapotát, megtalálja az aktuális javaslatok számát, és megtekintheti a fenyegetési riasztások aktuális állapotát. Az egyes magas szintű diagramokat kibontva további részleteket tekinthet meg.

![A Security Center irányítópultja](./media/tutorial-azure-security/asc-dash.png)

A Security Center az adatészlelésen túl javaslatokat is ad az észlelt problémák megoldására. Ha például egy virtuális gépet csatolt hálózati biztonsági csoport nélkül telepít, a Security Center megjelenít egy, az elvégezhető javítási lépéseket ismertető javaslatot. A Security Center környezetének elhagyása nélkül kaphat automatizált javításokat.  

![A képernyőképen a javaslatok oldal látható, amely tartalmazza az ajánlások leírását, erőforrását, állapotát és súlyosságát.](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Adatgyűjtés beállítása

Ha a virtuális gép biztonsági konfigurációjára kíváncsi, előbb be kell állítania a Security Center adatgyűjtését. Ennek során be kell kapcsolnia az adatgyűjtést, amely automatikusan telepíti a Microsoft Monitoring Agentet az előfizetés minden virtuális gépén.

1. A Security Center irányítópultján kattintson a **Biztonsági szabályzat** elemre, és válassza ki az előfizetését. 
2. Az **Automatikus kiépítés****Adatgyűjtés** pontjánál válassza a **Be** lehetőséget.
3. Az **Alapértelmezett munkaterület-konfiguráció** menüpont beállítása maradjon a **Security Center által létrehozott munkaterület(ek) használata (alapértelmezett)**.
4. A **Biztonsági eseményeknél** tartsa meg az **Általános** alapértelmezett beállítást.
4. Kattintson az oldal tetején lévő **Mentés** elemre. 

Ekkor a rendszer telepíti a Security Center adatgyűjtési ügynökét minden virtuális gépen, és megkezdődik az adatgyűjtés. 

## <a name="set-up-a-security-policy"></a>Biztonsági szabályzat beállítása

A biztonsági szabályzatok segítségével határozhatók meg azok az elemek, amelyekhez a Security Center adatokat gyűjt és javaslatokat tesz. Az Azure-erőforrások különböző halmazaira különböző biztonsági szabályzatokat alkalmazhat. Noha alapértelmezés szerint a rendszer az Azure-erőforrásokat az összes szabályzatelem szerint értékeli ki, az egyes szabályzatelemek kikapcsolhatók minden Azure-erőforráshoz, vagy egy adott erőforráscsoporthoz. Ha további információt szeretne megtudni a Security Center biztonsági szabályzatairól olvassa el a [biztonsági szabályzatok az Azure Security Centerben történő beállítását](../../security-center/tutorial-security-policy.md) ismertető részt. 

Ha biztonsági szabályzatot szeretne beállítani a teljes előfizetéshez:

1. A Security Center irányítópultján válassza a **Biztonsági szabályzat** lehetőséget, és válassza ki az előfizetését.
2. A **Biztonsági szabályzat** panelen válassza a **Biztonsági szabályzat** elemet. 
3. A **biztonsági házirend – biztonsági szabályzat** panelen kapcsolja be vagy kapcsolja ki azokat a szabályzatokat, amelyeket alkalmazni kíván az előfizetésre.
4. Ha végzett a beállítások megadásával, kattintson a panel tetején lévő **Mentés** elemre. 


![Egyedi szabályzat](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Virtuálisgép-konfiguráció állapotának megtekintése

Miután bekapcsolta az adatgyűjtést, és beállított egy biztonsági szabályzatot, a Security Center elkezd riasztásokat és javaslatokat küldeni. A virtuális gépek üzembe helyezésekor a rendszer telepíti az adatgyűjtési ügynököt. Ezután a Security Centerbe betöltődnek az új virtuális gépekhez tartozó adatok. A virtuális gép konfigurációs állapotára vonatkozó részletes adatokkal kapcsolatban [a virtuális gépek a Security Centerben történő védelmét](../../security-center/security-center-virtual-machine-protection.md) ismertető szakaszban olvashat bővebben. 

Ahogy gyűlnek az adatok, az egyes virtuális gépek erőforrás-állapotát és a kapcsolódó Azure-erőforrásokat a rendszer összesíti. Az információk egy könnyen értelmezhető diagramon jelennek meg. 

Az erőforrás állapotának megtekintése:

1.  A Security Center irányítópultjának **Megelőzés** területén válassza a **Compute** elemet. 
2.  A **Compute** panelen válassza a **Virtuális gépek és számítógépek** lehetőséget. Ebben a nézetben megtekintheti az összes virtuális gép konfigurációs állapotának összegzését.

![Compute-állapot](./media/tutorial-azure-security/compute-health.png)

A virtuális gépre vonatkozó összes javaslat megtekintéséhez válassza ki a virtuális gépet. A javaslatokról és a javításokról a jelen oktatóanyag következő szakaszában lesz részletesebben szó.

## <a name="remediate-configuration-issues"></a>Konfigurációs problémák javítása

Miután a Security Center elkezd feltöltődni konfigurációs adatokkal, a javaslatokat a rendszer a beállított biztonsági szabályzat alapján készíti el. Ha például egy virtuális gépet a társított hálózati biztonsági csoport nélkül állított be, a rendszer javasolja, hogy hozzon létre egyet. 

A javaslatok listájának megtekintése: 

1. A Security Center irányítópultján válassza a **Javaslatok** elemet.
2. Válasszon ki egy adott javaslatot. Megjelenik azon erőforrások listája, amelyekre vonatkozik a javaslat.
3. Egy javaslat alkalmazásához válasszon ki egy erőforrást. 
4. A javítási lépések végrehajtásához kövesse az utasításokat. 

A Security Center olyan végrehajtható lépéseket ajánl, amelyekkel a Security Center elhagyása nélkül kezelhet egy javaslatot. A következő példában a Security Center olyan hálózati biztonsági csoportot észlel, amelyhez tartozik egy korlátozás nélküli bejövő szabály. A javaslatok oldalán kiválaszthatja a **Bejövő szabályok szerkesztése** gombot. Megjelenik a szabály módosításához szükséges felhasználói felület. 

![Javaslatok](./media/tutorial-azure-security/remediation.png)

Ha a rendszer elvégzi a javaslatokhoz tartozó javításokat, megoldottként jelöli meg őket. 

## <a name="view-detected-threats"></a>Észlelt fenyegetések megtekintése

Az erőforrások konfigurációjára vonatkozó javaslatok mellett a Security Center megjelenít fenyegetésészlelési riasztásokat is. A biztonsági riasztások szolgáltatása az Azure-erőforrásokra leselkedő biztonsági fenyegetések felderítése érdekében összesíti az egyes virtuális gépekről gyűjtött adatokat, az Azure-beli hálózati naplókat és a csatlakoztatott partnermegoldásokat. Security Center veszélyforrások észlelési képességeivel kapcsolatos részletes információkért lásd: [Hogyan észleli a fenyegetéseket az Security Center?](../../security-center/security-center-alerts-overview.md#detect-threats)

A biztonsági riasztások funkciójának használatához a Security Center tarifacsomagját *Ingyenesről**Standardra* kell emelni. Erre a magasabb szintű díjszabásra való áttéréskor **ingyenes próbaverzió** érhető el. 

A tarifacsomag módosítása:  

1. A Security Center irányítópultján kattintson a **Biztonsági szabályzat** elemre, és válassza ki az előfizetését.
2. Válassza ki az **árképzési szintet**.
3. Válassza a **Standard** lehetőséget, majd kattintson a panel tetején lévő **Mentés** elemre.


Miután módosította a tarifacsomagot, a rendszer feltölti a biztonsági riasztások diagramját, amint biztonsági fenyegetéseket észlel.

![Biztonsági riasztások](./media/tutorial-azure-security/security-alerts.png)

Az információk megtekintéséhez válasszon ki egy riasztást. Megjelenik például a fenyegetés leírása, az észlelési idő, minden fenyegetési kísérlet, valamint a javasolt javítás. Az alábbi példában a rendszer egy találgatásos RDP-támadást észlelt 294 meghiúsult RDP-kísérlettel. A rendszer felkínál egy javasolt megoldást.

![RDP-támadás](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban beállította az Azure Security Centert, és áttekintette a virtuális gépek Security Centerben való kezelését. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adatgyűjtés beállítása
> * Biztonsági szabályzatok beállítása
> * A konfiguráció állapotára vonatkozó problémák megtekintése és javítása
> * Észlelt fenyegetések áttekintése

A következő oktatóanyagból megismerheti, hogyan telepíthet SQL&#92;IIS&#92;.NET-vermet Windows rendszerű virtuális gépeken.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET-verem](tutorial-iis-sql.md)
