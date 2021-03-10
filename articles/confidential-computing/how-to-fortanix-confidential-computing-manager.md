---
title: Fortanix bizalmas számítástechnikai kezelő egy Azure által felügyelt alkalmazásban
description: Megtudhatja, hogyan helyezhet üzembe Fortanix bizalmas számítástechnikai kezelőt (CCM) a Azure Portal felügyelt alkalmazásokban.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563421"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix bizalmas számítástechnikai kezelő egy Azure által felügyelt alkalmazásban

Ez a cikk bemutatja, hogyan helyezhet üzembe olyan alkalmazást, amelyet a Fortanix bizalmas számítástechnikai kezelője felügyel a Azure Portalban.

A Fortanix egy külső gyártótól származó szoftvergyártó, amely az Azure-infrastruktúrára épülő termékekkel és szolgáltatásokkal rendelkezik. Más külső szolgáltatók is hasonló bizalmas számítástechnikai szolgáltatásokat nyújtanak az Azure-ban.

> [!NOTE]
>A dokumentumban hivatkozott termékek nem a Microsoft felügyelete alá tartoznak. A Microsoft ezt az információt kizárólag kényelmi célokra nyújtja, és a nem Microsoft-termékekre való hivatkozás nem a Microsoft jóváhagyását jelenti.

## <a name="prerequisites"></a>Előfeltételek

- Privát Docker-beállításjegyzék a konvertált alkalmazások lemezképének leküldéséhez.
- Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Bizalmas számítástechnikai kezelő üzembe helyezése egy Azure által felügyelt alkalmazáson keresztül

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure Portal.":::

2. A keresési sávban keressen rá a "Fortanix bizalmas számítástechnikai kezelője" kifejezésre, és megtalálja a piactéren a Fortanix CCM-hoz tartozó listát. Válassza **a Fortanix bizalmas számítástechnika kezelője az Azure**-ban lehetőséget.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace-lista.":::

3. Megnyílik az a lap, amelyen létrehozza a CCM által felügyelt alkalmazást. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Hozzon létre egy alkalmazást.":::

4. Töltse ki az összes kötelező mezőt.
   1. A felügyelt alkalmazás részletei szakaszban a **felügyelt erőforráscsoport** mezőben egy alapértelmezett érték fog megjelenni, amelyet a felhasználó módosíthat, ha szükséges.
   2. A régió mezőben válassza ki a **Kelet-Ausztrália**, a **Délkelet-Ausztrália**, az **USA keleti** régiója, az **USA 2. nyugati** régiója, **Nyugat**-Európa, **Észak-Európa**, **Közép**-Kanada, Kelet- **Kanada** vagy az **USA 2. keleti** **régiója** – euap.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Kötelező mezők":::

   Válassza a **felülvizsgálat + létrehozás** lehetőséget a Fortanix CCM által felügyelt alkalmazás létrehozásához.

5. Tekintse át a részleteket, és az érvényesítési fázisok után jelölje be az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet, majd válassza a **Létrehozás** lehetőséget a felügyelt alkalmazás létrehozásához.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Részletek áttekintése.":::

6. A Fortanix CCM üzembe helyezése elindul, és értesíti arról, hogy a telepítés folyamatban van.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Telepítési folyamat.":::

7. Ha a telepítés befejeződött, válassza az **Ugrás az erőforráshoz** gombot, hogy az üzembe helyezett CCM által felügyelt alkalmazás "áttekintés" lapjára lépjen a számítási csomópont regisztrálásához.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="A Azure Portal sikeres telepítését bemutató képernyőkép":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Képernyőkép, amely a Azure Portal bizalmas számítástechnikai erőforrásának áttekintését jeleníti meg.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>A számítási csomópont regisztrálása a Fortanix CCM-ben

