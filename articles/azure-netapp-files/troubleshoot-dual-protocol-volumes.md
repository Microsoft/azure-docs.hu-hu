---
title: SMB-vagy kétprotokollos kötetek hibáinak megoldása a Azure NetApp Fileshoz | Microsoft Docs
description: Azokat a hibaüzeneteket és megoldásokat ismerteti, amelyek segíthetnek az SMB vagy a kettős protokollal kapcsolatos problémák megoldásában Azure NetApp Files.
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
ms.date: 02/02/2021
ms.author: b-juche
ms.openlocfilehash: 237fb514229f370fcba79133232e80a6a655048f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104416"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>SMB-vagy kettős protokollú kötetek hibáinak megoldása

Ez a cikk a kettős protokollú kötetek létrehozásakor vagy felügyeletekor fellépett hibákra vonatkozó megoldásokat ismerteti.

## <a name="errors-for-dual-protocol-volumes"></a>Hibák a kettős protokollú köteteken

|     Hiba feltételei    |     Megoldások    |
|-|-|
| A TLS protokollon keresztüli LDAP engedélyezve van, és a kétprotokollos kötet létrehozása meghiúsul a hibával `This Active Directory has no Server root CA Certificate` .    |     Ha ez a hiba kettős protokollú kötet létrehozásakor fordul elő, győződjön meg arról, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány fel van töltve a NetApp-fiókba.    |
| A kétprotokollos kötet létrehozása a következő hibával meghiúsul `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Lehet, hogy a DNS-kiszolgálón hiányzik az AD Host Machine mutató (PTR) rekordja. Létre kell hoznia egy névkeresési zónát a DNS-kiszolgálón, majd hozzá kell adnia egy PTR-rekordot az adott névkeresési zónában. <br> Tegyük fel például, hogy az AD-gép IP-címe `10.X.X.X` , az ad-gép állomásneve (a parancs használatával megtalált `hostname` ) `AD1` , és a tartománynév `contoso.com` .  A névkeresési zónához hozzáadott PTR-rekordnak a következőnek kell lennie: `10.X.X.X`  ->  `contoso.com` .   |
| A kétprotokollos kötet létrehozása a következő hibával meghiúsul `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.X.X.X, port 88 using TCP\\n**[ 950] FAILURE` . |     Ez a hiba azt jelzi, hogy az AD-jelszó helytelen, ha Active Directory csatlakozik a NetApp-fiókhoz. Frissítse az AD-kapcsolatokat a megfelelő jelszóval, és próbálkozzon újra. |
| A kétprotokollos kötet létrehozása a következő hibával meghiúsul `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Ez a hiba azt jelzi, hogy a DNS nem érhető el. Ennek oka az lehet, hogy a DNS IP-címe helytelen, vagy hálózati probléma van. Ellenőrizze az AD-kapcsolatban megadott DNS-IP-címet, és győződjön meg arról, hogy az IP-cím helyes. <br> Győződjön meg arról is, hogy az AD és a kötet ugyanabban a régióban és ugyanabban a VNet található. Ha különböző virtuális hálózatok vannak, győződjön meg arról, hogy a két virtuális hálózatok között létrejött a VNet-társítás.|
| A kettős protokollú kötetek csatlakoztatásakor a rendszer megtagadta az engedélyt. | A kettős protokollú kötetek az NFS-és az SMB-protokollokat is támogatják.  Amikor megpróbál hozzáférni a csatlakoztatott kötethez a UNIX rendszerű rendszeren, a rendszer megkísérli leképezni a Windows-felhasználóhoz használt UNIX-felhasználót. Ha nem található leképezés, az "engedély megtagadva" hibaüzenet jelenik meg. <br> Ez a helyzet akkor is érvényes, ha a "root" felhasználót használja a hozzáféréshez. <br> Az "engedély megtagadva" probléma elkerüléséhez győződjön meg arról, hogy a Windows Active Directory tartalmazza `pcuser` a csatlakoztatási pont elérését. Ha az `pcuser` "engedély megtagadva" problémával találkozik, várjon 24 órát a gyorsítótár-bejegyzés törlésére a hozzáférés ismételt megkísérlése előtt. |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>Gyakori hibák az SMB és a kettős protokollú kötetek esetében

|     Hiba feltételei    |     Megoldások    |
|-|-|
| Az SMB vagy a kettős protokoll kötetének létrehozása a következő hiba miatt meghiúsul: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | Ez a hiba azt jelzi, hogy a DNS nem érhető el. <br> Vegye figyelembe a következő megoldásokat: <ul><li>Ellenőrizze, hogy a Hozzáadás és a kötet üzembe helyezése azonos régióban történik-e.</li> <li>Ellenőrizze, hogy a Hozzáadás és a kötet ugyanazt a VNet használja-e. Ha különböző virtuális hálózatok használnak, győződjön meg arról, hogy a virtuális hálózatok egymással vannak társítva. Lásd: [Azure NetApp Files hálózati tervezéssel kapcsolatos irányelvek](azure-netapp-files-network-topologies.md). </li> <li>Lehetséges, hogy a DNS-kiszolgáló hálózati biztonsági csoportokat (NSG) alkalmaz.  Ezért nem teszi lehetővé a forgalom áramlását. Ebben az esetben nyissa meg a NSG a DNS vagy az AD használatával a különböző portokhoz való csatlakozáshoz. A portokra vonatkozó követelményekért lásd: [Active Directory kapcsolatokra vonatkozó követelmények](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). </li></ul> <br>Ugyanezek a megoldások is érvényesek az Azure-hoz. Az Azure-BŐVÍTMÉNYeket ugyanabban a régióban kell üzembe helyezni. A VNet ugyanabban a régióban kell lennie, vagy a kötet által használt VNet kell összeállítani. | 
| Az SMB vagy a kettős protokoll kötetének létrehozása a következő hiba miatt meghiúsul: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>Győződjön meg arról, hogy a megadott Felhasználónév helyes. </li> <li>Győződjön meg arról, hogy a felhasználó a számítógép-fiókok létrehozásához szükséges jogosultsággal rendelkező rendszergazda csoport tagja. </li> <li> Ha az Azure AD-t használja, ellenőrizze, hogy a felhasználó tagja-e az Azure AD-csoportnak `Azure AD DC Administrators` . </li></ul> | 
| Az SMB vagy a kettős protokoll kötetének létrehozása a következő hiba miatt meghiúsul: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.X.X.X, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | Győződjön meg arról, hogy az Active Directory-csatlakozáshoz megadott jelszó helyes. |
| Az SMB vagy a kettős protokoll kötetének létrehozása a következő hiba miatt meghiúsul: `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.X.X.X, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | Győződjön meg arról, hogy az AD-kapcsolódáshoz való csatlakozáshoz megadott szervezeti egység elérési útja helyes. Ha Azure-HOZZÁADÁSt használ, győződjön meg arról, hogy a szervezeti egység elérési útja a ( `OU=AADDC Computers` ). |

## <a name="next-steps"></a>Következő lépések  

* [SMB-kötet létrehozása](azure-netapp-files-create-volumes-smb.md)
* [Kettős protokollú kötet létrehozása](create-volumes-dual-protocol.md)
* [NFS-ügyfél konfigurálása az Azure NetApp Fileshoz](configure-nfs-clients.md)
