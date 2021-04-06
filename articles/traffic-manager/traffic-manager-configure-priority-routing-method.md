---
title: 'Oktatóanyag: prioritású forgalom útválasztásának konfigurálása az Azure Traffic Manager'
description: Ez az oktatóanyag ismerteti, hogyan konfigurálhatja a prioritási forgalom útválasztási módszerét Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92208056"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Oktatóanyag: a prioritási forgalom útválasztási módszerének konfigurálása Traffic Manager

Ez az oktatóanyag azt ismerteti, hogyan lehet az Azure Traffic Manager használatával átirányítani a felhasználói forgalmat adott végpontokra a prioritásos útválasztási módszer használatával. Ebben az útválasztási módszerben meg kell határozni az egyes végpontok sorrendjét, amelyek bekerülnek Traffic Manager profil konfigurációba. A felhasználóktól érkező forgalom a listázott sorrendben lesz átirányítva a végpontra. Ez az útválasztási módszer akkor hasznos, ha a szolgáltatás feladatátvételét szeretné konfigurálni. Az elsődleges végpont a (z) "1" prioritási számot kapja, és minden bejövő kérelmet kiszolgál. Míg az alacsonyabb prioritású végpontok biztonsági mentéseket fognak működni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Hozzon létre egy Traffic Manager-profilt prioritás-útválasztással.
> - Végpontok hozzáadása.
> - Konfigurálja a végpontok prioritását.
> - Használja a Traffic Manager profilt.
> - Traffic Manager profil törlése.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="to-configure-the-priority-traffic-routing-method"></a>A prioritási forgalom útválasztási módszerének konfigurálása
1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **+ erőforrás létrehozása** lehetőséget a bal oldalon. Keressen rá **Traffic Manager profilra** , és válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Traffic Manager prioritási profil létrehozása":::

1. A *Traffic Manager profil létrehozása* lapon adja meg a következő beállításokat:

    | Beállítás         | Érték                                              |
    | ---             | ---                                                |
    | Név            | Adja meg a profil nevét. Ennek a névnek egyedinek kell lennie az trafficmanager.net zónán belül. A Traffic Manager-profil eléréséhez használja a DNS-nevet `<profilename>.trafficmanager.net` . |    
    | Útválasztási metódus  | Válassza a **Priority (prioritás**) lehetőséget. |
    | Előfizetés    | Válassza ki előfizetését. |
    | Erőforráscsoport   | Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot, amelybe a profilt be kívánja helyezni. Ha úgy dönt, hogy létrehoz egy új erőforráscsoportot, az *erőforráscsoport helye* legördülő menüben adja meg az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan telepített Traffic Manager-profilra. |

1. Válassza a **Létrehozás** lehetőséget a Traffic Manager profil telepítéséhez.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Traffic Manager profil prioritásának létrehozása":::

## <a name="add-endpoints"></a>Végpontok hozzáadása

1. Válassza ki a Traffic Manager profilt a listából.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Traffic Manager profilok listája":::

1. Válassza a **végpontok** lehetőséget a *Beállítások* területen, majd válassza a **+ Hozzáadás** lehetőséget új végpont hozzáadásához.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Végpontok hozzáadása Traffic Manager":::

1. Válassza ki vagy adja meg a következő beállításokat: 

    | Beállítás                | Érték                                              |
    | ---                    | ---                                                |
    | Típus                   | Válassza ki a végpont típusát. |    
    | Name                   | Adjon meg egy nevet a végpont azonosításához. |
    | Célerőforrás típusa   | Válassza ki a cél erőforrás-típusát. |
    | Célerőforrás        | Válassza ki az erőforrást a listából. |
    | Prioritás               | Adjon meg egy prioritási számot ehhez a végponthoz. 1 a legmagasabb prioritás. |


1. A végpont hozzáadásához válassza a **Hozzáadás** lehetőséget. További végpontok hozzáadásához ismételje meg a 2. és a 3. lépést. Ne felejtse el beállítani a megfelelő prioritási számot.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="1. prioritású végpont hozzáadása":::

1. A **végpontok** lapon tekintse át a végpontok prioritási sorrendjét. Ha a **prioritási** forgalom útválasztási módszerét választja, a kiválasztott végpontok sorrendje számít. Ellenőrizze a végpontok prioritási sorrendjét.  Az elsődleges végpont felül van. Ellenőrizze a megjelenő sorrendet. Az összes kérelem át lesz irányítva az első végpontra, és ha Traffic Manager észleli, hogy sérült, akkor a forgalom automatikusan átadja a következő végpontot. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Prioritási végpontok listája":::

1. A végpont prioritási sorrendjének módosításához válassza ki a végpontot, módosítsa a prioritás értékét, majd válassza a **Mentés** lehetőséget a végpont beállításainak mentéséhez.

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager profil használata

1.  A portálon keresse meg az előző szakaszban létrehozott **Traffic Manager profil** nevét, és válassza a Traffic Manager-profil lehetőséget a megjelenített eredmények között.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Keresés Traffic Manager profilban":::

1.  Az **Traffic Manager profil** áttekintése lap az újonnan létrehozott Traffic Manager-profil DNS-nevét jeleníti meg. Ezt bármely ügyfél használhatja (például a böngésző használatával történő navigálással), hogy a jobb oldali végpontra irányítsa át az útválasztási típus alapján. Ebben az esetben a rendszer az első végponthoz irányítja át a kérelmeket, és ha Traffic Manager észleli, hogy sérült, a forgalom automatikusan átadja a következő végpontot.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Traffic Manager DNS-neve":::

1. Ha a Traffic Manager-profilja működik, szerkessze a DNS-rekordot a mérvadó DNS-kiszolgálón, hogy a vállalat tartománynevét a Traffic Manager tartománynevére mutassa.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a Traffic Manager profilra, keresse meg a profilt, és válassza a **Profil törlése** lehetőséget.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Traffic Manager prioritási profil törlése":::

## <a name="next-steps"></a>Következő lépések

A Kiemelt útválasztási módszerről további információért lásd:

> [!div class="nextstepaction"]
> [Prioritási útválasztási módszer](traffic-manager-routing-methods.md#priority-traffic-routing-method)
