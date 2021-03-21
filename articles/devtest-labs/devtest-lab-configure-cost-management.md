---
title: Tekintse meg a havi becsült tesztkörnyezet Azure DevTest Labs
description: Ez a cikk azt ismerteti, hogyan lehet nyomon követni a labor (havi becsült költség trend diagram) költségeit a Azure DevTest Labsban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6a2a9bef9e54ef7deda123aad34cf0c576fd158f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98892337"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Laborhoz kapcsolódó költségek nyomon követése Azure DevTest Labs
Ez a cikk a labor díjszabásának nyomon követésére vonatkozó információkat tartalmaz. Bemutatja, hogyan tekintheti meg a tesztkörnyezet aktuális naptári hónapjának becsült költségeit. A cikk azt is bemutatja, hogyan tekintheti meg az erőforrásokra vonatkozó havi költségeket a laborban.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Tekintse meg a havi becsült laboratóriumi költségek trendjét 
Ebben a szakaszban megtudhatja, hogyan használhatja a **havi becsült költség trend** diagramot az aktuális naptári hónap becsült költségének és az aktuális naptári hónapra vetített teljes havi költségének megtekintéséhez. Azt is megtudhatja, hogyan kezelheti a laborok költségeit úgy, hogy a kitűzött célok és a küszöbértékek megadásával kiváltja az eredményeket a DevTest Labs szolgáltatásban.

