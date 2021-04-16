---
title: Rendelkezésre állási készletek áttekintése
description: Az Azure rendelkezésre állási készletekkel kapcsolatos tudnivalók
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530549"
---
# <a name="availability-sets-overview"></a>Rendelkezésre állási készletek áttekintése

Ez a cikk áttekintést nyújt az Azure-beli virtuális gépek rendelkezésre állási funkcióiról.

## <a name="what-is-an-availability-set"></a>Mi az a rendelkezésre állási csoport? 

A rendelkezésre állási csoport a virtuális gépek logikus csoportosítását jelenti, ami lehetővé teszi az Azure számára az alkalmazás felépítésének megértését a redundancia és a rendelkezésre állás biztosításához. Azt javasoljuk, hogy egy rendelkezésre állási készleten belül két vagy több virtuális gép létrehozása a magas rendelkezésre állású alkalmazások és a [99,95%-os Azure SLA nak való megfelelése érdekében.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Magának a rendelkezésre állási csoportnak nincs költsége, csak a létrehozott virtuálisgép-példányokért kell fizetnie.

## <a name="how-do-availability-sets-work"></a>Hogyan működnek a rendelkezésre állási készletek?
A mögöttes Azure-platform a  rendelkezésre állási  csoport minden virtuális gépéhez hozzárendel egy frissítési tartományt és egy tartalék tartományt. Minden rendelkezésre állási készlet legfeljebb három tartalék és húsz frissítési tartománysal konfigurálható. A frissítési tartományok olyan virtuális gépek és mögöttes fizikai hardverek csoportjait jelölik, amelyek egyidejűleg indíthatók újra. Ha egy rendelkezésre állási csoportba ötnél több virtuális gép van konfigurálva, a hatodik virtuális gép az elsővel azonos frissítési tartományba kerül, a hetedik a másodikkal és így tovább. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre. Az újraindított frissítési tartománynak 30 perce van, hogy helyreálljon, mielőtt a karbantartás elkezdődik egy másik frissítési tartományon.

A tartalék tartományok azonos tápforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportjai. Alapértelmezés szerint a rendelkezésre állási készletben konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve. Bár a virtuális gépek rendelkezésre állási csoportokba rendezése nem védi az alkalmazást az operációs rendszerre vezethető vagy az alkalmazásspecifikus hibáktól, korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy tápellátás-megszakadások hatását.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="A különböző számítási fürtök tartalék tartományokra való felosztását bemutató ábra, amelyeken belül több frissítési tartomány található":::

A virtuális gépek a lemezes tartalék tartományokhoz is igazodnak. Ez az igazítás biztosítja, hogy a virtuális géphez csatolt összes felügyelt lemez ugyanabban a tartalék tartományban található. 

Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagram a lemezek és virtuális gépek tartalék tartományai közötti igazításról.":::

## <a name="next-steps"></a>Következő lépések
Mostantól a saját Azure-környezetében is használhatja ezeket a rendelkezésre állási és redundanciával kapcsolatos szolgáltatásokat. Javasoljuk, hogy tájékozódjon [az Azure rendelkezésre állásával kapcsolatos ajánlott eljárásokról](/azure/architecture/checklist/resiliency-per-service).

