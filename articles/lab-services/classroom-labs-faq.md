---
title: Labs Azure Lab Services – GYIK | Microsoft Docs
description: Ez a cikk a Azure Lab Services Labs szolgáltatással kapcsolatos gyakori kérdésekre (GYIK) ad választ.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437184"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Labs Azure Lab Services – gyakori kérdések (GYIK)
Választ kaphat a Azure Lab Services Labs szolgáltatásával kapcsolatos leggyakoribb kérdésekre. 

## <a name="quotas"></a>Kvóták

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Felhasználónként vagy hetente, vagy a labor teljes időtartama alatt a kvóta? 
A laborhoz beállított kvóta minden tanuló számára a labor teljes időtartamára vonatkozik. A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a felhasználó számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt.  A kvótákkal kapcsolatos további információkért lásd: [kvóták beállítása a felhasználók](how-to-configure-student-usage.md#set-quotas-for-users)számára.

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Ha a pedagógus bekapcsol egy tanuló virtuális gépet, akkor ez befolyásolja a tanulói kvótát? 
Nem. Semmilyet. Amikor a pedagógus bekapcsolja a tanuló virtuális gépet, nem befolyásolja a tanuló számára kiosztott kvótát. 

## <a name="schedules"></a>Ütemezések

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>A laborban lévő összes virtuális gép automatikusan elindul, ha be van állítva egy ütemterv? 
Nem. Nem minden virtuális gép. Csak azok a virtuális gépek, amelyek egy adott időpontban vannak hozzárendelve a felhasználókhoz. A nem a felhasználóhoz rendelt virtuális gépek nem indulnak el automatikusan. Ez a tervezés. 

## <a name="lab-accounts"></a>Tesztkörnyezetfiókok

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Miért nem tudok labort létrehozni, mert a címtartomány nem áll rendelkezésre. 

A Labs létrehozhat laboratóriumi virtuális gépeket egy olyan IP-címtartomány keretein belül, amelyet a Azure Portal a labor-fiók létrehozásakor határoz meg. Ha címtartomány van megadva, a rendszer minden olyan labort hoz létre, amelyet a rendszer a labor virtuális gépek 512 IP-címeinek kiosztotta után hozott létre. A Lab-fiókhoz tartozó címtartomány elég nagynak kell lennie ahhoz, hogy az összes, a labor-fiókban létrehozni kívánt labort befogadja. 

Ha például egy blokk/19-10.0.0.0/19, ez a címtartomány 8192 IP-címet és 16 Labs-t (8192/512 = 16 Labs) foglal magában. Ebben az esetben a labor létrehozása sikertelen a 17. labor létrehozásakor.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Milyen porttartomány-beállításokat kell megnyitnia a szervezetem tűzfal-beállításán, hogy az RDP/SSH használatával kapcsolódjon a labor virtuális gépekhez?

A portok a következők: 49152 – 65535. A Labs a terheléselosztó mögött található. Mindegyik tesztkörnyezet egyetlen nyilvános IP-címmel rendelkezik, és a tesztkörnyezet minden virtuális gépe egyedi porttal rendelkezik. 

Az egyes virtuális gépek magánhálózati IP-címét a Azure Portalban található labor kezdőlapjának **virtuálisgép-készlet** lapján is megtekintheti. Ha újra közzétesz egy labort, a tesztkörnyezet nyilvános IP-címe nem változik, de a laborban lévő egyes virtuális gépek magánhálózati IP-címe és portszáma is változhat. További információt a következő cikkben talál: [Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Milyen nyilvános IP-címtartományt nyitok meg a szervezetem tűzfal beállításai között, hogy az RDP/SSH-n keresztül csatlakozzanak a labor virtuális gépekhez?
Lásd: [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519), amely a nyilvános IP-címtartományt biztosítja az Azure-beli adatközpontok számára. Megnyithatja azon régiók IP-címeit, amelyeken a labor fiókjai vannak.

## <a name="virtual-machine-images"></a>Virtuálisgép-rendszerképek

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>A tesztkörnyezet létrehozója, miért nem engedélyezhető további képbeállítások a virtuálisgép-lemezképek legördülő menüjében új Labor létrehozásakor?

Ha egy rendszergazda labor-létrehozóként adja hozzá Önt labor-fiókhoz, a rendszer engedélyeket kap a Labs létrehozásához. Azonban nincs engedélye arra, hogy szerkessze a beállításokat a labor-fiókon belül, beleértve az engedélyezett virtuálisgép-rendszerképek listáját is. További lemezképek engedélyezéséhez lépjen kapcsolatba a labor-fiók rendszergazdájával, vagy kérje meg a rendszergazdát, hogy vegye fel Önt közreműködő szerepkörként a labor-fiókba. A közreműködői szerepkör lehetővé teszi a virtuális gép rendszerkép-listájának szerkesztését a labor-fiókban.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Tudok további lemezeket csatlakoztatni egy virtuális géphez?
Nem. a tantermi laborban nem lehet további lemezeket csatlakoztatni egy virtuális géphez. 

## <a name="users"></a>Felhasználók

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Hány felhasználó lehet egy osztályterem laborban?
Akár 400 felhasználót is hozzáadhat egy osztályterem laborhoz. 

## <a name="blog-post"></a>Blogbejegyzés
Fizessen elő a [Azure Lab Services blogra](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Frissítési értesítések
Fizessen elő a [labor Services frissítéseire](https://azure.microsoft.com/updates/?product=lab-services) , és tájékozódjon a labor Services új funkcióiról.

## <a name="general"></a>Általános kérdések
### <a name="what-if-my-question-isnt-answered-here"></a>Mi a teendő, ha nem válaszolt a kérdésre?
Ha a kérdés nem szerepel a listán, tudassa velünk, hogy segítsen megtalálni a választ.

- Tegye fel kérdéseit a GYIK végén. 
- Ha szélesebb közönséget szeretne elérni, tegye fel kérdéseit a [Azure Lab Services – technikai közösségi fórum](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- A szolgáltatási kérelmek esetében küldje el a kéréseit és ötleteit [Azure Lab Services – felhasználói hang](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

