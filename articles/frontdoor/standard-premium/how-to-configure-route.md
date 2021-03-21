---
title: Az Azure-beli előtérben lévő útvonal konfigurálása
description: Ez a cikk bemutatja, hogyan konfigurálhat egy útvonalat a tartományok és a származási csoportok között.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098923"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Azure bejárati ajtó standard/prémium útvonalának konfigurálása

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ez a cikk ismerteti a meglévő végpontok Azure-beli bejárati (AFD-) útvonalának létrehozásához használt beállításokat. Miután hozzáadta az egyéni tartományt és a forrást a meglévő Azure-beli bejárati végponthoz, konfigurálnia kell az útvonalat, hogy meghatározza a tartományok és eredetek közötti társítást a közöttük lévő forgalom irányításához.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az Azure bejárati útvonalának konfigurálása előtt létre kell hoznia legalább egy származási csoportot és egy egyéni tartományt az aktuális végponton belül. 

A Origin csoportok beállításával kapcsolatban lásd: [új Azure bejárati ajtó standard/prémium eredetű csoportjának létrehozása](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Új Azure-beli ajtós standard/prémium szintű útvonal létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure bejárati ajtó standard/Premium profiljához.

1. Válassza a **Endpoint Manager** lehetőséget a **Beállítások** területen.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Képernyőkép az első ajtós Endpoint Manager beállításairól." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Ezután válassza a **végpont szerkesztése** lehetőséget ahhoz a végponthoz, amelyhez az útvonalat konfigurálni kívánja.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Képernyőkép a végpont szerkesztése lehetőség kiválasztásáról.":::

1. Ekkor megjelenik a **végpont szerkesztése** oldal. Válassza a **+ Hozzáadás** útvonalakhoz lehetőséget.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Képernyőkép az útvonal hozzáadása a végpont szerkesztése lapon.":::    
    
1. Az **útvonal hozzáadása** lapon adja meg vagy válassza ki a következő adatokat.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Az útvonal hozzáadása lap képernyőképe." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Beállítás | Érték |
    | --- | --- |
    | Név | Adjon meg egy egyedi nevet az új útvonalhoz. |   
    | Tartomány| Válasszon ki egy vagy több olyan tartományt, amely érvényesítve lett, és nincs másik útvonalhoz társítva |
    | Egyeztetendő minták  | Adja meg az összes URL-elérésiút-mintát, amelyet ez az útvonal elfogad. Ezt beállíthatja például úgy, hogy `/images/*` fogadja az összes kérelmet az URL-címen `www.contoso.com/images/*` . A AFD először a pontos egyezés alapján kísérli meg megállapítani a forgalmat, ha nem pontosan egyeznek az elérési utak, majd megkeresi a megfelelő helyettesítő karaktert. Ha nem található egyező elérési úttal rendelkező útválasztási szabály, akkor utasítsa el a kérelmet, és adja vissza a 400-es hibát: hibás kérelem HTTP-válasza. |
    | Elfogadott protokollok | Határozza meg azokat a protokollokat, amelyeknek az Azure bejárati ajtaját el kell fogadnia, amikor az ügyfél a kérést kéri. |
    | Átirányítás | Annak megadása, hogy a HTTPS kikényszerítve van-e a HTTP-kérelemmel rendelkező bejövő kérelem esetében |
    | Forrás csoport | Válassza ki, hogy melyik származási csoportot kell továbbítani ahhoz, hogy a visszaadott forrásra vonatkozó kérelem megtörténjen. |
    | Forrás elérési útja | Adja meg a gyorsítótárazni kívánt erőforrások elérési útját. Ha engedélyezni szeretné a tartomány bármely erőforrásának gyorsítótárazását, hagyja üresen ezt a beállítást. |
    | Továbbító protokoll | Válassza ki a kérelem továbbításához használt protokollt. |
    | Gyorsítótárazás | Ezzel a beállítással engedélyezheti a statikus tartalom gyorsítótárazását az Azure-előtérben. |
    | Szabály | Válassza ki az útvonalra alkalmazni kívánt szabálykészlet. A szabályok konfigurálásával kapcsolatos további információkért lásd: [szabálykészlet beállítása az Azure bejárati ajtóhoz](how-to-configure-rule-set.md) | 

1. Válassza a **Hozzáadás** lehetőséget az új útvonal létrehozásához. Az útvonal megjelenik a végponthoz tartozó útvonalak listájában.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Képernyőkép az útvonalak listájáról.":::  
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rá egy útvonal törléséhez, válassza ki az útvonalat, majd válassza a **Törlés** lehetőséget. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Képernyőkép az útvonal törléséről.":::  

## <a name="next-steps"></a>Következő lépések
Ha szeretne többet megtudni az egyéni tartományokról, folytassa az Oktatóanyaggal, amely az Azure-beli előtérben lévő végponthoz adja hozzá az egyéni tartományt.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása]()
