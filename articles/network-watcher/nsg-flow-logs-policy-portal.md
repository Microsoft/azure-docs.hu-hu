---
title: Rövid útmutató – NSG-folyamatok naplófájljainak üzembe helyezése és kezelése Azure Policy használatával
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan használható a beépített szabályzatok a NSG-folyamatábrák üzembe helyezésének kezeléséhez
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011101"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Gyors útmutató: NSG-folyamatok naplófájljainak üzembe helyezése és kezelése Azure Policy használatával 

## <a name="overview"></a>Áttekintés
Az Azure Policy segít a szervezeti szabványok betartatásában és a megfelelőségi követelmények nagy léptékű kiértékelésében. A Azure Policy gyakori használati esetei közé tartozik az erőforrás-konzisztencia megvalósítása, a szabályozások megfelelősége, a biztonság, a költséghatékonyság és a felügyelet. Ebben a cikkben két beépített szabályzatot fogunk használni a NSG, amelyekkel kezelheti a flow-naplókat. Az első szabályzat minden olyan NSG jelölőt jelző, amely nem engedélyezett a flow-naplók nélkül. A második házirend automatikusan üzembe helyezi a NSG a flow-naplók nélkül, engedélyezve. 

Ha első alkalommal hoz létre Azure-szabályzatot, a következő módon olvashatja el: 
- [Az Azure Policy áttekintése](../governance/policy/overview.md) 
- [Oktatóanyag a szabályzat létrehozásához](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>A szabályzatok megkeresése
1. Ugrás a Azure Portal – [Portal.Azure.com](https://portal.azure.com) 

Navigáljon a Azure Policy lapra a keresési sáv felső részén ![ található házirend keresésével](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Lépjen a bal oldali ablaktábla **hozzárendelések** lapjára

![Hozzárendelések lap](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Kattintson a **házirend kiosztása** gombra 

![Házirend kiosztása gomb](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Az elérhető házirendek megjelenítéséhez kattintson a "házirend-definíciók" alatt látható három pont menüre.

5. Használja a típus szűrőt, és válassza a "beépített" lehetőséget. Ezután keressen rá a "flow log" kifejezésre.

A flow-naplók házirend-listájának két beépített szabályzata jelenik meg ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Válassza ki a hozzárendelni kívánt szabályzatot

- *"A flow-naplót úgy kell konfigurálni, hogy minden hálózati biztonsági csoport számára"* a nem megfelelő NSG jelző naplózási szabályzatot, amely nincs engedélyezve a NSG nélkül.
- *"A flow log-erőforrás üzembe helyezése a célként megadott hálózati biztonsági csoporttal"* a házirend egy telepítési művelettel, amely lehetővé teszi a folyamat naplóit az összes NSG a flow-naplók nélkül

Az alábbi házirendekhez külön utasítások vonatkoznak.  

## <a name="audit-policy"></a>Naplórend 

### <a name="how-the-policy-works"></a>A szabályzat működése

A házirend ellenőrzi a "Microsoft. Network/networkSecurityGroups" típusú összes meglévő ARM-objektumot, amely egy adott hatókör összes NSG megtekinti, és ellenőrzi, hogy létezik-e a csatolt folyamatok naplói a NSG flow logs tulajdonságával. Ha a tulajdonság nem létezik, a rendszer megjelöli a NSG.

Ha meg szeretné tekinteni a szabályzat teljes definícióját, látogasson el a [definíciók lapra](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) , és keressen rá a "flow naplók" kifejezésre, és keresse meg a szabályzatot.

### <a name="assignment"></a>Hozzárendelés

1. Adja meg a szabályzat részleteit

- Hatókör: az előfizetés a leggyakoribb választás, és az Ön számára megfelelő felügyeleti csoportot vagy erőforráscsoportot is kiválaszthat.  
- Házirend-definíció: a "szabályzatok keresése" szakaszban látható módon kell kiválasztani.
- AssignmentName: adjon meg egy leíró nevet 

2. A hozzárendelés áttekintéséhez kattintson a "felülvizsgálat + létrehozás" gombra.

A Szabályzathoz nem szükséges paraméter. A naplózási szabályzatok kiosztásakor nem kell kitöltenie a részleteket a "szervizelés" lapon.  

![Naplózási házirend áttekintése](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Results (Eredmények)

Az eredmények ellenőrzéséhez nyissa meg a megfelelőség lapot, és keresse meg a hozzárendelés nevét.
A szabályzat futása után a következő képernyőképhez hasonlónak kell megjelennie. Ha a házirend nem fut, várjon egy ideig. 

![Naplózási házirend eredményei](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Nem létező házirend telepítése 

### <a name="policy-structure"></a>Házirend szerkezete

A házirend ellenőrzi a "Microsoft. Network/networkSecurityGroups" típusú összes meglévő ARM-objektumot, amely egy adott hatókör összes NSG megtekinti, és ellenőrzi, hogy létezik-e a csatolt folyamatok naplói a NSG flow logs tulajdonságával. Ha a tulajdonság nem létezik, a házirend központilag telepíti a folyamat naplóját. 

Ha meg szeretné tekinteni a szabályzat teljes definícióját, látogasson el a [definíciók lapra](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) , és keressen rá a "flow naplók" kifejezésre, és keresse meg a szabályzatot. 

### <a name="assignment"></a>Hozzárendelés

1. Adja meg a szabályzat részleteit

- Hatókör: az előfizetés a leggyakoribb választás, és az Ön számára megfelelő felügyeleti csoportot vagy erőforráscsoportot is kiválaszthat.  
- Házirend-definíció: a "szabályzatok keresése" szakaszban látható módon kell kiválasztani.
- AssignmentName: adjon meg egy leíró nevet 

2. Házirend-paraméterek hozzáadása 

A Network Watcher szolgáltatás regionális szolgáltatás. Ezek a paraméterek lehetővé teszik a folyamat-naplók központi telepítésének végrehajtásához szükséges házirend-műveletet. 
- NSG régió: az Azure-régiók, amelyeken a szabályzatot megcélozták
- Storage ID: a Storage-fiók teljes erőforrás-azonosítója. Megjegyzés: a Storage-fióknak ugyanabban a régióban kell lennie, mint a NSG. 
- Network figyelők RG: az Network Watcher erőforrást tartalmazó erőforráscsoport neve. Ha nem lett átnevezve, megadhatja a "NetworkWatcherRG" értéket, amely az alapértelmezett.
- Network Watcher neve: a regionális Network Watcher szolgáltatás neve. Formátum: NetworkWatcher_RegionName. Példa: NetworkWatcher_centralus. Tekintse meg a teljes listát.

![ÉTKEZÉS házirend-paraméterek](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Szervizelés részleteinek hozzáadása

- A "szervizelési feladat létrehozása" jelölőnégyzet bejelölése, ha azt szeretné, hogy a házirend hatással legyen a meglévő erőforrásokra 
- A "felügyelt identitás létrehozása" lehetőséget már be kell jelölni
- A felügyelt identitáshoz tartozó korábbi helyet választotta 
- A szabályzat használatához közreműködői vagy tulajdonosi jogosultságok szükségesek. Ha rendelkezik ezekkel az engedélyekkel, nem talál hibát.

![A házirend szervizelése](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. A hozzárendelés áttekintéséhez kattintson a "felülvizsgálat + létrehozás" gombra, és a következő képernyőképhez hasonlóan kell megjelennie.

![VACSORÁZZON a szabályzatok áttekintésében](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Results (Eredmények)

Az eredmények ellenőrzéséhez nyissa meg a megfelelőség lapot, és keresse meg a hozzárendelés nevét.
A szabályzatnak a következő képernyőképen kell megjelennie. Ha a házirend nem fut, várjon egy ideig.

![A házirend eredményeinek megtekintése](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Következő lépések 

-   Ebben az [oktatóanyagban](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) mélyebben használhatja az ARM-sablonokat a flow-naplók és a Traffic Analytics üzembe helyezéséhez.
-   További információ a [Network Watcher](./index.yml)