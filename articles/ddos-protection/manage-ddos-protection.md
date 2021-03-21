---
title: Azure DDoS Protection standard kezelése a Azure Portal használatával
description: Ismerje meg, hogyan csökkentheti a támadásokat a Azure DDoS Protection standard használatával.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 336eeee7d522647d11f55a65dc9dc4e65e8b7d47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535236"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Rövid útmutató: Azure DDoS Protection standard létrehozása és konfigurálása

A Azure DDoS Protection standard használatának első lépései a Azure Portal használatával. 

A DDoS Protection-csomag olyan virtuális hálózatokat határoz meg, amelyeken engedélyezve van a DDoS Protection standard, az előfizetések között. Beállíthat egy DDoS Protection-tervet a szervezet számára, és a virtuális hálózatokat több előfizetésből ugyanahhoz a csomaghoz kapcsolhatja. 

Ebben a rövid útmutatóban létrehoz egy DDoS Protection-tervet, és összekapcsolja azt egy virtuális hálózattal. 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen. Győződjön meg arról, hogy a fiókja hozzá van rendelve a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely a útmutató az [engedélyek](manage-permissions.md)útmutatójában felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-ddos-protection-plan"></a>DDoS elleni védelmi terv létrehozása

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
2. Keressen rá a *DDoS* kifejezésre. Ha a **DDoS Protection-csomag** megjelenik a keresési eredmények között, válassza ki.
3. Válassza a **Létrehozás** lehetőséget.
4. Adja meg vagy válassza ki a következő értékeket, majd válassza a **Létrehozás** lehetőséget:

    |Beállítás        |Érték                                              |
    |---------      |---------                                          |
    |Név           | Adja meg a _MyDdosProtectionPlan_.                     |
    |Előfizetés   | Válassza ki előfizetését.                         |
    |Erőforráscsoport | Válassza az **új létrehozása** elemet, és adja meg a _MyResourceGroup_.|
    |Hely       | Adja meg az _USA keleti_ régióját.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>DDoS-védelem engedélyezése virtuális hálózathoz

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>DDoS-védelem engedélyezése új virtuális hálózat esetén

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki a következő értékeket, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás** lehetőséget:

    | Beállítás         | Érték                                           |
    | ---------       | ---------                                       |
    | Név            | Adja meg a _MyVnet_.                                 |
    | Előfizetés    | Válassza ki előfizetését.                                    |
    | Erőforráscsoport  | Válassza a **meglévő használata** lehetőséget, majd a **MyResourceGroup** lehetőséget. |
    | Hely        | Adja meg az _USA keleti_ régióját                                                    |
    | DDoS Protection Standard | Válassza az **Engedélyezés** lehetőséget. A kiválasztott csomag a virtuális hálózattal megegyező vagy eltérő előfizetésben is lehet, de mindkét előfizetéshez ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.|

A virtuális hálózat nem helyezhető át másik erőforráscsoporthoz vagy előfizetésbe, ha a DDoS standard engedélyezve van a virtuális hálózathoz. Ha a virtuális hálózatot a DDoS standard használatával kell áthelyeznie, először tiltsa le a DDoS standardot, helyezze át a virtuális hálózatot, majd engedélyezze a DDoS standard használatát. Az áthelyezést követően a rendszer alaphelyzetbe állítja a virtuális hálózatban lévő összes védett nyilvános IP-cím automatikusan beállított szabályzatának küszöbértékeit.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>DDoS-védelem engedélyezése meglévő virtuális hálózat esetén

