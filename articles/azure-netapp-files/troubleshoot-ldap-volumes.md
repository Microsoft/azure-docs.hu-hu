---
title: A Azure NetApp Files LDAP-kötetekkel kapcsolatos problémáinak elhárítása | Microsoft Docs
description: A Azure NetApp Files LDAP-köteteinek konfigurálásakor esetlegesen fellépett hibákra vonatkozó felbontásokat ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498989"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Az LDAP-kötetek problémáinak elhárítása

Ez a cikk az LDAP-kötetek konfigurálásakor esetlegesen fellépett hibákra vonatkozó megoldásokat ismerteti.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Az LDAP-kötetekkel kapcsolatos hibák és megoldások

|     Hiba feltételei    |     Megoldások    |
|-|-|
| Hiba történt az ldapEnabled as true értékkel rendelkező SMB-kötet létrehozásakor: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Nem hozhat létre olyan SMB-kötetet, amelyen engedélyezve van az LDAP. <br> SMB-kötetek létrehozása az LDAP által letiltva. |
| Hiba történt egy meglévő kötethez tartozó ldapEnabled-paraméter értékének frissítésekor: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  A kötet létrehozása után nem módosítható az LDAP-beállítás. <br> Ne frissítse az LDAP beállítás értékét egy létrehozott köteten. További részletekért lásd: [a configure LDAP használata kiterjesztett csoportokkal az NFS mennyiségi hozzáféréséhez](configure-ldap-extended-groups.md) . |
| Hiba történt az LDAP-kompatibilis NFS-kötet létrehozásakor: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  Ez a hiba azért fordul elő, mert a DNS nem érhető el. <br> <ul><li> Ellenőrizze, hogy a megfelelő helyet (a hely hatókörét) konfigurálta-e a Azure NetApp Fileshoz. </li><li> A DNS nem érhető el, ezért helytelen DNS IP-cím vagy hálózati probléma lehet. Ellenőrizze, hogy az AD-kapcsolatban megadott DNS IP-cím helyes-e. </li><li> Győződjön meg arról, hogy az AD és a kötet ugyanahhoz a régióhoz és azonos VNet van. Ha különböző virtuális hálózatok vannak, győződjön meg arról, hogy a két virtuális hálózatok között létrejött a VNet-társítás.</li></ul> |
| Hiba történt a kötet pillanatképből való létrehozásakor: <br> `Aggregate does not exist` | A Azure NetApp Files nem támogatja az új, LDAP-kompatibilis kötetek kiépítési folyamatát egy olyan pillanatképből, amely egy LDAP-letiltott kötethez tartozik. <br> Próbáljon meg új LDAP-letiltott kötetet létrehozni az adott pillanatképből. |

## <a name="next-steps"></a>Következő lépések  

* [Konfigurálja az LDAP HOZZÁADÁSát kiterjesztett csoportokkal az NFS mennyiségi hozzáféréshez](configure-ldap-extended-groups.md)
* [NFS-kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [Hozzon létre egy Dual-Protocol (NFSv3 és SMB) kötetet Azure NetApp Files](create-volumes-dual-protocol.md)