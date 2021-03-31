---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86544626"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Nyilvános végpontok létrehozása a felhőalapú készüléken

1. Jelentkezzen be az Azure Portalra.
2. Lépjen a **Virtuális gépek** elemre, majd válassza ki a felhőalapú készülékként használt virtuális gépet, és kattintson rá.
    
3. Létre kell hozni egy hálózati biztonság csoportra (network security group, NSG) vonatkozó szabályt a virtuális gépbe és gépből irányuló forgalom szabályozásához. Az NSG-re vonatkozó szabály létrehozásához hajtsa végre az alábbi lépéseket.
    1. Válassza a **Hálózati biztonsági csoport** lehetőséget.
        ![Képernyőkép a virtuális gép oldaláról. A beállítások szakaszban a hálózati biztonsági csoport van kiemelve.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Kattintson a megjelenő alapértelmezett hálózati biztonsági csoportra.
        ![A hálózati biztonsági csoport oldalának képernyőképe. Az alapértelmezett hálózati biztonsági csoport ki van emelve.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Válassza a **Bejövő biztonsági szabályok** lehetőséget.
        ![Képernyőkép az alapértelmezett hálózati biztonsági csoport tulajdonságait megjelenítő oldalról. A navigációs ablaktáblán a bejövő biztonsági szabályok ki vannak emelve.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Kattintson a **+ Hozzáadás** gombra egy bejövő biztonsági szabály létrehozásához.
        ![Képernyőkép a bejövő biztonsági szabályok lapról. A pluszjel és a szó hozzáadása egymás mellett történik, és ki vannak emelve.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        A Bejövő biztonsági szabály felvétele panelen:

        1. A **név** mezőbe írja be a következő végpont nevét: WinRMHttps.
        
        2. A **Prioritás** értékhez válasszon egy 1000-nél (amely az alapértelmezett szabálynak van fenntartva) kisebb számot. Minél magasabb az érték, annál alacsonyabb a prioritás.

        3. A **Forrás** elemet állítsa **Bármely** értékre.

        4. A **Szolgáltatásnál** válassza a **WinRM** lehetőséget. A **Protokoll** értéke automatikusan **TCP** lesz, a **Porttartomány** értéke pedig **5986**.

        5. A szabály létrehozásához kattintson az **OK** gombra.

            ![Képernyőkép a bejövő biztonsági szabály hozzáadása panelről. Az értékek kitöltése az eljárásban leírtak szerint történik, és az OK gomb ki van emelve.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Az utolsó lépés a hálózati biztonsági csoport társítása egy alhálózattal vagy egy adott hálózati adapterrel. Ha a hálózati biztonsági csoportot egy alhálózattal szeretné társítani, végezze el a következő lépéseket.
    1. Nyissa meg az **Alhálózatok** részt.
    2. Kattintson a **+ Társítás** gombra.
        ![Képernyőkép az alhálózatok oldalról. A pluszjel és a szó hozzárendelése egymás mellett történik, és ki vannak emelve.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Válassza ki a virtuális hálózatot, majd válassza ki a megfelelő alhálózatot.
    4. A szabály létrehozásához kattintson az **OK** gombra.

        ![Képernyőkép az alhálózat hozzárendelése oldalról. A virtuális hálózat ki van választva, és az OK gomb ki van jelölve.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Ha létrehozta a szabályt, a részletek között megnézheti a nyilvános virtuális IP-címet (VIP). Jegyezze fel ezt az IP-címet.