A havi becsült költség trend diagram megtekintéséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A Labs listából válassza ki a labort.  
4. Válassza a **konfiguráció és szabályzatok** lehetőséget a bal oldali menüben.  
4. A bal oldali menüben a **Cost Tracking** szakaszban válassza a **Cost trend** elemet. Az alábbi képernyőfelvételen egy példa látható a Cost diagramra. 
   
    ![Cost diagram](./media/devtest-lab-configure-cost-management/graph.png)

    A **becsült költségérték** az aktuális naptári hónap becsült költséghatékony értéke. A **tervezett költségek** a teljes aktuális naptári hónap becsült díja, amelyet az előző öt nap Lab-díja alapján számítunk fel.

    A rendszer a következő egész számra kerekíti a költségeket. Például: 

   * 5,01 legfeljebb 6 
   * 5,50 legfeljebb 6
   * 5,99 legfeljebb 6

     Ahogy az a diagram felett van, a diagramon alapértelmezés szerint megjelenő költségek [az utólagos elszámolású ajánlatok](https://azure.microsoft.com/offers/ms-azr-0003p/) díjszabása alapján *becsült* költségek. A diagramon megjelenő saját költségkeretek is megadhatók a [tesztkörnyezet költségekkel kapcsolatos céljainak kezelésével.](#managing-cost-targets-for-your-lab)

     A költségek kiszámítása *nem* tartalmazza a következő költségeket:

   * A CSP és a DreamSpark-előfizetések jelenleg nem támogatottak, mert a Azure DevTest Labs az Azure számlázási API-k használatával számítja ki a laboratóriumi költségeket, amely nem támogatja a CSP vagy a DreamSpark előfizetéseket.
   * Ajánlati díjak. Jelenleg nem használhatja a Microsoft vagy a Microsoft partnereivel egyeztetett ajánlati díjakat (az Ön előfizetése alatt látható). Csak utólagos elszámolású díjszabást használunk.
   * Saját adók
   * Kedvezmények
   * A számlázási pénznem. Jelenleg a tesztkörnyezet díja csak USD pénznemben jelenik meg.

### <a name="managing-cost-targets-for-your-lab"></a>A tesztkörnyezet díjszabásának kezelése
A DevTest Labs segítségével hatékonyabban kezelheti a laborban felmerülő költségeket, ha megtekinti a havi becsült költség trend diagramot. A DevTest Labs értesítéseket is küldhet, ha eléri a megadott megcélzott kiadásokat vagy küszöbértékeket. 

1. A **Cost trend** lapon válassza a **kezelés cél** lehetőséget.

    ![Cél kezelése gomb](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. A **cél kezelése** lapon határozza meg a kiadások célját és a küszöbértékeket. Azt is beállíthatja, hogy az egyes kiválasztott küszöbértékek a Cost trend diagramon vagy egy webhook-értesítésen keresztül legyenek jelentve.

    ![Cél ablaktábla kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Válassza ki azt az időszakot, amely alatt a költségeket nyomon szeretné követni.
      - **Havi**: a Cost-célok havonta vannak követve.
      - **Kijavítva**: a rendszer a költségeket a kezdő és a záró dátumokban megadott dátumtartomány szerint nyomon követi. Ezek az értékek általában azt jelzik, hogy a projekt mennyi ideig fut.
   - Határozza meg a **cél költségeit**. Például azt, hogy mennyit kíván költeni a laborra a megadott időszakban.
   - Ezzel a beállítással engedélyezheti vagy letilthatja a jelentett küszöbértékeket – 25%-os növekmények esetén, a megadott **célzott díj** akár 125%-ában.
      - **Értesítés**: ha eléri ezt a küszöbértéket, a rendszer értesítést küld a megadott webhook URL-címéről.
      - **Ábra a diagramon**: Ha ezt a küszöbértéket teljesíti, az eredményeket a megtekinthető trend Graph diagramon láthatja el, ahogyan a a havi becsült költség trend chart megtekintése című témakörben leírtak szerint.
   - Ha úgy dönt, hogy a küszöbérték teljesülése esetén **értesítést kap** , meg kell adnia egy webhook URL-címét. Az Integration (Cost Integration) területen válassza a **kattintson ide az integráció hozzáadásához**. Írja be a **webhook URL-címét** az értesítés konfigurálása ablaktáblában, majd kattintson **az OK gombra**.

       ![Értesítési ablaktábla konfigurálása](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Ha megadja az **értesítéseket**, meg kell adnia egy webhook URL-címét.
     - Hasonlóképpen, ha megad egy webhook URL-címet, be kell állítania az **értesítéseket** **a be** értékre a Cost küszöbérték ablaktáblán.
     - Az itt való belépés előtt létre kell hoznia egy webhookot.  

       A webhookokkal kapcsolatos további információkért lásd: [webhook vagy API Azure-függvény létrehozása](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Ár megtekintése erőforrás szerint 
A Labs havi költség trend szolgáltatásával megtekintheti, hogy mennyit költött el az aktuális naptári hónapban. Azt is mutatja, hogy a kiadások kivetítése a hónap végéig, az elmúlt hét napban megadott költségek alapján történik. Annak megismeréséhez, hogy a laborban töltött kiadások miért nem teljesítik a küszöbértékeket, használhatja a **Cost by Resource** funkciót, amely megjeleníti a tábla **erőforrásainak** havi költségeit.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A laborok listájából válassza ki a kívánt labort.  
4. Válassza a **konfiguráció és szabályzatok** lehetőséget a bal oldali menüben.
5. A bal oldali menüben a **Cost Tracking (díjszabás** ) szakaszban válassza ki az **erőforrás** lehetőséget. Ekkor megjelenik a laborhoz társított egyes erőforrásokhoz kapcsolódó költségek. 

    ![Költségek erőforrások szerint](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Ezzel a funkcióval egyszerűen azonosíthatók azok az erőforrások, amelyek a legtöbbet teszik lehetővé, így a laboratóriumi költségek csökkentése érdekében műveleteket végezhet. Egy virtuális gép díja például a virtuális gép méretétől függ. Minél nagyobb a virtuális gép mérete, annál több a díj. Könnyen megtalálhatja a virtuális gép méretét és a tulajdonost, így megtudhatja, hogy a virtuális gép tulajdonosa miért van szükség az ilyen virtuálisgép-méretre, és hogy van-e lehetőség a méret csökkentésére.

Az [automatikus leállítási szabályzat](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) segít csökkenteni a költségeket azáltal, hogy a nap adott időpontjában leállítja a labor virtuális gépeket. A labor felhasználó azonban letilthatja a leállítási szabályzatot, ami növeli a virtuális gép futtatásának költségeit. Kiválaszthat egy virtuális gépet a táblázatban, hogy megtudja, van-e kiválasztva az automatikus leállítási házirendből. Ebben az esetben a virtuális gép tulajdonosával megtudhatja, miért választotta ki a virtuális gépet a szabályzatból.
 
## <a name="next-steps"></a>Következő lépések
A következő néhány dolgot érdemes kipróbálni:

* [Tesztkörnyezet-házirendek meghatározása](devtest-lab-set-lab-policy.md) – útmutató a tesztkörnyezet és a virtuális gépek használatának szabályozásához használt különböző szabályzatok beállításához. 
* [Egyéni rendszerkép létrehozása](devtest-lab-create-template.md) – virtuális gép létrehozásakor meg kell adnia egy alapot, amely lehet egyéni rendszerkép vagy Piactéri rendszerkép is. Ez a cikk bemutatja, hogyan hozhat létre egyéni rendszerképet egy VHD-fájlból.
* [Marketplace-lemezképek konfigurálása](devtest-lab-configure-marketplace-images.md) – a DevTest Labs támogatja az Azure Marketplace-lemezképeken alapuló virtuális gépek létrehozását. Ez a cikk bemutatja, hogyan határozható meg, hogy mely, ha van ilyen, Azure Marketplace-lemezképeket használhat a virtuális gépek tesztkörnyezetben való létrehozásakor.
* [Virtuális gép létrehozása laborban](devtest-lab-add-vm.md) – azt mutatja be, hogyan hozható létre virtuális gép egy alaprendszerképből (akár egyéni, akár piactér), és hogyan használhatók az összetevők a virtuális gépen.
