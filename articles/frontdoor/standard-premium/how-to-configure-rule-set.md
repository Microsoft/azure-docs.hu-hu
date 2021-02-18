---
title: 'Azure bejárati ajtó: az előtérben beállított szabálykészlet konfigurálása'
description: Ez a cikk útmutatást nyújt a szabálykészlet konfigurálásához.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: e2fe475b171a99ec27ed162511db289891066e00
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098881"
---
# <a name="configure-a-rule-set"></a>Szabálykészlet konfigurálása

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy szabálykészlet és az első szabálykészlet a Azure Portalban. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Szabálykészlet beállítása a portál használatával.
> - Szabálykészlet törlése a AFD-profilból a portál használatával

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag lépéseinek elvégzése előtt először létre kell hoznia egy Azure-beli bejárati ajtót (standard/prémium). További információ: gyors üzembe helyezés Azure-beli előtérben [standard/prémium szintű profil létrehozása](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Szabálykészlet konfigurálása Azure Portal

1. A bejárati ajtó profiljában válassza a **Beállítások** területen található **szabálykészlet** elemet. Válassza a **Hozzáadás** lehetőséget, és adja meg a szabálykészlet nevét.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="A szabálykészlet kezdőlapjának képernyőképe":::
    
1. Az első szabály létrehozásához kattintson a **szabály hozzáadása** lehetőségre. Adja meg a szabály nevét. Ezután válassza a **feltétel hozzáadása** vagy a **művelet hozzáadása** lehetőséget a szabály definiálásához. Egy szabályhoz legfeljebb 10 feltételt és 5 műveletet adhat meg. Ebben a példában a kiszolgálói változót használjuk, hogy a *contosot* tartalmazó kérelmekhez egy 8Geo-országot adjon hozzá az URL-címben.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="A szabálykészlet konfigurációs oldalának képernyőképe.":::
    
    > [!NOTE]
    > * Egy szabály feltételének vagy műveletének törléséhez használja a szemetet az adott feltétel vagy művelet jobb oldalán.
    > * Egy olyan szabály létrehozásához, amely az összes bejövő forgalomra vonatkozik, ne határozzon meg feltételeket.
    > * Ha meg szeretné szüntetni a fennmaradó szabályok kiértékelését, ha egy adott szabály teljesül, ellenőrizze a **fennmaradó szabály kiértékelésének befejezését**. Ha ez a beállítás be van jelölve, és a szabálykészlet összes többi szabálya nem lesz végrehajtva, függetlenül attól, hogy teljesülnek-e a megfelelő feltételek.  

1. A szabálykészlet szabályainak prioritását a nyílgombok segítségével határozhatja meg, hogy a szabályok magasabb vagy alacsonyabb prioritással legyenek áthelyezve. A lista növekvő sorrendben van, ezért a legfontosabb szabály a listában látható.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="A szabálykészlet prioritásának képernyőképe" lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Miután létrehozott egy vagy több szabályt, válassza a **Mentés** lehetőséget a szabálykészlet létrehozásának befejezéséhez.

1. Most rendelje hozzá a szabályt egy útvonalhoz, hogy az érvénybe lépjen. Társíthatja a szabálykészlet lapon beállított szabályokat, vagy megtekintheti a végpont-kezelőt a társítás létrehozásához.
 
    **Szabálykészlet lap**: 
    
    1. Válassza ki a társítani kívánt szabályt.
    
    1. Válassza ki a nem *társított* hivatkozást.
     

    1. Ezután az **útvonal hozzárendelése** panelen válassza ki azt a végpontot és útvonalat, amelyet a szabálykészlet alapján szeretne hozzárendelni. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Az útvonal létrehozása lap képernyőképe.":::    
        
    1. Kattintson a *tovább* gombra a szabálykészlet-megrendelések módosításához, ha több szabálykészlet van a kijelölt útvonal alatt. A szabálykészlet felülről lefelé lesz végrehajtva. A rendeléseket a szabálykészlet kiválasztásával és a feljebb vagy lejjebb történő mozgatásával módosíthatja. Ezután válassza a *hozzárendelés* lehetőséget.
    
        > [!Note]
        > Ezen a lapon csak egyetlen útvonallal rendelkező szabálykészlet társítható. Ha egy szabálykészlet több útvonalhoz van társítva, használja a Endpoint Managert.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="A szabálykészlet rendeléseinek képernyőképe":::
    
    1. A szabálykészlet mostantól egy útvonallal van társítva. Tekintse meg a válasz fejlécét, és tekintse meg a földrajzi országot.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Az útvonalhoz tartozó szabály képernyőképe.":::

   **Endpoint Manager**: 
    
    1. Nyissa meg a Endpoint Manager eszközt, és válassza ki azt a végpontot, amelyet hozzá szeretne rendelni a szabálykészlet számára.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Képernyőkép a végpont kiválasztásáról a Endpoint Managerben." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Kattintson a *végpont szerkesztése* elemre.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Képernyőkép a végpont szerkesztése lehetőség kiválasztásáról a Endpoint Managerben." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Kattintson az útvonalra. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Képernyőkép az útvonal kiválasztásáról.":::
    
    1. Az *útvonal frissítése* panelen, a *szabályok* területen válassza ki azt a szabályt, amelyet az útvonalhoz szeretne rendelni a legördülő listából. Ezt követően módosíthatja a rendeléseket úgy, hogy a beállított szabály fel és le értékre van állítva. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Képernyőkép az útvonal frissítése oldalról.":::
    
    1. Ezután válassza a *frissítés* vagy *Hozzáadás* elemet a társítás befejezéséhez.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Szabálykészlet törlése az Azure-beli előtérben lévő profilból

Az előző lépésekben konfigurálta és társította az útvonalhoz beállított szabályt. Ha már nem szeretné, hogy a szabálykészlet a bejárati ajtóhoz legyen társítva, a következő lépések végrehajtásával távolíthatja el a szabályt:

1. Nyissa meg a **szabálykészlet lapot** a **Beállítások** területen a szabálykészlet az összes társított útvonalból való társításának megszüntetéséhez.

1. Bontsa ki az útvonalat, és kattintson a három pontra, majd az *útvonal szerkesztése* lehetőségre.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Képernyőkép az útvonal kibontásáról a szabálykészlet beállításban.":::

1. Ugrás a szabályok szakaszra az útvonal lapon válassza ki a szabályt, majd kattintson a *Törlés* gombra. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Képernyőkép az útvonal frissítése lapról a szabálykészlet törléséhez." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Válassza a *frissítés* lehetőséget, és a szabálykészlet nem fog megjelenni az útvonalon.

1. Ismételje meg az 2-5-es lépést a szabálykészlet más útvonalak társításának megszüntetéséhez, amíg az útvonalak állapota nem *jelenik meg.*

1. A nem *társított* szabálykészlet esetében törölheti a szabályt úgy, hogy a jobb oldalon található három pontra kattint, és kiválasztja a *Törlés* lehetőséget. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="A szabálykészlet törlésének képernyőképe.":::

1. A szabálykészlet már törölve lett.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Szabálykészlet létrehozása
* Rendeljen hozzá egy szabályt a AFD útvonalhoz.
* Szabálykészlet törlése a AFD-profilból

Ha meg szeretné tudni, hogyan adhat hozzá biztonsági fejléceket a szabálykészlet használatával, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Biztonsági fejlécek beállított szabályokkal]()
