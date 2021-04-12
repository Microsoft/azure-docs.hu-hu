---
title: Gyakori hibák és ismert problémák az Azure Cloud Services-ba való Migrálás során (kiterjesztett támogatás)
description: A Cloud Services (klasszikus) rendszerről a Cloud Service-be való Migrálás gyakori hibáinak áttekintése (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286943"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Gyakori hibák és ismert problémák az Azure Cloud Services-ba való Migrálás során (kiterjesztett támogatás)

Ez a cikk a Cloud Services (klasszikus) és a Cloud Services (bővített támogatás) közötti áttelepítés során előforduló ismert problémákat és gyakori hibákat ismerteti. 

## <a name="known-issues"></a>Ismert problémák
A következő problémák ismertek és kezelhetők.

| Ismert problémák | Kockázatcsökkentés | 
|---|---|
| A sikeres véglegesítés után a szerepkör-példányok a UD-t a UD újraindítják. | Az újraindítási művelet ugyanazt a módszert követi, mint a vendég operációs rendszerek havi bevezetése. Ne véglegesítse a felhőalapú szolgáltatások egyetlen szerepkör-példánnyal való áttelepítését, vagy az újraindítással.| 
| Azure Portal a böngésző frissítése után nem lehet beolvasni az áttelepítési állapotot. | Futtassa újra az érvényesítési és előkészítési műveletet, hogy visszaálljon az eredeti áttelepítési állapotba. | 
| A tanúsítvány titkos erőforrásként jelenik meg a Key vaultban. | Az áttelepítés után töltse fel újra a tanúsítványt tanúsítvány-erőforrásként a frissítési művelet leegyszerűsítéséhez Cloud Services (kiterjesztett támogatás). | 
| A telepítési címkék nem lettek felmentve címkékként a Migrálás részeként. | A címkéket manuálisan hozza létre az áttelepítés után, hogy fenntartsa ezeket az információkat.
| Az erőforráscsoport neve minden sapkában található. | Nem befolyásoló. A megoldás még nem érhető el. |
| A zárolás neve Cloud Services (bővített támogatás) zárolása helytelen. | Nem befolyásoló. A megoldás még nem érhető el. | 
| Az IP-cím neve helytelen Cloud Services (bővített támogatás) portál paneljén. | Nem befolyásoló. A megoldás még nem érhető el. | 
| Érvénytelen DNS-név jelenik meg a virtuális IP-címhez az áttelepített felhőalapú szolgáltatás frissítési művelete után. | Nem befolyásoló. A megoldás még nem érhető el. | 
| A sikeres előkészítést követően az új Cloud Services (kiterjesztett támogatás) üzemelő példányának összekapcsolása nem engedélyezett. | Ne csatlakoztasson egy új felhőalapú szolgáltatást egy előkészített felhőalapú szolgáltatáshoz. | 
| A hibaüzeneteket frissíteni kell. | Nem befolyásoló. | 

## <a name="common-migration-errors"></a>Gyakori áttelepítési hibák
Gyakori áttelepítési hibák és kockázatcsökkentő lépések. 

| Hibaüzenet | Részletek | 
|---|---|
| Az erőforrás típusa nem található a (z) `Microsoft.Compute` "2020-10-01-Preview" API-verzió névterében. | [Regisztrálja a CloudServices szolgáltatás előfizetését](in-place-migration-overview.md#setup-access-for-migration) a nyilvános előzetes verzió eléréséhez. | 
| A kiszolgáló belső hibát észlelt. Próbálja megismételni a kérelmet. | Próbálja megismételni a műveletet, vagy használja [a Microsoft Q&a vagy az](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ügyfélszolgálat segítségét. | 
| A kiszolgáló váratlan hibát észlelt a felhőalapú szolgáltatás hálózati erőforrásainak lefoglalására tett kísérlet során. Próbálja megismételni a kérelmet. | Próbálja megismételni a műveletet, vagy használja [a Microsoft Q&a vagy az](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ügyfélszolgálat segítségét. | 
| Központi telepítés központi telepítése – a Cloud Service Cloud-Service-Name értékének az áttelepítendő virtuális hálózaton belül kell lennie. | A központi telepítés nem egy virtuális hálózatban található. További részletekért tekintse meg [ezt a](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentumot. | 
| Az üzembe helyezés központi telepítési példányának áttelepítése – név a Cloud Service Cloud szolgáltatásban – a name nem támogatott, mert a régió régiójának neve. Engedélyezett régiók: [elérhető régiók listája]. | A régió migrálása még nem támogatott. | 
| A központi telepítés központi telepítése – név a Cloud Service-beli felhőben – a szolgáltatás neve nem telepíthető át, mert nincsenek hozzárendelve alhálózatok a szerepkör (ek) szerepkörhöz. Társítsa az összes szerepkört egy alhálózattal, majd próbálja megismételni a felhőalapú szolgáltatás áttelepítését. | Frissítse a Cloud Service (klasszikus) üzemelő példányt úgy, hogy az áttelepítést megelőzően egy alhálózaton helyezi el. |  
| A központi telepítés központi telepítése – név a Cloud Service-beli felhőben – a szolgáltatás neve nem telepíthető át, mert a központi telepítéshez legalább egy olyan szolgáltatás szükséges, amely nincs regisztrálva az előfizetésben Azure Resource Manager. Regisztrálja az összes szükséges funkciót az üzemelő példány áttelepítéséhez. Hiányzó funkció (k): [hiányzó szolgáltatások listája]. | Forduljon az ügyfélszolgálathoz, és kérje le a regisztrált funkciók jelzőit. | 
| A központi telepítés nem telepíthető át, mert az üzemelő példány felhőalapú szolgáltatása két foglalt tárolóhelytel rendelkezik. A Cloud Services áttelepítése csak olyan központi telepítések esetén támogatott, amelyek csak a felhőalapú szolgáltatásban üzemelnek. A központi telepítés áttelepítésének folytatásához törölje a Felhőbeli szolgáltatás másik központi telepítését. | További részletekért tekintse meg a nem [támogatott forgatókönyvek](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) listáját. | 
| Központi telepítés központi telepítése – a üzemeltetett Cloud-Service-Name neve közbenső állapotban van: állapot. Az áttelepítés nem engedélyezett. | Az üzemelő példány létrehozása, törlése vagy frissítése folyamatban van. Várjon, amíg a művelet befejeződik, és próbálkozzon újra. | 
| Az üzemelő példány központi telepítése-neve az üzemeltetett szolgáltatásban (Cloud-Service-Name) fenntartott IP-címmel rendelkezik, de nincs fenntartott IP-név. A probléma megoldásához frissítse a fenntartott IP-címet, vagy lépjen kapcsolatba a Microsoft Azure ügyfélszolgálatával. | A Cloud Service üzembe helyezésének frissítése. | 
| A központi telepítés központi telepítése – az üzemeltetett szolgáltatásbeli Cloud-Service-Name szolgáltatásban fenntartott IP-címek vannak fenntartva – IP-név, de nincs végpont a fenntartott IP-címen. A probléma megoldásához adjon hozzá legalább egy végpontot a fenntartott IP-címhez. | Végpont hozzáadása a fenntartott IP-címhez. | 
| Az üzemelő példánynak a üzemeltetett-ben történő áttelepítése {0} {1} véglegesítés alatt áll, és nem módosítható, amíg a művelet sikeresen be nem fejeződik.  | Várjon vagy próbálkozzon újra a művelettel. | 
| Az üzemelő példánynak a üzemeltetett-ben történő áttelepítése {0} {1} megszakítás alatt áll, és nem módosítható, amíg a művelet sikeresen be nem fejeződik. | Várjon vagy próbálkozzon újra a művelettel. |
| A üzemeltetett-ben üzemelő példányban egy vagy több virtuális gép {0} {1} frissítési művelettel működik. Nem telepíthető át, amíg az előző művelet sikeresen be nem fejeződik. Némi várakozás után próbálkozzon újra. | Várjon, amíg a művelet befejeződik. | 
| Az áttelepítés nem támogatott a üzemeltetett-ben való központi telepítéshez {0} , {1} mert az a következő funkciókat használja az áttelepítés során még nem támogatott: nem vnet telepítés.| A központi telepítés nem egy virtuális hálózatban található. További részletekért tekintse meg [ezt a](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentumot. | 
| A virtuális hálózat neve nem lehet null értékű vagy üres. | Adja meg a virtuális hálózat nevét a REST-kérelem törzsében | 
| Az alhálózat neve nem lehet null értékű vagy üres. | Adja meg az alhálózat nevét a REST-kérelem törzsében. | 
| A DestinationVirtualNetwork az alábbi értékek egyikére kell beállítani: default, New vagy meglévő. | Adja meg a DestinationVirtualNetwork tulajdonságot a REST-kérelem törzsében. | 
| Az alapértelmezett VNet cél beállítás nincs implementálva. | Az "alapértelmezett" érték nem támogatott a REST-kérelem törzsében lévő DestinationVirtualNetwork tulajdonság esetében. | 
| A központi telepítés {0} nem telepíthető át, mert a CSPKG nem érhető el. | Frissítse a központi telepítést, és próbálkozzon újra. | 
| A (z) "" AZONOSÍTÓJÚ alhálózat egy másik helyen található, mint a (z) "" {0} {1} üzemeltetett szolgáltatásban lévő "" központi telepítés {2} . Az alhálózat helye: " {3} ", és az üzemeltetett szolgáltatás helye: " {4} ".  A központi telepítéssel megegyező helyen lévő alhálózatot kell megadnia. | Frissítse a Cloud Service-t úgy, hogy az az alhálózat és a felhőalapú szolgáltatás is legyen ugyanazon a helyen, az áttelepítés előtt. | 
| Az üzemelő példánynak a üzemeltetett-ben történő áttelepítése {0} {1} megszakítás alatt áll, és nem módosítható, amíg a művelet sikeresen be nem fejeződik. | Várjon, amíg a megszakítás befejeződik, vagy próbálja megismételni a megszakítást. Használja [A Microsoft Q&A vagy az](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ügyfélszolgálat segítségét. | 
| A {0} üzemeltetett-ben való üzembe helyezés {1} nem készült el az áttelepítéshez. | Futtassa a felkészülést a Cloud Service-ben a végrehajtási művelet futtatása előtt. | 
| UnknownExceptionInEndExecute: a szerződés. állítása sikertelen volt: a rgName null értékű vagy üres: kivétel érkezett a EndExecute-ben, amely nem RdfeException. |   Használja [A Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) -t, vagy forduljon az ügyfélszolgálathoz. | 
| UnknownExceptionInEndExecute: A feladat meg lett szakítva: a kivétel olyan EndExecute érkezett, amely nem RdfeException. | Használja [A Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) -t, vagy forduljon az ügyfélszolgálathoz. | 
| XrpVirtualNetworkMigrationError: a virtuális hálózat áttelepítési hibája. | Használja [A Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) -t, vagy forduljon az ügyfélszolgálathoz. | 
| A {0} üzemeltetett-ben való üzembe helyezés {1}  Virtual Networkhoz tartozik {2} . Telepítse át Virtual Network áttelepíteni a {2} üzemeltetett {1} . | Tekintse át [Virtual Network áttelepítést](in-place-migration-technical-details.md#virtual-network-migration). | 
| A Azure Resource Managerban található erőforrás-név jelenlegi kvótája nem elegendő az áttelepítés befejezéséhez. A jelenlegi kvóta a {0} további szükséges {1} . Egy támogatási kérést küld a kvóta növelésére, majd próbálja megismételni az áttelepítést a kvóta kiváltása után.    | A kvóták növelését kérő megfelelő csatornák követése: <br>[A hálózati erőforrások kvótájának növekedése](../azure-portal/supportability/networking-quota-requests.md) <br>[A számítási erőforrások kvótájának növekedése](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Következő lépések
Az áttelepítés követelményeivel kapcsolatos további információkért lásd: az [Azure Cloud Servicesba való áttelepítés technikai részletei (bővített támogatás)](in-place-migration-technical-details.md)
