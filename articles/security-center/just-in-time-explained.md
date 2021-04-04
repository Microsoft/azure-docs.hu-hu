---
title: A virtuális gépek igény szerinti elérésének megértése Azure Security Center
description: Ez a dokumentum azt ismerteti, hogy az Azure Security Center az Azure-beli virtuális gépekhez való hozzáférés szabályozása miként szabályozható
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93285730"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Az igény szerinti (JIT) virtuálisgép-hozzáférés ismertetése

Ez az oldal ismerteti a Azure Security Center igény szerinti (JIT) virtuálisgép-hozzáférési funkciójának alapelveit, valamint az ajánlás mögötti logikát.

Ha meg szeretné tudni, hogyan alkalmazhatja a JIT-t a virtuális gépekre a Azure Portal (Security Center vagy Azure Virtual Machines) vagy a programozott módon használatával, tekintse meg a [felügyeleti portok biztonságossá tétele a JIT](security-center-just-in-time.md)segítségével című témakört.


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>A virtuális gépen futó felügyeleti portok kockázata

A fenyegetésekkel foglalkozó szereplők aktívan vadásznak a nyílt felügyeleti portokkal (például RDP vagy SSH) rendelkező, elérhető gépekről. Az összes virtuális gép potenciális célpont a támadásoknak. Ha egy virtuális gép biztonsága sikeres, a rendszer belépési pontként használja a környezetében lévő további erőforrások támadásához.



## <a name="why-jit-vm-access-is-the-solution"></a>Miért érdemes a JIT VM-hozzáférést választani a megoldáshoz 

Ahogy az összes kiberbiztonsági-megelőzési módszer esetében, a cél az, hogy csökkentse a támadási felületet. Ebben az esetben az azt jelenti, hogy kevesebb nyitott portot, különösen a felügyeleti portokat kell megnyitnia.

A legitim felhasználók ezeket a portokat is használják, ezért nem célszerű megtartani őket.

Ennek a dilemmának a megoldásához Azure Security Center JIT-t kínál. A JIT használatával zárolhatja a virtuális gépek bejövő forgalmát, így csökkentve a támadásoknak való kitettséget, miközben könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Hogyan működik a JIT a hálózati biztonsági csoportokkal és a Azure Firewallokkal

Ha engedélyezi a virtuális gépek igény szerinti elérését, kiválaszthatja a virtuális gépen azokat a portokat, amelyeken a bejövő forgalom le lesz tiltva. Security Center biztosítja, hogy a [hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) és a [Azure Firewall szabályok](../firewall/rule-processing.md)a kiválasztott portokra vonatkozó összes bejövő forgalmat megtagadva legyenek. Ezek a szabályok korlátozzák az Azure-beli virtuális gépek felügyeleti portjaihoz való hozzáférést, és megvédik azokat a támadásokkal szemben. 

Ha már léteznek más szabályok a kiválasztott portokhoz, akkor a meglévő szabályok prioritást élveznek az új "minden bejövő forgalom tiltása" szabályban. Ha nincsenek meglévő szabályok a kiválasztott portokon, akkor az új szabályok elsődleges prioritást kapnak a NSG és a Azure Firewall.

Amikor egy felhasználó hozzáférést kér egy virtuális géphez, Security Center ellenőrzi, hogy a felhasználó rendelkezik [-e Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) az adott virtuális géphez. Ha a kérést jóváhagyják, Security Center konfigurálja a NSG és a Azure Firewall, hogy engedélyezze a bejövő forgalmat a kiválasztott portokra a megfelelő IP-címről (vagy tartományból) a megadott időtartamra. Az idő lejárta után Security Center visszaállítja a NSG az előző állapotokra. A már létrehozott kapcsolatok nincsenek megszakítva.

> [!NOTE]
> A JIT nem támogatja a [Azure Firewall Manager](../firewall-manager/overview.md)által vezérelt Azure-tűzfalak által védett virtuális gépeket.




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Hogyan Security Center azonosítani, hogy mely virtuális gépekre van telepítve a JIT alkalmazás

Az alábbi ábra azt a logikát mutatja be, amelyet a Security Center a támogatott virtuális gépek kategorizálásának eldöntése során alkalmaz: 

[![Igény szerinti (JIT) virtuális gép (VM) logikai folyamata](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Amikor Security Center megtalál egy olyan gépet, amely kihasználhatja a JIT-t, hozzáadja az adott gépet az ajánlás nem megfelelő **állapotú erőforrásainak** lapjához. 

![Igény szerinti (JIT) virtuálisgép-hozzáférési javaslat](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>GYIK – a virtuális gépek igény szerinti elérésével kapcsolatos kérdések

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Milyen engedélyek szükségesek a JIT konfigurálásához és használatához?

A JIT megköveteli, hogy az [Azure Defender](defender-for-servers-introduction.md) engedélyezze a kiszolgálók számára az előfizetést. 

Az **olvasó** -és **SecurityReader** szerepkörök egyaránt megtekinthetik a JIT-állapotot és a paramétereket.

Ha a JIT-rel használható egyéni szerepköröket szeretne létrehozni, akkor az alábbi táblázatból kell megadnia az adatokat.

> [!TIP]
> Ha olyan felhasználók számára szeretne legkevesebb jogosultsági szintű szerepkört létrehozni, akiknek JIT-hozzáférést kell kérniük egy virtuális géphez, és nem kell más JIT-műveletet végezniük, használja a Security Center GitHub közösségi oldalain található [set-JitLeastPrivilegedRole szkriptet](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) .

| A felhasználók engedélyezése: | Beállított engedélyek|
| --- | --- |
|Egy virtuális gép JIT-szabályzatának konfigurálása vagy szerkesztése | *Rendelje hozzá ezeket a műveleteket a szerepkörhöz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Egy előfizetés vagy egy virtuális gép erőforráscsoport hatókörén: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|JIT-hozzáférés kérése egy virtuális géphez | *Rendelje hozzá ezeket a műveleteket a felhasználóhoz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Az előfizetés vagy az erőforráscsoport vagy a virtuális gép hatókörén:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Az előfizetés vagy az erőforráscsoport vagy a virtuális gép hatókörén:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|JIT-szabályzatok olvasása| *Rendelje hozzá ezeket a műveleteket a felhasználóhoz:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Következő lépések

Ez az oldal azt ismerteti, _hogy miért_ kell használni az igény szerinti (JIT) virtuális GÉPET (VM). 

Az útmutató cikkből megtudhatja, hogyan engedélyezheti a JIT-t, és hogyan kérhet hozzáférést a JIT-kompatibilis virtuális gépekhez:

> [!div class="nextstepaction"]
> [Felügyeleti portok biztonságossá tétele a JIT használatával](security-center-just-in-time.md)