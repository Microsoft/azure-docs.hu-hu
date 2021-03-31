---
title: A felhőalapú Azure AD Connect Azure AD-beli szinkronizálásának előfeltételei
description: Ez a cikk a felhőalapú szinkronizáláshoz szükséges előfeltételeket és hardverkövetelményekat ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0277d4ce263610576178e3844a0665ab6506fbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579161"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>A Azure AD Connect Cloud Sync használatának előfeltételei
Ez a cikk útmutatást nyújt az Azure Active Directory (Azure AD) felhőalapú szinkronizálásának kiválasztásához és használatához a személyazonossági megoldásként.

## <a name="cloud-provisioning-agent-requirements"></a>A felhőalapú kiépítési ügynökre vonatkozó követelmények
Azure AD Connect Cloud Sync használatához a következőkre lesz szüksége:

- Tartományi rendszergazda vagy vállalati rendszergazdai hitelesítő adatok a Azure AD Connect Cloud Sync gMSA (csoportosan felügyelt szolgáltatásfiók) létrehozásához az ügynök szolgáltatás futtatásához. 
- Egy hibrid identitás-rendszergazdai fiók az Azure AD-bérlőhöz, amely nem vendég felhasználó.
- Helyszíni kiszolgáló a kiépítési ügynökhöz Windows 2016 vagy újabb rendszerrel.  A kiszolgálónak a [Active Directory felügyeleti rétegek modellje](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)alapján 0. rétegű kiszolgálónak kell lennie.
- Helyszíni tűzfal-konfigurációk.

