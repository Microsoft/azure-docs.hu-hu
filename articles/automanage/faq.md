---
title: Azure Automanage gépekre vonatkozó gyakori kérdések
description: Válaszok a virtuális gépek Azure Automanage kapcsolatos gyakori kérdésekre.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: f5a9ff7661fda372631d1bb912b1c137b37c7e07
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363359"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Gyakori kérdések a virtuális Azure Automanage kapcsolatos kérdésekről

Ez a cikk a virtuális gépek Azure Automanage [kapcsolatos leggyakoribb kérdésekre ad választ.](automanage-virtual-machines.md)

Ha a cikk nem foglalkozik az Azure-ral kapcsolatos probléma megoldásával, keresse fel az Azure fórumait az MSDN-ről, és [Stack Overflow.](https://azure.microsoft.com/support/forums/) A problémát közzétenheti ezeken a fórumokon, vagy közzétenheti a [ @AzureSupport Twitteren.](https://twitter.com/AzureSupport) Elküldhet egy kérést Azure-támogatás is. Ha támogatási kérést küld, a Azure-támogatás [oldalán válassza](https://azure.microsoft.com/support/options/)a **Támogatás kérése lehetőséget.**


## <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage virtuális gépekhez

**Milyen előfeltételek szükségesek a Azure Automanage?**

A következő előfeltételek a Azure Automanage:
- Támogatott [Windows Server-verziók](automanage-windows-server.md#supported-windows-server-versions) és [Linux-disztribúciók](automanage-linux.md#supported-linux-distributions-and-versions)
- A virtuális gépeknek támogatott régióban kell lennie
- A felhasználónak megfelelő engedélyekkel kell rendelkeznie
- Csak nem méretezési halmazú virtuális gépek
- Az automatikus automatizálás jelenleg nem támogatja a sandbox-előfizetéseket

**Milyen Azure RBAC-engedélyre van szükség az automatikus automatizálás engedélyezéséhez?**

Ha egy meglévő automanage-fiókkal rendelkezik egy virtuális gépen az automatikus automatizálás engedélyezését, közreműködői szerepkörre lesz szüksége ahhoz az erőforráscsoporthoz, amelyben a virtuális gép található.

Ha új automatikus felügyeleti fiókot használ az engedélyezéskor, akkor tulajdonosi vagy Közreműködő + Felhasználói hozzáférés-rendszergazda szerepkört kell használnia az előfizetéshez.


**Milyen régiók támogatottak?**

A támogatott régiók teljes listája itt [érhető el.](./automanage-virtual-machines.md#supported-regions)


**Mely képességeket Azure Automanage automatizálni?**

Az itt felsorolt szolgáltatások automatikus regisztrációja, konfigurálása és figyelés a virtuális gép életciklusa [során.](automanage-virtual-machines.md)

**Működik Azure Automanage a Azure Arc virtuális gépekkel?**

Az automatikus automatizálás jelenleg nem támogatja az Arc-kompatibilis virtuális gépeket.

**Testre szabhatom a konfigurációkat a Azure Automanage?**

Az ügyfelek konfigurációs beállítások segítségével testreszabhatják adott szolgáltatások beállításait, például Azure Backup megőrzési beállításokat. A módosítható beállítások teljes listáját az itt található [dokumentációban találhatja meg.](automanage-virtual-machines.md#customizing-an-environment-using-preferences)


**Működik Azure Automanage Linux és Windows rendszerű virtuális gépekkel is?**

Igen, tekintse meg a [támogatott Windows Server-verziókat](automanage-windows-server.md#supported-windows-server-versions) és [Linux-disztribúciókat.](automanage-linux.md#supported-linux-distributions-and-versions)


**Alkalmazhatom szelektíven az automatikus automatizálni a virtuális gépek egy halmazát?**

Az automatikus automatizálható kattintással és egyszerűséggel a kiválasztott új és meglévő virtuális gépeken. Az automatikus automatizálható bármikor letiltható.


**Támogatja Azure Automanage virtuálisgép-méretezési készletben lévő virtuális gépeket?**

Nem, Azure Automanage virtuálisgép-méretezési készletben lévő virtuális gépek jelenleg nem támogatottak.


**Mennyibe kerül Azure Automanage?**

Azure Automanage a nyilvános előzetes verzióban további költségek nélkül érhető el. A csatolt Azure-erőforrások, például Azure Backup, költségeket fognak felszámításra.


**Alkalmazhatom az automatikus szolgáltatásokat az Azure Policyn keresztül?**

Igen, van egy beépített szabályzatunk, amely automatikusan alkalmazza az automatikus adatokat a meghatározott hatókörben található összes virtuális gépre. A környezet konfigurációját (DevTest vagy Production) is meg kell adnia az Automanage-fiókkal együtt. Az Automatikus automatizálás Azure Policyvel való engedélyezéséről itt [olvashat bővebben.](virtual-machines-policy-enable.md)


**Mi az az automatikus fiók?**

Az automatikus felügyelet fiókja egy MSI (felügyeltszolgáltatás-identitás), amely biztosítja azt a biztonsági környezetet vagy identitást, amelyen az automatikus műveletek le vannak végezve.


**Az Automatikus automatizálás engedélyezése hatással van a kiválasztott virtuális gépeken kívül további virtuális gépekre is?**

Ha a virtuális gépe egy meglévő Log Analytics-munkaterülethez van kapcsolva, a munkaterületet újra felhasználjuk a következő megoldások alkalmazásával: Change Tracking, Inventory és Update Management. A munkaterülethez csatlakoztatott összes virtuális gépen engedélyezve lesznek ezek a megoldások.


**Módosít tudom a virtuális gépem környezetét?**

Ekkor le kell tiltania a virtuális gép automanage funkcióját, majd újra engedélyeznie kell az Automatikus automatizálás beállítást a kívánt környezettel és beállításokkal.


**Ha a virtuális gépem már konfigurálva van egy szolgáltatáshoz, például a Update Management, az Automanage újrakonfigurálja?**
Nem, az automatikus automatizálás nem konfigurálja újra. Elkezdjük figyelni a szolgáltatáshoz társított erőforrások eltérését.


**Miért van Sikertelen állapotú a virtuális gépem az Automatikus portálon?**

Ha a Sikertelen állapotot *látja,* az üzembe helyezés hibaelhárítása többféleképpen is lehetséges:
* Az **Erőforráscsoportok menüben** válassza ki  az erőforráscsoportot, kattintson az Üzemelő példányok elemre, és itt tekintse meg a Sikertelen állapotot a hiba részleteivel együtt. 
* Az **Előfizetések menüben** válassza ki az erőforráscsoportot, kattintson az Üzemelő **példányok** elemre, és itt tekintse meg a Sikertelen állapotot a hiba részleteivel együtt. 
* Felkeresheti egy virtuális gép tevékenységnaplóját is, amely a "Konfigurációs profil-hozzárendelések létrehozása vagy frissítése" bejegyzést fogja tartalmazni. Ez további részleteket is tartalmazhat az üzemelő példányról.

**Hogyan kaphatok hibaelhárítási támogatást az automanage-hez?**

Műszaki támogatási jegyet is [be lehet nyújtatni.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) A Szolgáltatás **beállításnál** keresse meg és válassza az *Automatikus kezelés lehetőséget* a *Figyelés és felügyelet szakaszban.*


## <a name="next-steps"></a>Következő lépések

Próbálja meg engedélyezni a virtuális gépek automatikus Azure Portal.

> [!div class="nextstepaction"]
> [A virtuális gépek automatikus Azure Portal](quick-create-virtual-machines-portal.md)