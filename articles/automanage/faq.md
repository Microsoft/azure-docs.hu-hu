---
title: A Virtual Machines szolgáltatás Azure-beli autokezelése – gyakori kérdések
description: Válaszok a virtuális gépekre vonatkozó Azure-beli automanaged szolgáltatással kapcsolatos gyakori kérdésekre.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: b8f2b3e1ffde49c8df3c3a6d7c0b9b3751fda5fa
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696176"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Gyakori kérdések az Azure-beli automanage virtuális gépekről

Ez a cikk a [virtuális gépek Azure-beli automanage szolgáltatásával](automanage-virtual-machines.md)kapcsolatos leggyakoribb kérdésekre ad választ.

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az [MSDN webhelyen](https://azure.microsoft.com/support/forums/)található Azure-fórumokra, és stack overflow. Felteheti a problémát ezekben a fórumokon, vagy közzéteheti a [ @AzureSupport Twitteren](https://twitter.com/AzureSupport). Azure-támogatási kérést is küldhet. Ha támogatási kérést szeretne küldeni, az [Azure-támogatás lapon](https://azure.microsoft.com/support/options/)válassza a **támogatás** lekérése lehetőséget.


## <a name="azure-automanage-for-virtual-machines"></a>Azure-beli automata felügyelet virtuális gépeken

**Milyen előfeltételekre van szükség az Azure automanage szolgáltatás engedélyezéséhez?**

Az alábbi előfeltételek az Azure automanage szolgáltatás engedélyezésének előfeltételei:
- Csak Windows Server-alapú virtuális gépek
- A virtuális gépeknek támogatott régióban kell lenniük
- A felhasználónak megfelelő engedélyekkel kell rendelkeznie
- Csak a nem méretezési csoportba tartozó virtuális gépek
- Az automanage nem támogatja a homokozó-előfizetéseket.

**Milyen Azure RBAC engedélyre van szükség az automanage engedélyezéséhez?**

Ha egy meglévő automanage-fiókkal rendelkező virtuális gépen engedélyezi az automanage-alapú felügyeletet, akkor közreműködői szerepkörrel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyben a virtuális gép található.

Ha az engedélyezésekor új automanage-fiókot használ, rendelkeznie kell a tulajdonos szerepkörrel, vagy közreműködői + felhasználói hozzáférés rendszergazdai szerepkörrel kell rendelkeznie az előfizetéshez.


**Mely régiók támogatottak?**

A következő régiókban működő virtuális gépek támogatottak: Nyugat-Európa, USA keleti régiója, USA 2. nyugati régiója, Közép-Kanada, USA nyugati középső régiója, Kelet-Japán.


**Mely funkciók automatizálják az Azure automatikus felügyeletét?**

Az [itt](virtual-machines-best-practices.md)felsorolt szolgáltatások a virtuális gép életciklusa alatt beléptetések, konfigurálások és figyelők automatikus kezelése.

**Az Azure automanage együttműködik az Azure arc-kompatibilis virtuális gépekkel?**

Az automanage jelenleg nem támogatja az arc-kompatibilis virtuális gépeket.

**Testreszabhatók a konfigurációk az Azure automanage szolgáltatásban?**

Az ügyfelek a konfigurációs beállítások segítségével testre szabhatják az adott szolgáltatások beállításait, például a Azure Backup megőrzést. A módosítható beállítások teljes listájáért tekintse meg a dokumentációt [itt](virtual-machines-best-practices.md).


**Működik az Azure automanage a Linux és a Windows rendszerű virtuális gépekkel?**

Az automanage jelenleg a Windows Server Azure virtuális gépeket támogatja.


**Szelektíven is alkalmazhatom az automanage-t csak a virtuális gépek készletén?**

A kiválasztott új és meglévő virtuális gépeken a click és a Point egyszerűség lehetőséggel engedélyezhető az automanage szolgáltatás. Az automanage is bármikor letiltható.


**Támogatja az Azure automanage a virtuálisgép-méretezési csoportokban a virtuális gépeket?**

Nem, az Azure automanage jelenleg nem támogatja a virtuálisgép-méretezési csoportokban lévő virtuális gépeket.


**Mennyibe kerül az Azure automanaged Cost?**

Az Azure automanage szolgáltatás díjmentesen elérhető nyilvános előzetes verzióban. A csatlakoztatott Azure-erőforrások (például Azure Backup) költséggel járnak.


**Alkalmazhatom az Azure Policy használatával történő autofelügyeletet?**

Igen, van egy beépített szabályzatunk, amely automatikusan alkalmazza az automatikus felügyeletet a megadott hatókörön belüli összes virtuális gépre. A konfigurációs profilt (DevTest vagy éles környezet) is meg kell adnia az automatikus felügyelet fiókkal együtt. További információ az Azure Policy szolgáltatással való autofelügyelet engedélyezéséről [itt](virtual-machines-policy-enable.md).


**Mi az az automanage Account?**

Az automatikus felügyelet fiók egy MSI (Managed Service Identity), amely biztosítja a biztonsági környezetet, illetve azt az identitást, amely alatt az automatizált műveletek történnek.


**Az automanage engedélyezése esetén a kiválasztott virtuális gép (ek) mellett további virtuális gépekre is hatással van?**

Ha a virtuális gép egy meglévő Log Analytics-munkaterülethez van csatolva, a következő megoldások alkalmazásával fogjuk felhasználni a munkaterületet: Change Tracking, leltár és Update Management. Az adott munkaterülethez csatlakozó összes virtuális gépnek engedélyezve lesznek ezek a megoldások.


**Módosíthatom a virtuális gép konfigurációs profilját?**

Ekkor le kell tiltania a virtuális gép automatikus felügyeletét, majd újra engedélyeznie kell az automatikus felügyeletet a kívánt konfigurációs profillal és beállításokkal.


**Ha a virtuális gép már konfigurálva van egy szolgáltatáshoz (például Update Management), az automatikusan kezeli az újrakonfigurálást?**
Nem, az automanage nem konfigurálja újra. Elkezdjük figyelni a szolgáltatáshoz kapcsolódó erőforrásokat a drifthez.


**Miért van a virtuális gép hibás állapota az automanage portálon?**

Ha az állapotot *nem sikerült* megtekinteni, a virtuális gép által használt erőforráscsoport használatával elháríthatja a telepítést. Lépjen az **erőforráscsoportok** elemre, válassza ki az erőforráscsoportot, kattintson a **központi telepítések** elemre, és tekintse meg a *hibás* állapotot a hiba részleteivel együtt.

**Hogyan szerezhetem be az automanage hibaelhárítási támogatását?**

A [technikai támogatási esetekről szóló jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)is megadhat. A **szolgáltatás** beállításnál keresse meg és válassza ki az *automanage* ( *felügyelet és felügyelet) szakaszt a figyelés és kezelés* szakaszban.


## <a name="next-steps"></a>További lépések

Próbálja meg engedélyezni a Azure Portalban található virtuális gépek autofelügyeletét.

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)