1. A bal oldali navigációs menüből válassza a **bizalmas számítástechnikai kezelő** elemet. Jelentkezzen be a Fortanix CCM-ba, és hozzon létre egy fiókot a **9. ábrán** látható módon.

    A regisztrációval, bejelentkezéssel és a CCM-fiók létrehozásával kapcsolatos további információkért tekintse meg a [ccm első lépések](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Képernyőkép, amely a Fortanix bizalmas számítástechnika-kezelő bejelentkezési adatait jeleníti meg.":::
    
2. Az illesztési jogkivonat a CCM felügyeleti konzolról való lekéréséhez először válassza a **csomópont** beléptetése gombot. Ezután a csomópont beléptetése ablakban válassza a **Másolás** gombot az illesztési jogkivonat másolásához.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Az illesztési token beszerzését bemutató képernyőkép.":::

3. Most, hogy regisztrálja a csomópont-ügynököt, válassza a **bizalmas számítástechnikai csomópont-ügynök** lapot, és válassza a **Hozzáadás** lehetőséget a CCM-csomóponti ügynök hozzáadásához.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="A csomópont-ügynök hozzáadását bemutató képernyőkép.":::

4.  A CCM-csomópont ügynöke űrlapon töltse ki az összes kötelező mezőt. Illessze be a 2. lépésben a **JOIN tokenben** másolt JOIN tokent. A megerősítéshez válassza a **felülvizsgálat + küldés** lehetőséget.

    A CCM számítási csomópontjának regisztrálásával kapcsolatos további információkért lásd: a [számítási csomópont regisztrálása](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="A számítási csomópont regisztrálását bemutató képernyőkép.":::
    
5. Az ellenőrzés után válassza a **Submit (Küldés** ) lehetőséget a csomópont-ügynök létrehozásának befejezéséhez.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="A csomópont-ügynököt bemutató képernyőkép.":::

6. A központi telepítés állapotának megtekintéséhez nyissa meg az **Áttekintés** lapot, és válassza a **felügyelt erőforráscsoport** hivatkozás lehetőséget.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="A csomópontot bemutató képernyőkép.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="A központi telepítés állapotának ellenőrzését bemutató képernyőkép.":::

7. Most láthatja, hogy a központi telepítés állapota még folyamatban van, és eltarthat néhány percig, amíg a csomópont-ügynök sikeresen regisztrálva lesz.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="A telepítést bemutató képernyőkép.":::

8. Ha a csomópont-ügynök beléptetése sikeres, az állapot "sikeres" állapotúra változik.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="A központi telepítés sikerességét bemutató képernyőkép.":::

9. Most a CCM által felügyelt alkalmazásban nyissa meg a számítási csomópontok lapjait, és figyelje meg, hogy a csomópont **aktív** állapotban van, és sikeresen bejelentkezett.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="A csomópont sikeres regisztrálását bemutató képernyőkép.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A felhasználó törölheti is a CCM Node Agent ügynököt a bizalmas számítástechnikai csomópont-ügynök lapról. A csomópont-ügynök törléséhez válassza ki a csomópont-ügynököt, és kattintson a felső sávon található **Törlés** gombra.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="A csomópont-ügynök törlését bemutató képernyőkép.":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy, az Azure által felügyelt alkalmazás használatával regisztrált egy csomópontot a Fortanix bizalmas számítástechnikai kezelőjéhez. A csomópont-beléptetés lehetővé teszi, hogy az alkalmazás rendszerképét a bizalmas számítástechnikai virtuális gépeken való futtatásra alakítsa át. Az Azure-beli bizalmas számítástechnikai virtuális gépekkel kapcsolatos további információkért lásd: [Virtual Machines-megoldások](virtual-machine-solutions.md).

Az Azure bizalmas számítástechnikai kínálatával kapcsolatos további információkért lásd: [Azure bizalmas számítástechnika](overview.md).

Megtudhatja, hogyan hajthat végre hasonló feladatokat más, harmadik féltől származó ajánlatokkal az Azure-ban, például a [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) és a [Scone](https://sconedocs.github.io).

