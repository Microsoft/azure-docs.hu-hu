---
title: Hely kiválasztásának engedélyezése a labor Creator számára Azure Lab Services
description: Ez a cikk azt ismerteti, hogy a labor-fiókok rendszergazdája hogyan veheti igénybe a labor-készítőket a laboratóriumok helyeinek kiválasztásához.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444199"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>A labor létrehozójának kiválasztási helyének engedélyezése a laborban Azure Lab Services
Azure Lab Services a labor-fiók tulajdonosa engedélyezheti a labor-létrehozók (oktatók) számára, hogy kiválasszák a létrehozott labor helyét. Ez a hely eltérő lehet a labor-fiók helyétől. A hely az Azure-régiók egy csoportja. Például Egyesült Államok hely olyan régiók csoportja, mint például az USA keleti régiója, az USA nyugati régiója és így tovább. 

A labor-fiók tulajdonosaként a Lab-fiók létrehozása és a tesztkörnyezet létrehozása (vagy egy meglévő labor-fiók) létrehozása után is kiválaszthatja a labor- **létrehozó engedélyezése** lehetőséget. 

## <a name="at-the-time-of-lab-account-creation"></a>A labor-fiók létrehozásakor
Labor-fiók létrehozásakor ez a lehetőség az első képernyőn jelenik meg (**alapbeállítások** lap). 

![A beállítás engedélyezése a labor létrehozásakor](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Ez a beállítás le van tiltva, ha a **speciális** lapon kiválaszt egy társ virtuális hálózatot a labor-fiókjához.  

![Ha a társ virtuális hálózat engedélyezve van](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>A labor-fiók létrehozása után
A tesztkörnyezet létrehozása után a következő lépésekkel engedélyezheti vagy tilthatja le ezt a beállítást: 

1. A **labor-fiók** lapon válassza a bal oldali menüben a **labor beállításai** lehetőséget.
2. Jelölje be a tesztkörnyezet **-létrehozó engedélyezése a labor helyének** kiválasztása lehetőséget, ha engedélyezni szeretné a tesztkörnyezet létrehozójának a tesztkörnyezet helyét. Ha a szolgáltatás le van tiltva, a rendszer automatikusan létrehozza a laborokat ugyanazon a helyen, ahol a labor-fiók létezik. 
    
    Ez a mező le van tiltva, amikor kiválaszt egy virtuális hálózatot a **társ virtuális hálózat** mezőhöz. Ez azért van, mert a labor-fiókban lévő laboroknak ugyanabban a régióban kell lenniük, mint a társ virtuális hálózat erőforrásaihoz való hozzáféréshez. 
1. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Tesztkörnyezet beállításai](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Nem engedélyezett a virtuális hálózat és a hely kiválasztása
Ebben a forgatókönyvben nem engedélyezte a **tesztkörnyezet-létrehozó engedélyezése a labor helyének kiválasztása** lehetőséget. 

![Nincs labor helye](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Ezután a labor létrehozói (pedagógusok) nem választhatnak helyet a laborhoz. A rendelkezésre álló összes mérethez óránként óradíjat fognak látni. Amikor létrehoznak egy labort, a rendszer egy olyan Azure-régióban hozza létre, amely ugyanabban a helyen található, mint az az Azure-régió, ahol a labor-fiókja szerepel. Ha például a labor-fiók az **USA nyugati** régiójában található, akkor a labor létrehozása az **USA déli középső** régiójában lehetséges, de nem hozható létre **Kelet-Kanadában**. Nem garantáljuk, hogy mi a helyzet a régión kívülről. Ha a méret jelenleg korlátozott, akkor a tesztkörnyezet létrehozója egy jelölőnégyzetet jelenít meg, ahol láthatják, hogy az általában támogatott méretek láthatók, de jelenleg nem érhetők el. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>A virtuális hálózat és a hely kiválasztása nem engedélyezett
Ebben az esetben a labor- **létrehozó engedélyezése a labor helyének** kiválasztására beállítás le van tiltva, mert egy társ virtuális hálózatot választott a tesztkörnyezet fiókjához. Ezután a labor alkotói ugyanazt a képernyőt fogják látni, mint az előző lehetőséggel. Mivel minden virtuális gépnek ugyanabban az Azure-régióban kell lennie, mint a virtuális hálózatnak, a labor ugyanabban az Azure-régióban jön létre, ahol a virtuális hálózat található. Ha az adott régió mérete korlátozott, akkor a méret nem elérhetőként jelenik meg. 

## <a name="location-selection-is-enabled"></a>A hely kiválasztása engedélyezve van
Ha kiválasztja a labor **Creator engedélyezése lehetőséget a labor helyének** kiválasztásához, a labor-létrehozók (oktatók) lehetőségre kattintva kiválaszthatja a kívánt helyet a tesztkörnyezet létrehozásakor. 

![Tesztkörnyezet helyének kiválasztása](./media/allow-lab-creator-pick-lab-location/location-selection.png)

A labor-készítők az összes olyan hely árának tartományát látják, ahol a méret szerepel, és választhat egy helyet. A labor minden olyan Azure-régióban létrejön, amely az adott helyre leképezi.

Ha a hely korlátozott, alapértelmezés szerint nem jelenik meg a listában. Bontsa ki a legördülő listát, és válassza a nem **elérhető tárolóhelyek megjelenítése ehhez a mérethez** lehetőséget. 

![Nem elérhető helyszínek megjelenítése](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Költségek
Korábban a díjszabás a laborhoz választott virtuálisgép-mérettől függ. Az ár most az operációs rendszer (OS), a méret és a hely kombinációján alapul. 

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [A labor hálózatának összekötése egy társ virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
- [A labor egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)