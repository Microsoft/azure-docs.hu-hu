---
title: 'Oktatóanyag: földrajzi forgalom útválasztásának konfigurálása az Azure Traffic Manager'
description: Ez az oktatóanyag azt ismerteti, hogyan konfigurálható a földrajzi forgalom útválasztási módszere az Azure Traffic Manager használatával
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96188666"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Oktatóanyag: a földrajzi forgalom útválasztási módszerének konfigurálása Traffic Manager használatával

A földrajzi forgalom útválasztási módszere lehetővé teszi, hogy az adott végpontokra irányítsa a forgalmat azon a földrajzi hely alapján, ahol a kérelmek származnak. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Traffic Manager profilt ezzel az útválasztási módszerrel, és hogyan konfigurálhatja a végpontokat az adott földrajzi helyekről érkező forgalom fogadására.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Hozzon létre egy Traffic Manager-profilt földrajzi útválasztással.
> - Beágyazott végpont konfigurálása.
> - Használja a Traffic Manager profilt.
> - Traffic Manager profil törlése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **+ erőforrás létrehozása** lehetőséget a bal oldalon. Keressen rá **Traffic Manager profilra** , és válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Traffic Manager-profil létrehozása":::

1. A *Traffic Manager profil létrehozása* lapon adja meg a következő beállításokat:

    | Beállítás         | Érték                                              |
    | ---             | ---                                                |
    | Név            | Adja meg a profil nevét. Ennek a névnek egyedinek kell lennie az trafficmanager.net zónán belül. A Traffic Manager-profil eléréséhez használja a DNS-nevet `<profilename>.trafficmanager.net` . |    
    | Útválasztási metódus  | Válassza a **földrajzi** lehetőséget. |
    | Előfizetés    | Válassza ki előfizetését. |
    | Erőforráscsoport   | Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot, amelybe a profilt be kívánja helyezni. Ha úgy dönt, hogy létrehoz egy új erőforráscsoportot, az *erőforráscsoport helye* legördülő menüben adja meg az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan telepített Traffic Manager-profilra. |

1. Válassza a **Létrehozás** lehetőséget a Traffic Manager profil telepítéséhez.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Traffic Manager profil létrehozása lap":::

## <a name="add-endpoints"></a>Végpontok hozzáadása

1. Válassza ki a Traffic Manager profilt a listából.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Manager földrajzi lista":::

1. Válassza a **végpontok** lehetőséget a *Beállítások* területen, majd válassza a **+ Hozzáadás** lehetőséget új végpont hozzáadásához.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Végpontok hozzáadása":::

1. Válassza ki vagy adja meg a következő beállításokat: 

    | Beállítás                | Érték                                              |
    | ---                    | ---                                                |
    | Típus                   | Válassza ki a végpont típusát. Az éles környezetben használt földrajzi útválasztási profilok esetében javasoljuk, hogy használjon olyan beágyazott végpontokat, amelyek egynél több végponttal rendelkező alárendelt profilt tartalmaznak. További információ: [a földrajzi forgalom útválasztási módszereivel kapcsolatos gyakori kérdések](traffic-manager-FAQs.md). |    
    | Name                   | Adjon meg egy nevet a végpont azonosításához. |
    | Célerőforrás típusa   | Válassza ki a cél erőforrás-típusát. |
    | Célerőforrás        | Válassza ki az erőforrást a listából. |

    > [!Note]
    > Ezen a lapon bizonyos mezők a hozzáadott végpont típusától függenek:
    > 1. Ha Azure-végpontot ad hozzá, válassza ki a **cél erőforrás típusát** és **célját** azon erőforrás alapján, amelynek a forgalmát át szeretné irányítani
    > 1. Ha **külső** végpontot ad hozzá, adja meg a végpont teljes tartománynevét **(FQDN)** .
    > 1. Ha **beágyazott végpontot** ad hozzá, válassza ki a használni kívánt gyermek profilnak megfelelő **cél erőforrást** , és adja meg a **gyermek végpontok minimális darabszámát**.

1. A *geo-leképezés* szakaszban a legördülő lista segítségével adja hozzá azokat a régiókat, amelyekről szeretné elküldeni a forgalmat a végpontnak. Legalább egy régiót fel kell venni. Több régió is leképezhető.

1. Ismételje meg az utolsó lépést minden olyan végpont esetében, amelyet hozzá szeretne adni a profil alatt, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Traffic Manager-végpont hozzáadása":::

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager profil használata

1.  A portálon keresse meg az előző szakaszban létrehozott **Traffic Manager profil** nevét, és válassza a Traffic Manager-profil lehetőséget a megjelenített eredmények között.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager-profil keresése":::

1. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. A nevet bármely ügyfél használhatja (például a böngésző használatával történő navigálással), hogy a jobb oldali végpontra irányítsa át az útválasztási típus alapján. A földrajzi útválasztásnál Traffic Manager a bejövő kérelem forrás IP-címét, és meghatározza azt a régiót, amelyből a származik. Ha az adott régió egy végpontra van leképezve, a rendszer átirányítja a forgalmat oda. Ha ez a régió nincs leképezve végpontra, akkor Traffic Manager egy Adatlekérdezési választ ad vissza.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="A földrajzi Traffic Manager áttekintése":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a Traffic Manager profilra, keresse meg a profilt, és válassza a **Profil törlése** lehetőséget.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Traffic Manager profil törlése":::

## <a name="next-steps"></a>Következő lépések

A földrajzi útválasztási módszerekkel kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Földrajzi forgalom útválasztási módszere](traffic-manager-routing-methods.md#geographic)