## <a name="group-managed-service-accounts"></a>Csoportosan felügyelt szolgáltatásfiókok
A csoportosan felügyelt szolgáltatásfiók egy felügyelt tartományi fiók, amely automatikus jelszavas kezelést, egyszerűsített egyszerű szolgáltatásnév (SPN) felügyeletet, a felügyelet más rendszergazdák számára való delegálását, valamint a funkció több kiszolgálón való kibővítését is lehetővé teszi.  Azure AD Connect a Cloud Sync támogatja, és gMSA használ az ügynök futtatásához.  A fiók létrehozásához a rendszer kérni fogja a rendszergazdai hitelesítő adatok megadását a telepítés során.  A fiók a következőképpen fog megjelenni: (domain\provAgentgMSA $).  További információ a gMSA: [csoportosan felügyelt szolgáltatásfiókok](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>A gMSA előfeltételei:
1.  A gMSA tartomány erdő Active Directory sémáját frissíteni kell a Windows Server 2016-re.
2.  [POWERSHELL RSAT-modulok](/windows-server/remote/remote-server-administration-tools) egy tartományvezérlőn
3.  A tartomány legalább egy tartományvezérlőjén a Windows Server 2016 operációs rendszernek kell futnia.
4.  Egy tartományhoz csatlakoztatott kiszolgáló, amelyen az ügynök telepítve van, Windows Server 2016 vagy újabb rendszernek kell lennie.

### <a name="custom-gmsa-account"></a>Egyéni gMSA-fiók
Ha egyéni gMSA-fiókot hoz létre, gondoskodnia kell arról, hogy a fiók a következő engedélyekkel rendelkezik.

|Típus |Name |Access |Érvényesség| 
|-----|-----|-----|-----|
|Engedélyezés |gMSA-fiók |Az összes tulajdonság olvasása |Leszármazott eszköz objektumai| 
|Engedélyezés |gMSA-fiók|Az összes tulajdonság olvasása |Leszármazott InetOrgPerson objektumok| 
|Engedélyezés |gMSA-fiók |Az összes tulajdonság olvasása |Leszármazott számítógép-objektumok| 
|Engedélyezés |gMSA-fiók |Az összes tulajdonság olvasása |Leszármazott foreignSecurityPrincipal objektumok| 
|Engedélyezés |gMSA-fiók |Teljes hozzáférés |Leszármazott csoport objektumai| 
|Engedélyezés |gMSA-fiók |Az összes tulajdonság olvasása |Leszármazott felhasználói objektumok| 
|Engedélyezés |gMSA-fiók |Az összes tulajdonság olvasása |Leszármazott kapcsolattartási objektumok| 
|Engedélyezés |gMSA-fiók |Felhasználói objektumok létrehozása/törlése|Ez az objektum és az összes leszármazott objektum| 

A meglévő ügynökök gMSA-fiók használatára való frissítésének lépései a [csoportosan felügyelt szolgáltatásfiókok](how-to-install.md#group-managed-service-accounts)című részben olvashatók.

### <a name="in-the-azure-active-directory-admin-center"></a>A Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú hibrid identitás-rendszergazdai fiókot az Azure AD-bérlőn. Így kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások meghibásodnak vagy elérhetetlenné válnak. Ismerje meg, hogyan [adhat hozzá csak felhőalapú hibrid identitású rendszergazdai fiókot](../fundamentals/add-users-azure-active-directory.md). Ennek a lépésnek a befejezése kritikus fontosságú annak biztosítása érdekében, hogy ne legyen kizárva a bérlőből.
1. Adjon hozzá egy vagy több [Egyéni tartománynevet](../fundamentals/add-custom-domain.md) az Azure ad-bérlőhöz. A felhasználók a következő tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-directory-in-active-directory"></a>A címtárában Active Directory

A [IdFix eszköz](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) futtatásával készítse elő a címtár-attribútumok szinkronizálását.

### <a name="in-your-on-premises-environment"></a>Helyszíni környezetben

1. Azonosítson egy Windows Server 2016 vagy újabb rendszert futtató, tartományhoz csatlakoztatott gazdagépet legalább 4 GB RAM-mal és .NET 4.7.1 + futtatókörnyezettel.

2. A helyi kiszolgálón található PowerShell végrehajtási házirendet nem definiált vagy RemoteSigned értékre kell beállítani.

3. Ha tűzfal található a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
    - Győződjön meg arról, hogy az ügynökök az alábbi portokon keresztül tehetnek *kimenő* kéréseket az Azure ad-nek:

      | Portszám | Használat célja |
      | --- | --- |
      | **80** | Letölti a visszavont tanúsítványok listáját (CRL) a TLS/SSL-tanúsítvány ellenőrzése közben.  |
      | **443** | A szolgáltatással folytatott összes kimenő kommunikációt kezeli. |
      | **8080** (nem kötelező) | Az ügynökök 10 percenként jelentik az állapotukat az 8080-as porton keresztül, ha a 443-es port nem érhető el. Ez az állapot az Azure AD-portálon jelenik meg. |

    - Ha a tűzfal a felhasználók helye szerint érvényesíti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatások adatforgalma számára.
    - Ha a tűzfal vagy proxy lehetővé teszi a biztonságos utótagok megadását, vegyen fel kapcsolatokat a \* . msappproxy.net és a \* . servicebus.Windows.net. Ha nem, engedélyezze a hozzáférést az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek hetente frissülnek.
    - Az ügynököknek hozzá kell férniük a login.windows.net és a login.microsoftonline.com címhez a kezdeti regisztrációhoz. Nyissa meg a tűzfalat az URL-címekhez is.
    - A tanúsítvány érvényesítéséhez oldja fel a következő URL-címeket: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 és www \. Microsoft.com:80. Ezek az URL-ek tanúsítvány-ellenőrzésre szolgálnak más Microsoft-termékek esetén, így előfordulhat, hogy az URL-ek tiltásának feloldása már megtörtént.

    >[!NOTE]
    > A felhőalapú kiépítési ügynök telepítése a Windows Server Core rendszerre nem támogatott.

### <a name="additional-requirements"></a>További követelmények

- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-követelmények

> [!NOTE]
> A Transport Layer Security (TLS) egy olyan protokoll, amely biztonságos kommunikációt tesz lehetővé. A TLS-beállítások módosítása a teljes erdőre hatással van. További információ: [frissítés a tls 1,1 és a tls 1,2 alapértelmezett biztonságos protokollokként a Windowsban](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

A Azure AD Connect felhőalapú kiépítési ügynököt futtató Windows Servernek a telepítése előtt rendelkeznie kell a TLS 1,2-es beállítással.

A TLS 1,2 engedélyezéséhez kövesse az alábbi lépéseket.

1. Állítsa be a következő beállításkulcsokat:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Indítsa újra a kiszolgálót.

## <a name="known-limitations"></a>Ismert korlátozások

A következő ismert korlátozások érvényesek:

### <a name="delta-synchronization"></a>Különbözeti szinkronizálás

- A csoport hatókör-szűrése a különbözeti szinkronizáláshoz nem támogatja a 1500-nál több tagot.
- Ha egy csoport-hatóköri szűrő részeként használt csoportot töröl, a csoportba tartozó felhasználók nem lesznek törölve. 
- Ha átnevezi a hatókörben lévő szervezeti egységet vagy csoportot, a különbözeti szinkronizálás nem fogja eltávolítani a felhasználókat.

### <a name="provisioning-logs"></a>Üzembehelyezési naplók
- A kiépítési naplók nem különböztetik meg egyértelműen a létrehozási és frissítési műveletek közötti különbséget.  A létrehozáshoz egy frissítéshez és egy frissítési művelethez is megjelenhet egy létrehozási művelet.

### <a name="group-re-naming-or-ou-re-naming"></a>Csoportos Átnevezés vagy szervezeti egység átnevezése
- Ha olyan csoportot vagy szervezeti egységet nevez át az AD-ben, amely egy adott konfiguráció hatókörében van, akkor a felhőalapú szinkronizálási feladatok nem tudják felismerni a nevet az AD-ben. A feladatokra nem kerül a karanténba helyezés, és kifogástalan marad.


## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)