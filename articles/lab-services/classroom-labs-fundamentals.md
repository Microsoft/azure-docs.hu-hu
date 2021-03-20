---
title: Az architektúra alapjai a Azure Lab Servicesban | Microsoft Docs
description: Ez a cikk a labor Services és a labor alapszintű architektúrája által használt alapvető erőforrásokat fedi le.
author: emaher
ms.topic: overview
ms.date: 09/16/2020
ms.author: enewman
ms.openlocfilehash: 71d59e8bcf7b4078255d6b119e9d62f366e46033
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437320"
---
# <a name="architecture-fundamentals-in-azure-lab-services"></a>Az architektúra alapjai a Azure Lab Servicesban

A Azure Lab Services egy SaaS (szoftveres szolgáltatás) megoldás, ami azt jelenti, hogy a labor Services által igényelt erőforrások kezelése megtörténik. Ez a cikk a labor Services és a labor alapszintű architektúrája által használt alapvető erőforrásokat fedi le.  

Azure Lab Services biztosít néhány olyan területet, amely lehetővé teszi a saját erőforrások használatát a labor Services szolgáltatással együtt.  További információ a virtuális gépek saját hálózaton történő használatáról: How to [peer a Virtual Network](how-to-connect-peer-virtual-network.md).  Ha a lemezképeket egy megosztott képkatalógusból szeretné használni, tekintse meg [a megosztott rendszerkép csatolása](how-to-attach-detach-shared-image-gallery.md)című témakört.

Alább látható egy osztályterem labor alapszintű architektúrája.  A labor-fiók az előfizetésében van tárolva. A tanuló virtuális gépeket, valamint a virtuális gépek támogatásához szükséges erőforrásokat a labor Services által birtokolt előfizetésben üzemeltetik. Ismerkedjen meg a labor szolgáltatás előfizetésével kapcsolatos részletes információkkal.

![Labs alapszintű architektúrája](./media/classroom-labs-fundamentals/labservices-basic-architecture.png)

## <a name="hosted-resources"></a>Üzemeltetett erőforrások

A tantermi labor futtatásához szükséges erőforrások a Microsoft által felügyelt Azure-előfizetések egyikében találhatók.  Az erőforrások közé tartozik az oktató sablonjának virtuális gépe, az egyes tanulók virtuális gépe, valamint a hálózattal kapcsolatos elemek, például a terheléselosztó, a virtuális hálózat és a hálózati biztonsági csoport.  Ezeket az előfizetéseket a rendszer gyanús tevékenységhez figyeli.  Fontos megjegyezni, hogy ezt a figyelést a virtuális gépeken a virtuálisgép-bővítmény vagy a hálózati mintázatok figyelése révén hajtják végre külsőleg.  Ha a [Leválasztás](how-to-enable-shutdown-disconnect.md) engedélyezve van, a virtuális gépen engedélyezve van a diagnosztikai bővítmény. A bővítmény lehetővé teszi, hogy a labor Services értesüljön a távoli asztal protokoll (RDP) munkamenet-megszakítási eseményéről.

## <a name="virtual-network"></a>Virtual Network

Minden tesztkörnyezet el van különítve a saját virtuális hálózatával.  Ha a laborban van egy [társ virtuális hálózat](how-to-connect-peer-virtual-network.md), az egyes laborokat a saját alhálózata elkülöníti.  A tanulók a terheléselosztó használatával csatlakoznak a virtuális géphez.  Nem rendelkezik nyilvános IP-címmel rendelkező tanulói virtuális gépekkel; csak privát IP-címmel rendelkeznek.  A tanuló kapcsolati karakterlánca a terheléselosztó nyilvános IP-címe, valamint egy 49152 és 65535 közötti véletlenszerű port.  A terheléselosztó bejövő szabályai az operációs rendszertől függően továbbítják a csatlakozást a 22-es (SSH) vagy a megfelelő virtuális gép 3389-as portja (RDP) felé. Egy NSG megakadályozza a más portokon kívüli adatforgalmat.

## <a name="access-control-to-the-virtual-machines"></a>Hozzáférés-vezérlés a virtuális gépekhez

A labor Services kezeli a tanulók azon képességét, hogy olyan műveleteket hajtsanak végre, mint a virtuális gépek indítása és leállítása.  A virtuális gép kapcsolati adataihoz való hozzáférést is szabályozza.

A labor Services a tanulók szolgáltatásban való regisztrációját is kezeli. Jelenleg két különböző hozzáférési beállítás van: korlátozott és nem korlátozott. További információkért lásd a labor- [felhasználók kezelése](how-to-configure-student-usage.md#send-invitations-to-users) című cikket. A korlátozott hozzáférés azt jelenti, hogy a labor szolgáltatások azt igazolják, hogy a tanulók felhasználóként lesznek hozzáadva a hozzáférés engedélyezése előtt. A nem korlátozott beállítás azt jelenti, hogy bármely felhasználó regisztrálhat, ha a regisztrációs hivatkozással rendelkezik, és a laborban van kapacitása. A nem korlátozott Hackathon események esetében hasznos lehet.

Az osztályterem laborban üzemeltetett tanulói virtuális gépekhez a tesztkörnyezet létrehozója által beállított Felhasználónév és jelszó tartozik.  A tesztkörnyezet létrehozója lehetővé teheti a regisztrált tanulók számára, hogy az első bejelentkezéskor saját jelszavukat válasszák.  

## <a name="next-steps"></a>Következő lépések

További információ a labor-szolgáltatásokban elérhető szolgáltatásokról: [Azure Lab Services fogalmak](classroom-labs-concepts.md) és [Azure Lab Services áttekintése](classroom-labs-overview.md).
