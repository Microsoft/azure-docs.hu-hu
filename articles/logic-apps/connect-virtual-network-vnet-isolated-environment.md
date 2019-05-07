---
title: Azure virtuális hálózatok az Azure Logic Apps keresztül csatlakozhat egy integrációs service-környezet (ISE)
description: Hozzon létre egy integrációs service-környezet (ISE) logic apps és az integrációs fiókok hozzáférhet az Azure virtuális hálózatok (Vnetek), privát és nyilvános vagy "globális" Azure elkülönítve maradva háríthatják
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 8809a2fed5a44910e3a353d9dc5bc41ea964a1ce
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150556"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Csatlakozás az Azure virtuális hálózatok az Azure Logic Apps integrációs service-környezet (ISE) használatával

Forgatókönyvek, ahol a logic apps és az integrációs fiókok kell a hozzáférést egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md), hozzon létre egy [ *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Az ISE-ben olyan dedikált tárolási és más források vannak elkülönítve a nyilvános vagy "globális" Logic Apps szolgáltatást használó magán- és elkülönített környezet. Ez a fajta elkülönítés is csökkenti, amelyeket más Azure-bérlőt az alkalmazások teljesítményére hatással. Az ISE *beszúrta* be az Azure virtuális hálózathoz, amely azután telepíti a Logic Apps szolgáltatás a virtuális hálózatban. Amikor létrehoz egy logikai alkalmazásban vagy integrációs fiókot, válassza ki az ISE azok helyétől. A logikai alkalmazás vagy az integrációs fiók majd közvetlenül hozzáférhet a erőforrások, például a virtuális gépek (VM), kiszolgálók, rendszerek és szolgáltatások, a virtuális hálózaton.

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Állítsa be az Azure virtuális hálózat található portokat Forgalom utazhat az integrációs service-környezet (ISE) keresztül, a virtuális hálózat alhálózatai között.

* Az integrációs service-környezet (ISE) létrehozása.

* Hozzon létre egy logikai alkalmazást, amely képes futni az ISE-ben.

* A logic apps integrációs fiók létrehozása az ISE-ben.

Integrációs service Environment-környezetekkel kapcsolatos további információkért lásd: [Azure Logic Apps az Azure Virtual Network-erőforrásokhoz való hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

  > [!IMPORTANT]
  > A Logic apps beépített műveleteket és az ISE-ben futó összekötők használja egy másik díjszabási csomagot, nem a fogyasztás alapú díjszabással. További információkért lásd: [Logic Apps díjszabási](../logic-apps/logic-apps-pricing.md).

* Egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md). Ha nem rendelkezik virtuális hálózattal, megtudhatja, hogyan [egy Azure virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md). 

  * A virtuális hálózatnak rendelkeznie kell négy *üres* üzembe helyezéséhez és erőforrások létrehozása az ISE-ben alhálózat. Ezen alhálózatok előre hozhat létre, vagy megvárhatja, amíg nem hoz létre az ISE-ben, ahol létre alhálózatok egyszerre. Tudjon meg többet [alhálózati követelmények](#create-subnet). 
  
    > [!NOTE]
    > Ha [ExpressRoute](../expressroute/expressroute-introduction.md), amely biztosítja a privát kapcsolatot Microsoft-felhőszolgáltatásokhoz, be kell [hozzon létre egy útválasztási táblázatot](../virtual-network/manage-route-table.md) , amely rendelkezik a következő útvonal és a hivatkozás, hogy a táblázat minden egyes alhálózathoz, az ISE által használt:
    > 
    > **Név**: <*útvonal neve*><br>
    > **Címelőtag**: 0.0.0.0/0<br>
    > **A következő Ugrás**: Internet

  * Győződjön meg arról, hogy a virtuális hálózat [elérhetővé teszi ezeket a portokat](#ports) így megfelelően működik-e az ISE-ben, és elérhető marad.

* Ha egyéni DNS-kiszolgálókat az Azure virtual Network esetén használandó [ezen kiszolgálók beállítása a következő lépésekkel](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) az ISE-ben a virtuális hálózat üzembe helyezése előtt. Ellenkező esetben minden alkalommal, amikor módosítja a DNS-kiszolgáló is újraindítani az ISE-ben, amely egy olyan funkció, amely az ISE-ben nyilvános előzetes verzióban érhető el.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Állítsa be a hálózati portok

Megfelelően működjön, és elérhető-e, az integráció service-környezet (ISE) kell rendelkeznie az adott portokon a virtuális hálózaton rendelkezésre álló. Ezeket a portokat bármelyike nem érhető el, előfordulhat, hogy az ISE-ben, amelyek működése leáll, ellenkező esetben férhet hozzá. Az ISE-ben a virtuális hálózatban való használatakor gyakori telepítési problémát tapasztalja egy vagy több letiltott portot. Az ISE-ben és a cél rendszer közötti kapcsolatok esetén használja az összekötő is szükség lehet a saját port követelményei. Például ha az FTP-összekötő használatával kommunikálnak az FTP-rendszerek, győződjön meg arról, a portot használja, hogy elérhető legyen-e az FTP-rendszer 21-es porton, a Parancsküldés, például.

A forgalom szabályozása, amelyen központi telepítését az ISE-ben a virtuális hálózat alhálózatainak között, beállíthatja [hálózati biztonsági csoportok](../virtual-network/security-overview.md) ezekhez az alhálózatokhoz tartozó [alhálózatok közötti hálózati forgalom szűrése](../virtual-network/tutorial-filter-network-traffic.md). Ezek a táblázatok ismertetik a portokat a virtuális hálózat, amely az ISE-ben, és ahol azokat a portokat használja beolvasása. A [Resource Manager-szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags) IP-címelőtagokat, amelyek segítenek a minimálisra összetettségét, amikor a biztonsági szabályok létrehozása egy csoportját jelöli.

> [!IMPORTANT]
> A belső kommunikáció belül az alhálózatokra az ISE-ben szükséges ezekhez az alhálózatokhoz belül minden portok megnyitását.

| Cél | Direction | Portok | Forrás-szolgáltatáscímke | Cél szolgáltatáscímkéje | Megjegyzések |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Az Azure Logic Apps-kommunikációt | Kimenő | 80 & 443 | VirtualNetwork | Internet | A külső szolgáltatás, amellyel kommunikál a Logic Apps szolgáltatás függ, hogy a port |
| Azure Active Directory | Kimenő | 80 & 443 | VirtualNetwork | AzureActiveDirectory | |
| Az Azure Storage-függőségek | Kimenő | 80 & 443 | VirtualNetwork | Storage | |
| Intersubnet kommunikáció | A bejövő és kimenő | 80 & 443 | VirtualNetwork | VirtualNetwork | Az alhálózatok közötti kommunikációhoz |
| Az Azure Logic Apps-kommunikációt | Bejövő | 443 | Internet  | VirtualNetwork | A számítógép vagy szolgáltatás, amely meghívja ezt bármilyen kérelem típusú trigger vagy a webhookot, amely létezik a logikai alkalmazás az IP-címe. Bezárásával vagy blokkolja ezt a portot megakadályozza, hogy a kérelemtriggerekkel rendelkező logikai alkalmazások HTTP-hívások.  |
| Logikai alkalmazás futtatási előzmények | Bejövő | 443 | Internet  | VirtualNetwork | A számítógép, amelyen megtekintheti a logikai alkalmazás IP-címét a futtatási előzmények. Bezárásával vagy blokkolja ezt a portot nem akadályozza meg a futtatási előzmények megtekintése, bár nem tekintheti meg a bemenetek és kimenetek, amelyek az egyes lépések futtatási előzmények. |
| Kapcsolat kezelése | Kimenő | 443 | VirtualNetwork  | Internet | |
| Diagnosztikai naplók és mérőszámok közzététele | Kimenő | 443 | VirtualNetwork  | AzureMonitor | |
| Az Azure Traffic Manager a kommunikáció | Bejövő | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps Designer – dinamikus tulajdonságai | Bejövő | 454 | Internet  | VirtualNetwork | Kérelmek származhatnak a Logic Apps [végpontot bejövő IP-címeket az adott régióban](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| App Service Management-függőség | Bejövő | 454 & 455 | AppServiceManagement | VirtualNetwork | |
| Összekötő üzembe helyezés | Bejövő | 454 & 3443 | Internet  | VirtualNetwork | Üzembe helyezése és összekötők frissítése szükséges. Bezárásával vagy blokkolja ezt a portot hatására ISE központi telepítés sikertelen lesz, és megakadályozza, hogy a összekötő frissítéseket és javításokat. |
| Az Azure SQL-függőség | Kimenő | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Kimenő | 1886 | VirtualNetwork | Internet | A Resource Health-közzététel állapota |
| Az API Management - felügyeleti végpont | Bejövő | 3443 | APIManagement  | VirtualNetwork | |
| Eseményközpont-szabályzat és a monitorozási ügynök a napló függőséget | Kimenő | 5672 | VirtualNetwork  | EventHub | |
| Az Azure Cache elérése a Redis-példány között szerepkör példányai | Bejövő <br>Kimenő | 6379-6383 | VirtualNetwork  | VirtualNetwork | Ezenkívül az ISE-ben használható az Azure Cache a Redis, meg kell nyitnia ezeket [kimenő és bejövő portokat a redis Cache – gyakori kérdések az Azure Cache ismertetett](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Bejövő | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Hozzon létre az ISE-ben

Az integrációs service-környezet (ISE) létrehozásához kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **erőforrás létrehozása**.
A keresőmezőbe írja be szűrőként "integrációs szolgáltatás környezet".

   ![Új erőforrás létrehozása](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Az integrációs Service-környezet létrehozása paneljén válassza **létrehozás**.

   ![Válassza a "Create"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. A környezetnek meg az alábbi adatokat, és válassza a **felülvizsgálat + létrehozása**, például:

   ![Adja meg a környezet részletei](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Igen | <*Azure-előfizetés-neve*> | Az Azure-előfizetés a környezet használata |
   | **Erőforráscsoport** | Igen | <*Azure-resource-group-name*> | Az Azure erőforráscsoport, ahol szeretné létrehozni a környezetet |
   | **Integráció Service-környezet neve** | Igen | <*environment-name*> | A környezet nevét |
   | **Hely** | Igen | <*Azure-datacenter-region*> | Az Azure-adatközpontrégiót használhatják az üzembe helyezés a környezet |
   | **Ha extra kapacitásra** | Igen | 0 és 10 | Az ISE-erőforrás használandó további feldolgozási egységek száma. Létrehozása után adja hozzá a kapacitás, lásd: [hozzáadása ISE kapacitás](#add-capacity). |
   | **Virtuális hálózat** | Igen | <*Azure-virtual-network-name*> | Az Azure virtuális hálózat, ahol szeretné a környezet betöltése, hogy a logic apps, a környezetben hozzáférhessen a virtuális hálózat. Ha nem rendelkezik egy hálózati [először hozza létre az Azure-beli virtuális hálózathoz](../virtual-network/quick-create-portal.md). <p>**Fontos**: Is *csak* észrevegye hajtható végre, ha a hoz létre az ISE-ben. |
   | **Alhálózatok** | Igen | <*subnet-resource-list*> | Az ISE-ben szükséges négy *üres* alhálózatok a környezetében az erőforrások létrehozásához. Minden egyes alhálózat létrehozásához [a táblázat alatti lépéseket követve](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Alhálózat létrehozása**

   Erőforrások létrehozása a környezetében, az ISE-ben kell négy *üres* alhálózatokra, amelyek bármely szolgáltatás nincs delegálva. 
   Ön *nem* módosítsa ezeket az alhálózat-címeket, a környezet létrehozása után. Minden alhálózati ezeknek a feltételeknek kell megfelelnie:

   * A neve, amely betűvel vagy aláhúzásjellel kezdődik, de nem rendelkezik a következő karaktereket: `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Használja a [Classless Inter-Domain Routing (CIDR) formátum](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) és a B osztály címtér.

   * Használja legalább egy `/27` címét a lemezterület-minden egyes alhálózatnak rendelkeznie kell 32 címet, mert a *minimális*. Példa:

     * `10.0.0.0/27` 32-címmel rendelkezik, mert az 2<sup>(32-27)</sup> 2<sup>5</sup> vagy 32.

     * `10.0.0.0/24` 256-címmel rendelkezik, mert az 2<sup>(32-24)</sup> 2<sup>8</sup> vagy 256.

     * `10.0.0.0/28` csak 16-címmel rendelkezik, és túl kicsi mert 2<sup>(32-28)</sup> 2<sup>4</sup> vagy 16.

     Címek kiszámítása kapcsolatos további információkért lásd: [IPv4 CIDR-blokkok](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Ha [ExpressRoute](../expressroute/expressroute-introduction.md), ne felejtse el [hozzon létre egy útválasztási táblázatot](../virtual-network/manage-route-table.md) , amely rendelkezik a következő útvonal és a hivatkozás, hogy a táblázat minden egyes alhálózathoz, az ISE által használt:

     **Név**: <*útvonal neve*><br>
     **Címelőtag**: 0.0.0.0/0<br>
     **A következő Ugrás**: Internet

   1. Alatt a **alhálózatok** menüben válassza ki **kezelés alhálózati konfigurációt**.

      ![Alhálózati konfiguráció kezelése](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Az a **alhálózatok** panelen válassza a **alhálózati**.

      ![Alhálózat hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Az a **alhálózat hozzáadása** panelen adja meg ezt az információt.

      * **Név**: Az alhálózat nevét
      * **Címtartomány (CIDR-blokk)**: Az alhálózati címtartományt a virtuális hálózat és a CIDR formátumban

      ![Adja hozzá az alhálózati adatokat](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Ha elkészült, válassza ki a **OK**.

   1. Ismételje meg ezeket a lépéseket három további alhálózatokat.

      > [!NOTE]
      > Próbál létrehozni az alhálózatok nem érvényes, ha az Azure Portalon egy üzenetet jelenít meg, de nem tiltja a folyamatot.

1. Miután az Azure sikeresen ellenőrzi az ISE-adatokat, válassza ki a **létrehozás**, például:

   ![Sikeres ellenőrzés után válassza a "Létrehozás"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure elkezdi a környezetben, de ez a folyamat üzembe helyezéséhez *előfordulhat, hogy* befejezése előtt két órán belül. 
   Ellenőrizze a telepítés állapota, az Azure eszköztárában válassza az értesítések ikont, amely megnyitja az értesítési panelen.

   ![Üzembe helyezési állapot ellenőrzése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Ha a központi telepítés sikeres befejezését követően az Azure ezt az értesítést jelenít meg:

   ![A telepítés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Egyéb esetben kövesse az Azure-portálutasítások az üzembe helyezés hibaelhárítása.

   > [!NOTE]
   > Ha üzembe helyezése nem sikerül, vagy törölje az ISE-ben, az Azure is igénybe vehet egy órával az alhálózatok kibocsátása előtt. Ez a késleltetés azt jelenti, hogy azt jelenti, hogy előfordulhat, hogy várnia kell, a másik ISE-ben ezekhez az alhálózatokhoz újrahasznosítása előtt. 
   >
   > Ha törli a virtuális hálózat, Azure általában az alhálózatok mentése közzététele előtt akár két órát vesz igénybe, de ez a művelet hosszabb ideig tarthat. 
   > Amikor a virtuális hálózatok, győződjön meg arról, hogy nincsenek erőforrások továbbra is csatlakozik. Lásd: [virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. A környezet megtekintéséhez válassza **erőforrás megnyitása** , ha az Azure nem automatikusan nyissa meg a környezet üzembe helyezés befejezése után.  

Alhálózatok létrehozásával kapcsolatos további információkért lásd: [hozzáadása egy virtuális hálózat alhálózatához](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Logikai alkalmazás létrehozása – ISE-ben

Az integrációs service-környezet (ISE) használó logikai alkalmazások létrehozása, kövesse a [Logic Apps-alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) , de a következő eltérésekkel: 

* Létrehozásakor a logikai alkalmazást, a a **hely** tulajdonság, válassza ki az ISE-ben a a **integrációs service-környezetek** részben, például:

  ![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Azonos beépített eseményindítók és műveletek, például a HTTP-n, a azonos ISE-ben, a logikai alkalmazás futtatásához, amelyek is használhatja. Az összekötők a **ISE** címkézését is az azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban.

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Miután az ISE szúr be egy Azure-beli virtuális hálózatban, az ISE-ben a logic apps közvetlenül hozzáférhet a virtuális hálózatban lévő erőforrásokra. Egy virtuális hálózathoz csatlakozó helyszíni rendszerekhez behelyezése egy ISE-ben, e hálózat, a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával: 

  * ISE-ben, hogy a rendszer, például az SQL Server-összekötő
  
  * HTTP-művelet 
  
  * Egyéni összekötő

  A helyszíni rendszerek, amelyek nem a virtuális hálózat, vagy nem rendelkezik ISE összekötők, először [beállítása a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Integrációs fiók létrehozása – ISE-ben

A logic apps integrációs service-környezet (ISE) az integrációs fiók használatához integrációs fiókhoz kell használnia a *ugyanabban a környezetben* a logic Apps. A Logic apps egy ISE-ben csak integrációs fiókok ugyanazt az ISE-ben is lehet hivatkozni. 

Hozzon létre egy ISE használó integrációs fiókból, kövesse a [integrációs fiókok létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kivételével a **hely** tulajdonság, a **integrációs service-környezetek**  szakasz jelenik meg. Ehelyett válassza ki az ISE-ben, nem pedig a régiót, például:

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Adja hozzá az ISE-kapacitás

Az ISE alapegység javította a kapacitást, így a kapacitás növelése érdekében van szükség, ha több skálázási egység is hozzáadhat. Automatikus méretezés, teljesítmény-mérőszámon alapuló, vagy egy további feldolgozási egységek száma alapján is. Ha úgy dönt, hogy az automatikus skálázás metrika alapján, különböző feltételek közül választhat, és adja meg a feltételeknek megfelelő küszöbértéket feltételeit.

1. Az Azure Portalon keresse meg az ISE-ben.

1. Tekintse át a használat és a teljesítmény-mérőszámok az ISE-ben, az ISE fő menüjében válassza **áttekintése**.

   ![Használati adatok megtekintése az ISE-ben](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Alatt állíthatja be az automatikus skálázás, **beállítások**válassza **horizontális felskálázása**. Az a **konfigurálása** lapra, majd **automatikus skálázás engedélyezése**.

   ![Az automatikus skálázás bekapcsolása](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. A **automatikus skálázási beállítás neve**, adja meg a beállítás nevét.

1. Az a **alapértelmezett** válassza vagy **skálázás metrika alapján** vagy **skálázás adott példányszámra**.

   * Ha úgy dönt, hogy példány-alapú, adja meg a feldolgozási egységek száma 0 és 10 közötti szélsőértékeket is beleértve.

   * Ha úgy dönt, hogy a metrika-alapú, kövesse az alábbi lépéseket:

     1. Az a **szabályok** válassza **egy szabály hozzáadásához**.

     1. Az a **skálázási szabályhoz** panelen állítsa be a feltételeket és a művelet érvénybe a szabály aktiválásakor.

     1. Ha elkészült, válassza ki a **Hozzáadás**.

1. Ha elkészült az automatikus méretezési beállításokkal való, a módosítások mentéséhez.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
