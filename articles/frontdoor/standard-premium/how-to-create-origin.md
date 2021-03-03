---
title: Azure bejárati ajtó standard/prémium (előzetes verzió) forrásának beállítása
description: Ez a cikk bemutatja, hogyan konfigurálhat egy forrást a Endpoint Managerrel.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741888"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Azure bejárati ajtó standard/prémium (előzetes verzió) forrásának beállítása

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure-beli előtérben lévő standard/prémium szintű forrást egy meglévő Origin csoportban. 

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy létre tudja hozni az Azure bejárati ajtó standard/prémium forrását, létre kell hoznia legalább egy származási csoportot.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Új Azure bejárati ajtó standard/prémium forrásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure bejárati ajtó standard/Premium profiljához.

1. Válassza ki a **forrás csoportot**. Ezután válassza a **+ Hozzáadás** lehetőséget egy új Origin csoport létrehozásához.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Képernyőkép a forrás csoport kezdőlapján.":::

1. A **Origó hozzáadása** lapon adjon meg egy egyedi **nevet** az új származási csoport számára.

1. Ezután válassza a **+ forrás hozzáadása lehetőséget** , ha új forrást szeretne hozzáadni ehhez a származási csoporthoz. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Képernyőkép a forrás hozzáadása oldalról.":::
  
    | Beállítás | Érték |
    | --- | --- |
    | Név | Adjon meg egy egyedi nevet az új Azure-beli bejárati ajtó forrásához. |   
    | Forrás típusa | A hozzáadni kívánt erőforrás típusa. Az Azure bejárati ajtó standard/Premium verziója az App Service, a Cloud Service vagy a Storage szolgáltatásból származó alkalmazás-forrás automatikus észlelését támogatja. Ha más erőforrást szeretne használni az Azure-ban vagy egy nem Azure-beli hátteret, válassza az **Egyéni gazdagép** lehetőséget. |
    | Gazdagép neve  | Ha nem az **Egyéni gazdagép** lehetőséget választotta a forrás gazdagép típusaként, válassza ki a kívánt hátteret a legördülő menüben a forrás gazdagép nevének kiválasztásával. |
    | Forrás állomásfejléc | Adja meg a háttérként küldött állomásfejléc értékét az egyes kérések esetében. További információ: [Origin Host header](concept-origin.md#hostheader). |
    | HTTP-port | Adja meg annak a portnak az értékét, amelyet a forrás támogat a HTTP protokollhoz. |
    | HTTPS-port | Adja meg annak a portnak az értékét, amelyet a forrás támogat a HTTPS protokollhoz. |
    | Prioritás | Rendeljen prioritásokat a különböző forrásokhoz, ha az összes forgalomhoz elsődleges szolgáltatási forrást szeretne használni. Adja meg a biztonsági mentéseket is, ha az elsődleges vagy a biztonsági mentés forrása nem érhető el. További információ: Priority ( [prioritás](concept-origin.md#priority)). |
    | Tömeg | A különböző eredetekhez tartozó súlyok kiosztásával terjesztheti a forgalmat egy adott készleten belül, akár egyenletesen, akár súlyozási együtthatók alapján. További információ: [súlyok](concept-origin.md#weighted). |
    | Állapot | Válassza ezt a lehetőséget a forrás engedélyezéséhez. |
    | Szabály | Válassza ki az útvonalra alkalmazni kívánt szabálykészlet. A szabályok konfigurálásával kapcsolatos további információkért lásd: [szabálykészlet beállítása az Azure bejárati ajtóhoz](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > A konfiguráció során az API-k nem ellenőrzik, hogy a forrás elérhetetlenné válik-e az előtérben lévő környezetekben. Győződjön meg arról, hogy a bejárati ajtó elérheti a forrást.

1. Válassza a **Hozzáadás** lehetőséget az új forrás létrehozásához. A létrehozott forrásnak szerepelnie kell a forrás listában a csoporttal.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Képernyőkép a forrásról a lista nézetben.":::

1. A **Hozzáadás** gombra kattintva adja hozzá a forrás csoportot az aktuális végponthoz. A kezdőpontnak szerepelnie kell a forrás csoport paneljén.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége rá, kattintson a **...** elemre, majd a legördülő listából válassza a **Törlés** lehetőséget.

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="A Origin csoport törlésének képernyőképe.":::

Ha már nincs szüksége a forrás törlésére, kattintson a.. **.** elemre, majd válassza a **Törlés** lehetőséget a legördülő menüből. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Képernyőkép a forrás törléséről.":::

## <a name="next-steps"></a>Következő lépések

Az egyéni tartományokkal kapcsolatos további tudnivalókért lásd: [egyéni tartomány hozzáadása](how-to-add-custom-domain.md) az Azure bejárati ajtó standard/prémium végponthoz.
