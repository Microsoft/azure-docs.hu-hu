---
title: A NFSv 4.1-es Kerberos-kötetével kapcsolatos hibák elhárítása Azure NetApp Files esetén | Microsoft Docs
description: Azokat a hibaüzeneteket és megoldásokat ismerteti, amelyek segíthetnek a NFSv 4.1-es Kerberos-kötetekkel kapcsolatos hibák elhárításában Azure NetApp Files.
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
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98134313"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>NFSv 4.1 – Kerberos mennyiségi problémák elhárítása 

Ez a cikk a NFSv 4.1-es Kerberos-kötetek létrehozásakor vagy kezelésekor esetlegesen fellépett hibákra vonatkozó megoldásokat ismerteti. 

## <a name="error-conditions-and-resolutions"></a>Hibákra vonatkozó feltételek és megoldások

|     Hiba feltételei    |     Megoldások    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | A Azure NetApp Files nem támogatja a Kerberost a NFSv3-kötetekhez. A Kerberos csak a NFSv 4.1 protokoll esetében támogatott.  |
|`This NetApp account has no configured Active Directory   connections`  |  Konfigurálja Active Directoryt a NetApp-fiókhoz a **KDC IP-címe** és az **ad-kiszolgáló neve** mezőkkel. Lásd: [a Azure Portal konfigurálása](configure-kerberos-encryption.md#configure-the-azure-portal) útmutatáshoz. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | A Azure NetApp Files nem támogatja az egyszerű NFSv 4.1-kötetek konvertálása Kerberos NFSv 4.1-es kötetre, és fordítva. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Például: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Győződjön meg arról, hogy az A/PTR rekordok megfelelően vannak beállítva, és léteznek a Active Directory a kiszolgálónévhez `smb-test-64d9.contoso.com` . <br> Ha az NFS-ügyfélen `nslookup` az `smb-test-64d9.contoso.com` IP-IP1 (azaz) feloldja az IP-címet `10.1.1.68` , akkor `nslookup` a IP1-nek csak egy rekordra kell feloldania (azaz `smb-test-64d9.contoso.com` ). `nslookup` a IP1 *nem lehet több* névvel feloldani. </li>  <li>A `NFS-<Smb NETBIOS NAME>-<few random characters>` PowerShell vagy a felhasználói felület használatával állítsa be az AES-256 típusú NFS-számítógépfiók típusát az ad-ban. <br> Példák parancsokra: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Győződjön meg arról, hogy az NFS-ügyfél, az AD és a Azure NetApp Files tárolási szoftver időpontja szinkronizálva van egymással, és egy öt perces ferde tartományon belül van. </li>  <li>A parancs használatával szerezze be a Kerberos-jegyet az NFS-ügyfélen `kinit <administrator>` .</li> <li>Csökkentse az NFS-ügyfél állomásnevét 15 karakternél rövidebbre, és végezze el újra a tartományhoz való csatlakozást. </li><li>Indítsa újra az NFS-ügyfelet és a `rpcgssd` szolgáltatást az alábbiak szerint. A parancs az operációs rendszertől függően eltérő lehet.<br> 7. RHEL: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu <br> (Indítsa újra a `rpc-gssd` szolgáltatást.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Előfordulhat, hogy a probléma az NFS-ügyfél problémájának kapcsolatára vonatkozik. Indítsa újra az NFS-ügyfelet.    |
|`Hostname lookup failed`   | Létre kell hoznia egy névkeresési zónát a DNS-kiszolgálón, majd hozzá kell adnia egy PTR-rekordot az adott névkeresési zónában. <br> Tegyük fel például, hogy az AD-gép IP-címe: `10.1.1.4` , az ad-gép állomásneve (az állomásnév parancs használatával megtalálva) `AD1` , a tartománynév pedig `contoso.com` . A névkeresési zónához hozzáadott PTR-rekordnak a következőnek kell lennie: `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Két lehetséges megoldás érhető el: <br> <ul><li> Ez a hiba azt jelzi, hogy a DNS nem érhető el. Ennek oka lehet helytelen DNS-cím vagy hálózati probléma. Ellenőrizze az AD-kapcsolatban megadott DNS-IP-címet, és győződjön meg arról, hogy az IP-cím helyes. </li> <li> Győződjön meg arról, hogy az AD és a kötet ugyanabban a régióban és ugyanabban a VNet található. Ha különböző virtuális hálózatok vannak, győződjön meg arról, hogy a két virtuális hálózatok között létrejött a VNet-társítás. </li></ul> |
|A NFSv 4.1-es Kerberos-kötet létrehozása a következő példához hasonló hibával meghiúsul: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |A KDC IP-címe nem megfelelő, és a Kerberos-kötet létrejött. Frissítse a KDC IP-címét egy helyes címmel. <br> A KDC IP-címének frissítése után a hiba nem fog elindulni. Újra létre kell hoznia a kötetet. |

## <a name="next-steps"></a>Következő lépések  

* [Az NFSv 4.1 Kerberos-titkosításának konfigurálása az Azure NetApp Fileshoz](configure-kerberos-encryption.md)
