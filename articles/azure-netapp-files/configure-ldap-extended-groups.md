---
title: Konfigurálja az LDAP HOZZÁADÁSát kiterjesztett csoportokkal Azure NetApp Files NFS-kötetek eléréséhez | Microsoft Docs
description: Ismerteti azokat a szempontokat és lépéseket, amelyek lehetővé teszik az LDAP kibővített csoportokkal való engedélyezését, ha Azure NetApp Files használatával hoz létre NFS-kötetet.
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
ms.topic: how-to
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 2031cbf07d700307ae1e11c516f9fc736bce5080
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498980"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Konfigurálja az LDAP HOZZÁADÁSát kiterjesztett csoportokkal az NFS mennyiségi hozzáféréshez

Ha [létrehoz egy NFS-kötetet](azure-netapp-files-create-volumes.md), lehetősége van ENGEDÉLYEZNI az LDAP és a kiterjesztett csoportok funkció (az **LDAP** -beállítás) használatát a kötethez. Ez a szolgáltatás lehetővé teszi Active Directory LDAP-felhasználók és kiterjesztett csoportok (legfeljebb 1024 csoport) számára a kötet elérését.  

Ez a cikk ismerteti azokat a szempontokat és lépéseket, amelyek lehetővé teszik, hogy az LDAP-t kiterjesztett csoportokkal engedélyezze az NFS-kötetek létrehozásakor.  

## <a name="considerations"></a>Megfontolandó szempontok

* Azure Active Directory Domain Services (AADDS) használata esetén a TLS protokollon keresztüli LDAP-t *nem* szabad engedélyezni.  

* Ha engedélyezi az LDAP-t a kiterjesztett csoportok funkcióval, az LDAP-kompatibilis [Kerberos-kötetek](configure-kerberos-encryption.md) nem fogják megfelelően megjeleníteni a fájl tulajdonjogát az LDAP-felhasználók számára. Az LDAP-felhasználó által létrehozott fájl vagy könyvtár a `root` tényleges LDAP-felhasználó helyett a tulajdonos lesz. A fiók azonban a `root` paranccsal manuálisan is módosíthatja a fájl tulajdonjogát `chown <username> <filename>` . 

* A kötet létrehozása után nem módosítható az LDAP-beállítás (engedélyezve vagy letiltva).  

* Az alábbi táblázat az LDAP-gyorsítótár élettartam (TTL) beállításait ismerteti. Meg kell várnia a gyorsítótár frissítését, mielőtt egy ügyfélen keresztül próbál hozzáférni egy fájlhoz vagy könyvtárhoz. Ellenkező esetben egy hozzáférés-megtagadási üzenet jelenik meg az ügyfélen. 

    |     Hibafeltétel    |     Feloldás    |
    |-|-|
    | Gyorsítótár |  Alapértelmezett időtúllépés |
    | Csoporttagság listája  | 24 órás élettartam  |
    | UNIX-csoportok  | 24 órás TTL, 1 perces negatív TTL  |
    | UNIX-felhasználók  | 24 órás TTL, 1 perces negatív TTL  |

    A gyorsítótárak egy adott időtúllépési időszakot kapnak *, amely az idő alatt él*. Az időtúllépési időszakot követően a bejegyzések élettartama lejárt, így az elavult bejegyzések nem maradnak meg. A *negatív TTL* -érték a nem létező objektumok LDAP-lekérdezései miatti hibák elkerülése érdekében a sikertelenül megadott keresés.        

## <a name="steps"></a>Lépések

1. Az LDAP with Extended groups szolgáltatás jelenleg előzetes verzióban érhető el. A szolgáltatás első használata előtt regisztrálnia kell a szolgáltatást:  

    1. Regisztrálja a szolgáltatást:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. A szolgáltatás regisztrálási állapotának ellenõrzése: 

        > [!NOTE]
        > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . Várjon, amíg az állapot megmarad a `Registered` Folytatás előtt.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    Használhatja az [Azure CLI-parancsokat](/cli/azure/feature) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

2. Az LDAP-kötetek Active Directory konfigurációt igényelnek az LDAP-kiszolgáló beállításaihoz. Kövesse az [Active Directory kapcsolatokra vonatkozó követelmények](create-active-directory-connections.md#requirements-for-active-directory-connections) című témakör utasításait, és [hozzon létre egy Active Directory kapcsolatot](create-active-directory-connections.md#create-an-active-directory-connection) a Azure Portal Active Directory kapcsolatainak konfigurálásához.  

3. Győződjön meg arról, hogy a Active Directory LDAP-kiszolgáló működik és fut a Active Directory. Ezt úgy teheti meg, ha telepíti és konfigurálja a [Active Directory Lightweight Directory-szolgáltatások (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) szerepkört az ad-gépen.

4. Az LDAP NFS-felhasználóknak bizonyos POSIX-attribútumokkal kell rendelkezniük az LDAP-kiszolgálón. A szükséges attribútumok beállításához kövesse az [LDAP POSIX-attribútumok kezelése](create-volumes-dual-protocol.md#manage-ldap-posix-attributes) című témakört.  

5. Ha LDAP-hez integrált Linux-ügyfelet szeretne konfigurálni, tekintse meg [az NFS-ügyfél konfigurálása Azure NetApp Fileshoz](configure-nfs-clients.md)című témakört.

6.  Az NFS-kötet létrehozásához kövesse az [NFS-kötet létrehozása a Azure NetApp Files](azure-netapp-files-create-volumes.md) számára című témakör lépéseit. A kötet létrehozása folyamat alatt, a **protokoll** lapon engedélyezze az **LDAP** -beállítást.   

    ![Képernyőfelvétel: kötet létrehozása lap LDAP-beállítással.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Nem kötelező – engedélyezheti a Windows LDAP-kiszolgálón nem szereplő helyi NFS-ügyfelek számára az olyan NFS-kötetek elérését, amelyeken engedélyezve van az LDAP és a kiterjesztett csoportok használata. Ehhez engedélyezze a **helyi NFS-felhasználók engedélyezése LDAP** -sel beállítást a következő módon:
    1. Kattintson **Active Directory kapcsolatok** elemre.  Egy meglévő Active Directory-kapcsolatban kattintson a helyi menüre (a három pontra `…` ), majd válassza a **Szerkesztés** lehetőséget.  
    2. A megjelenő **Active Directory beállítások szerkesztése** ablakban jelölje be a **helyi NFS-felhasználók engedélyezése LDAP** -beállítással beállítást.  

    ![Képernyőfelvétel a helyi NFS-felhasználók engedélyezése LDAP-beállítással](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Következő lépések  

* [NFS-kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [Active Directory kapcsolatok létrehozása és kezelése](create-active-directory-connections.md)
* [Az LDAP-kötetek problémáinak elhárítása](troubleshoot-ldap-volumes.md)
