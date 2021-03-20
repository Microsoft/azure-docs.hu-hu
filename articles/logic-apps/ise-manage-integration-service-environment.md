---
title: Integrációs szolgáltatási környezetek kezelése Azure Logic Apps
description: A hálózati állapot és a Logic apps, kapcsolatok, egyéni összekötők és integrációs fiókok kezelése a Azure Logic Apps integrációs szolgáltatási környezetében (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 4df9543e5e747de640562b7e5be224e257e0cfd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92676104"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Az integrációs szolgáltatási környezetet (ISE) felügyelete az Azure Logic Appsben

Ez a cikk bemutatja, hogyan végezheti el az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)felügyeleti feladatait, például:

* Kezelheti az ISE-ban lévő erőforrásokat, például a Logic appst, a kapcsolatokat, az integrációs fiókokat és az összekötőket.

* Keresse meg az ISE hálózati állapotát.

* Adja hozzá a kapacitást, indítsa újra az ISE-t, vagy törölje az ISE-t, kövesse a jelen témakör lépéseit. Az összetevők az ISE-ben való hozzáadásával kapcsolatban lásd: összetevők [hozzáadása az integrációs szolgáltatási környezethez](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Az ISE megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A portál keresési mezőjébe írja be az "integrációs szolgáltatási környezetek" kifejezést, majd válassza az **integrációs szolgáltatási környezetek** lehetőséget.

   ![Integrációs szolgáltatási környezetek keresése](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Az eredmények listából válassza ki az integrációs szolgáltatási környezetet.

   ![Integrációs szolgáltatási környezet kiválasztása](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Folytassa a következő szakasszal a logikai alkalmazások, kapcsolatok, összekötők vagy integrációs fiókok kereséséhez az ISE-ben.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Hálózati állapot ellenõrzése

Az ISE menü **Beállítások** területén válassza a **hálózati állapot** elemet. Ezen a panelen látható az alhálózatok és a kimenő függőségek állapota az egyéb szolgáltatásokban.

![Hálózati állapot ellenõrzése](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> Ha az ISE hálózata sérült állapotba kerül, az ISE által használt belső App Service Environment (bevezető) nem Kifogástalan állapotba kerül. Ha a benyújtó nem kifogástalan állapotú, mint hét napja, a kiegészítő szolgáltatás fel van függesztve. Az állapot megoldásához keresse meg a virtuális hálózat beállításait. Javítsa ki a megtalált problémákat, majd indítsa újra az ISE-t. Ellenkező esetben, 90 nap elteltével a rendszer törli a felfüggesztett bekötést, és az ISE használhatatlanná válik. Ezért ügyeljen arra, hogy az ISE Kifogástalan állapotba kerüljön, hogy engedélyezze a szükséges forgalmat.
> 
> További információt az alábbi témakörökben talál:
>
> * [Azure App Service diagnosztika áttekintése](../app-service/overview-diagnostics.md)
> * [Azure App Service Environment üzenetek naplózása](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Logikai alkalmazások kezelése

Megtekintheti és kezelheti az ISE-ben található Logic apps-alkalmazásokat.

1. Az ISE menü **Beállítások** területén válassza a **logikai alkalmazások** elemet.

   ![Logikai alkalmazások megtekintése](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Ha el szeretné távolítani a már nem szükséges Logic apps-alkalmazásokat az ISE-ben, válassza ki a logikai alkalmazásokat, majd válassza a **Törlés** lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen** lehetőséget.

> [!NOTE]
> Ha töröl és újból létrehoz egy alárendelt logikai alkalmazást, újra kell mentenie a szülő logikai alkalmazást. Az újból létrehozott alárendelt alkalmazás eltérő metaadatokkal fog rendelkezni.
> Ha nem kívánja újra a szülő logikai alkalmazást a gyermek újbóli létrehozása után, a gyermek logikai alkalmazás hívásai sikertelenek lesznek, a "nem engedélyezett" hibaüzenettel. Ez a viselkedés a szülő-gyermek Logic apps-alkalmazásokra vonatkozik, például azokra, amelyek az integrációs fiókokban vagy az Azure functions szolgáltatásban használnak összetevőket.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API-kapcsolatok kezelése

Megtekintheti és kezelheti az ISE-ben futó logikai alkalmazások által létrehozott kapcsolatokat.

1. Az ISE menü **Beállítások** területén válassza az API- **kapcsolatok** elemet.

   ![API-kapcsolatok megtekintése](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Ha el szeretné távolítani a már nem szükséges kapcsolatokat az ISE-ben, válassza ki ezeket a kapcsolatokat, majd válassza a **Törlés** lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen** lehetőséget.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE-összekötők kezelése

Megtekintheti és kezelheti az ISE-ben üzembe helyezett API-összekötőket.

1. Az ISE menü **Beállítások** területén válassza a **felügyelt összekötők** lehetőséget.

   ![Felügyelt összekötők megtekintése](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Ha el szeretné távolítani az ISE-ben nem elérhető összekötőket, válassza ki az összekötőket, majd válassza a **Törlés** lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen** lehetőséget.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Egyéni összekötők kezelése

Megtekintheti és kezelheti az ISE-ben üzembe helyezett egyéni összekötőket.

1. Az ISE menü **Beállítások** területén válassza az **Egyéni összekötők** lehetőséget.

   ![Egyéni összekötők keresése](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Ha el szeretné távolítani az ISE-ben már nem szükséges egyéni összekötőket, válassza ki az összekötőket, majd válassza a **Törlés** lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen** lehetőséget.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Integrációs fiókok kezelése

1. Az ISE menü **Beállítások** területén válassza az **integrációs fiókok** elemet.

   ![Integrációs fiókok keresése](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Ha már nincs rá szükség az integrációs fiókok eltávolítására az ISE-ből, válassza ki ezeket az integrációs fiókokat, majd válassza a **Törlés** lehetőséget.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-kapacitás hozzáadása

A prémium ISE alapegység rögzített kapacitással rendelkezik, így ha több átviteli sebességre van szüksége, akkor akár a létrehozáskor, akár utána több skálázási egységet is hozzáadhat. A fejlesztői SKU nem tartalmazza a méretezési egységek hozzáadásának képességét.

1. A [Azure Portal](https://portal.azure.com)lépjen az ISE elemre.

1. Az ISE használati és teljesítmény-metrikáinak áttekintéséhez az ISE menüben válassza az **Áttekintés** lehetőséget.

   ![Az ISE használatának megtekintése](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. A **Beállítások** területen válassza a **felskálázás** lehetőséget. A **Konfigurálás** ablaktáblán válasszon a következő lehetőségek közül:

   * [**Manuális méretezés**](#manual-scale): méretezés a használni kívánt feldolgozási egységek számától függően.
   * [**Egyéni autoscale**](#custom-autoscale): méretezés teljesítmény-mérőszámok alapján, különböző feltételek kiválasztásával és a feltételek teljesítéséhez szükséges küszöbértékek megadásával.

   ![Képernyőkép, amely a "manuális méretezéssel" kiválasztva jeleníti meg a "felskálázás" lapot.](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Manuális méretezés

1. A **manuális méretezés** kiválasztása után a **további kapacitáshoz** válassza ki a használni kívánt skálázási egységek számát.

   ![Válassza ki a kívánt skálázási típust](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Egyéni méretezés

1. Miután kiválasztotta az **Egyéni autoskálázást**, az **autoskálázási beállítás neve** mezőben adja meg a beállítás nevét, és szükség esetén válassza ki azt az Azure-erőforráscsoportot, amelyben a beállítás tartozik.

   ![Adja meg az autoskálázási beállítás nevét, majd válassza az erőforráscsoport lehetőséget.](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Az **alapértelmezett feltétel beállításnál** válassza ki a **skála alapján mérőszámot** vagy **méretezést egy adott példányszámra**.

   * Ha a példány-alapú lehetőséget választja, adja meg a feldolgozási egységek számát, amely a 0 és 10 közötti érték.

   * Ha a metrika-alapú elemet választja, kövesse az alábbi lépéseket:

     1. A **szabályok** szakaszban válassza a **szabály hozzáadása** elemet.

     1. A **skálázási szabály** ablaktáblán állítsa be a feltételek és a művelet végrehajtását, ha a szabály eseményindítót végez.

     1. A **példányokra vonatkozó korlátokat** a következő értékek megadásával adhatja meg:

        * **Minimum**: a használandó feldolgozó egységek minimális száma
        * **Maximum**: a használni kívánt feldolgozási egységek maximális száma
        * **Alapértelmezett**: Ha bármilyen probléma merül fel az erőforrás-metrikák olvasása közben, és az aktuális kapacitás az alapértelmezett kapacitás alatt van, az automatikus skálázás az alapértelmezett számú feldolgozási egységre lesz kibővítve. Ha azonban az aktuális kapacitás meghaladja az alapértelmezett kapacitást, az automatikus skálázás nem méretezhető a-ben.

1. Egy másik feltétel hozzáadásához válassza a **skálázási feltétel hozzáadása** lehetőséget.

1. Ha elkészült az autoskálázási beállításokkal, mentse a módosításokat.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>ISE újraindítása

Ha módosítja a DNS-kiszolgáló vagy a DNS-kiszolgáló beállításait, újra kell indítania az ISE-t, hogy az ISE fel tudja venni a módosításokat. A prémium SKU ISE újraindítása nem eredményez leállást a redundancia és az újraindítást végző összetevők miatt. A fejlesztői SKU ISE azonban állásidőt tapasztal, mert nem létezik redundancia. További információ: [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-i.

1. A [Azure Portal](https://portal.azure.com)lépjen az ISE elemre.

1. Az ISE menüben válassza az **Áttekintés** lehetőséget. Az áttekintő eszköztáron **indítsa újra** a következőt:.

   ![Integrációs szolgáltatási környezet újraindítása](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>ISE törlése

Mielőtt töröl egy olyan ISE-t, amelyhez már nincs szüksége, vagy egy olyan Azure-erőforráscsoport, amely egy ISE-t tartalmaz, győződjön meg arról, hogy nincsenek szabályzatok vagy zárolások az Azure-erőforráscsoport számára, amely tartalmazza ezeket az erőforrásokat vagy az Azure Virtual Network szolgáltatást, mert ezek az elemek letilthatják a törlést.

Az ISE törlése után előfordulhat, hogy az Azure-beli virtuális hálózat vagy alhálózatok törlése előtt legfeljebb 9 órát kell várnia.

## <a name="next-steps"></a>Következő lépések

* [Erőforrás hozzáadása integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
