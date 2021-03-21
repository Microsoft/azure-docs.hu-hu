---
title: fájl belefoglalása
description: fájl belefoglalása
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 61aa5ffcbab493109371067b1eb9d199a29cb852
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100551815"
---
**A konfigurációs és a feldolgozási kiszolgáló követelményei**


## <a name="hardware-requirements"></a>Hardverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3, beleértve az operációsrendszer-lemezt, a feldolgozási kiszolgáló gyorsítótárának lemezét és a feladat-visszavételi meghajtót 
Szabad lemezterület (folyamat kiszolgálójának gyorsítótára) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
 | 

## <a name="software-requirements"></a>Szoftverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-*)
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssor elérésének tiltása. <br> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br> – A fájlmellékletek megbízhatósági logikája. <br> – A parancsfájlok végrehajtásának bekapcsolása. <br> [További információ](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | – Nincs előre meglévő alapértelmezett webhely <br> – Nincs már meglévő webhely/alkalmazás a 443-es porton <br>– [Névtelen hitelesítés](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) engedélyezése <br> – [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -beállítás engedélyezése 
FIPS (Federal Information Processing Standards) | FIPS mód engedélyezése
|

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

**Összetevő** | **Követelmény** 
--- | --- 
IP-cím típusa | Statikus 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 (ha a konfigurációs kiszolgáló egy VMware virtuális gép)
 |
**Internet-hozzáférés**  (a kiszolgálónak közvetlenül vagy proxyn keresztül kell hozzáférnie a következő URL-címekhez):|
\*.backup.windowsazure.com | A replikált adatátvitelhez és a koordinációhoz használatos
\*.blob.core.windows.net | A replikált adattárolást tároló fiók elérésére szolgál. Megadhatja a gyorsítótárbeli Storage-fiók adott URL-címét.
\*.hypervrecoverymanager.windowsazure.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
https:\//login.microsoftonline.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos 
time.nist.gov | A rendszer és a globális idő közötti idő-szinkronizálás vizsgálatára szolgál.
time.windows.com | A rendszer és a globális idő közötti idő-szinkronizálás vizsgálatára szolgál.
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https: \/ /login.Live.com </li><li> https: \/ /Graph.Windows.net </li><li> https:\//login.windows.net </li><li> *. services.visualstudio.com (nem kötelező) </li><li> https: \/ /www.Live.com </li><li> https: \/ /www.microsoft.com </li></ul> | A OVF-telepítőnek hozzá kell férnie a további URL-címekhez. A hozzáférés-vezérléshez és az identitások kezeléséhez használják Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | A MySQL letöltésének befejezéséhez. </br> Néhány régióban a letöltés átirányítva lesz a CDN URL-címére. Szükség esetén győződjön meg arról, hogy a CDN URL-cím is jóvá van hagyva.
|

> [!NOTE]
> Ha Site Recovery-tárolóval rendelkezik [magánhálózati kapcsolattal](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) , nincs szükség további internet-hozzáférésre a konfigurációs kiszolgálóhoz. Kivételt jelent ez alól, miközben a CS-gépet a petesejtek sablonnal állítja be, a privát kapcsolatokhoz való hozzáféréshez, a-hoz és a-hoz tartozó következő URL-címekhez is hozzá kell férnie https://management.azure.com https://www.live.com https://www.microsoft.com . Ha nem szeretné engedélyezni a hozzáférést ezekhez az URL-címekhez, állítsa be a CS-t az egyesített telepítő használatával.

## <a name="required-software"></a>Szükséges szoftverek

**Összetevő** | **Követelmény** 
--- | ---
VMware vSphere PowerCLI | Nem szükséges a 9,14-es és újabb verziókhoz
MYSQL | Telepíteni kell a MySQL-t. Manuálisan is telepítheti, vagy Site Recovery telepítheti. (További információ a [beállítások konfigurálása](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) című témakörben találhatók)
|

## <a name="sizing-and-capacity-requirements"></a>Méretezési és kapacitási követelmények

A következő táblázat összefoglalja a konfigurációs kiszolgáló kapacitásának követelményeit. Több VMware virtuális gép replikálásakor tekintse át a [kapacitás megtervezésével kapcsolatos szempontokat](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) , és futtassa az [Azure site Recovery Deployment Planner eszközt](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPU** | **Memória** | **Lemez gyorsítótára** | **Adatváltozási arány** | **Replikált gépek**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 szoftvercsatorna * 4 mag \@ 2,5 GHz | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gép
12 vCPU<br/><br/> 2 SOCKS * 6 mag \@ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | 100 – 150 gép
16 vCPU<br/><br/> 2 zokni * 8 mag \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 gép
|