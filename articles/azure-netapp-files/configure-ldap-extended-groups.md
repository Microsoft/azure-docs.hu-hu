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
ms.date: 04/09/2021
ms.author: b-juche
ms.openlocfilehash: 2546236399853f3ed6fad9e07e031edb568fbfe9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311532"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Konfigurálja az LDAP HOZZÁADÁSát kiterjesztett csoportokkal az NFS mennyiségi hozzáféréshez

Ha [létrehoz egy NFS-kötetet](azure-netapp-files-create-volumes.md), lehetősége van ENGEDÉLYEZNI az LDAP és a kiterjesztett csoportok funkció (az **LDAP** -beállítás) használatát a kötethez. Ez a szolgáltatás lehetővé teszi Active Directory LDAP-felhasználók és kiterjesztett csoportok (legfeljebb 1024 csoport) számára a kötet elérését. Az LDAP és a kiterjesztett csoportok funkció a NFSv 4.1-es és a NFSv3-kötetek esetében is használható. 

Ez a cikk ismerteti azokat a szempontokat és lépéseket, amelyek lehetővé teszik, hogy az LDAP-t kiterjesztett csoportokkal engedélyezze az NFS-kötetek létrehozásakor.  

## <a name="considerations"></a>Megfontolandó szempontok

* A kiterjesztett csoportokkal rendelkező LDAP csak Active Directory Domain Services (Hozzáadás) vagy Azure Active Directory tartományi szolgáltatásokkal (AADDS) támogatott. A OpenLDAP vagy más külső gyártótól származó LDAP-címtárszolgáltatások nem támogatottak. 

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

    > [!NOTE]
    > Győződjön meg arról, hogy konfigurálta a Active Directory-kapcsolatok beállításait. A rendszer létrehoz egy számítógépfiókot a Active Directoryi kapcsolatok beállításaiban megadott szervezeti egységben (OU). A beállításokat az LDAP-ügyfél használja a Active Directory való hitelesítéshez.

3. Győződjön meg arról, hogy a Active Directory LDAP-kiszolgáló működik és fut a Active Directory. 

4. Az LDAP NFS-felhasználóknak bizonyos POSIX-attribútumokkal kell rendelkezniük az LDAP-kiszolgálón. Az LDAP-felhasználók és az LDAP-csoportok attribútumai az alábbiak szerint állíthatók be: 

    * Az LDAP-felhasználók kötelező attribútumai:   
        `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: user`
    * Az LDAP-csoportok szükséges attribútumai:   
        `objectClass: group`, `gidNumber: 555`

    A POSIX-attribútumokat a Active Directory felhasználók és számítógépek MMC beépülő modullal kezelheti. A következő példa a Active Directory attribútum-szerkesztőt mutatja be:  

    ![Active Directory Attribute Editor](../media/azure-netapp-files/active-directory-attribute-editor.png) 

5. Ha LDAP-integrált NFSv 4.1 Linux-ügyfelet szeretne konfigurálni, tekintse meg [az NFS-ügyfél konfigurálása Azure NetApp Fileshoz](configure-nfs-clients.md)című témakört.

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