1. Hozzon létre egy DDoS Protection-tervet a [DDoS Protection-terv létrehozása](#create-a-ddos-protection-plan)című cikk lépéseinek végrehajtásával, ha nem rendelkezik meglévő DDoS Protection-csomaggal.
2. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
3. Adja meg annak a virtuális hálózatnak a nevét, amely számára engedélyezni szeretné a DDoS Protection a standard értéket a portál tetején található **erőforrások, szolgáltatások és dokumentumok keresése mezőbe** . Ha a virtuális hálózat neve megjelenik a keresési eredmények között, válassza ki.
4. A **Beállítások** területen válassza a **DDoS Protection** lehetőséget.
5. Válassza a **standard** lehetőséget. A **DDoS elleni védelmi terv** területen válasszon ki egy meglévő DDoS Protection-csomagot, vagy az 1. lépésben létrehozott csomagot, majd kattintson a **Mentés** gombra. A kiválasztott csomag a virtuális hálózattal megegyező vagy eltérő előfizetésben is lehet, de mindkét előfizetéshez ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>DDoS-védelem engedélyezése minden virtuális hálózat esetében

Ez a [házirend](https://aka.ms/ddosvnetpolicy) egy meghatározott hatókörben lévő olyan virtuális hálózatokat észlel, amelyeken nincs DDoS Protection standard szintű beállítás, majd opcionálisan létrehozhat egy szervizelési feladatot, amely létrehozza a VNet elleni társítást. A szabályzat központi telepítésének részletes ismertetését lásd: https://aka.ms/ddosvnetpolicy-techcommunity .

## <a name="validate-and-test"></a>Ellenőrzés és tesztelés

Először olvassa el a DDoS Protection-terv részleteit:

1. Válassza a **Minden szolgáltatás** lehetőséget a portál bal felső részén.
2. A **szűrő** mezőbe írja be a *DDoS* értéket. Ha a **DDoS Protection-csomagok** megjelennek az eredmények között, válassza ki.
3. Válassza ki a DDoS Protection-tervet a listából.

A _MyVnet_ virtuális hálózatnak szerepelnie kell a felsorolásban. 

### <a name="view-protected-resources"></a>Védett erőforrások megtekintése
A **védett erőforrások** területen megtekintheti a védett virtuális hálózatokat és a nyilvános IP-címeket, vagy további virtuális hálózatokat adhat hozzá a DDoS Protection-tervhez:

![Védett erőforrások megtekintése](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő oktatóanyagban megtarthatja az erőforrásait. Ha már nincs rá szükség, törölje a _MyResourceGroup_ erőforráscsoportot. Az erőforráscsoport törlésekor a DDoS elleni védelmi tervet és az ahhoz kapcsolódó összes erőforrást is törli. Ha nem kívánja használni ezt a DDoS Protection-csomagot, távolítsa el az erőforrásokat a szükségtelen költségek elkerülése érdekében.

   >[!WARNING]
   >Ez a művelet Irreversable.

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportot**, vagy válassza az **erőforráscsoportok** lehetőséget a Azure Portal menüből.

2. A _MyResourceGroup_ erőforráscsoport megkereséséhez szűrje vagy görgessen lefelé.

3. Válassza ki az erőforráscsoportot, majd válassza az **erőforráscsoport törlése** elemet.

4. Írja be az erőforráscsoport nevét az ellenőrzéshez, majd válassza a **Törlés** lehetőséget.

A DDoS-védelem letiltása virtuális hálózat esetén: 

1. Adja meg annak a virtuális hálózatnak a nevét, amelyre le szeretné tiltani a DDoS Protection-szabványt a portál tetején található **erőforrások, szolgáltatások és dokumentumok keresése mezőbe** . Ha a virtuális hálózat neve megjelenik a keresési eredmények között, válassza ki.
2. Válassza **a DDoS Protection standard**, majd a **Letiltás** lehetőséget.

Ha törölni szeretné a DDoS Protection-csomagot, először el kell távolítania az összes virtuális hálózatot. 

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan tekintheti meg és konfigurálhatja a DDoS elleni védelmi terv telemetria, folytassa az oktatóanyagokkal.

> [!div class="nextstepaction"]
> [DDoS Protection-telemetria megtekintése és konfigurálása](telemetry.md)
