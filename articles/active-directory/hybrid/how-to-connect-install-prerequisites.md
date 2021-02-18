---
title: 'Azure AD Connect: előfeltételek és hardver | Microsoft Docs'
description: Ez a cikk ismerteti az előfeltételeket és a Azure AD Connect hardverre vonatkozó követelményeit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15e5aba2bad4cd7ae63ceb9c9f67f7e653a82a91
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650150"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Az Azure AD Connect előfeltételei
Ez a cikk a Azure Active Directory (Azure AD) csatlakozási előfeltételeit és hardverkövetelmények leírását ismerteti.

## <a name="before-you-install-azure-ad-connect"></a>A Azure AD Connect telepítése előtt
A Azure AD Connect telepítése előtt néhány dolog szükséges.

### <a name="azure-ad"></a>Azure AD
* Szüksége lesz egy Azure AD-bérlőre. Egy [ingyenes Azure-próbaverziót](https://azure.microsoft.com/pricing/free-trial/)kap. Azure AD Connect kezeléséhez a következő portálok egyikét használhatja:
  * A [Azure Portal](https://portal.azure.com).
  * Az [Office-portálon](https://portal.office.com).
* [Adja hozzá és ellenőrizze az](../fundamentals/add-custom-domain.md) Azure ad-ben használni kívánt tartományt. Ha például contoso.com kíván használni a felhasználók számára, győződjön meg róla, hogy a tartomány ellenőrzése megtörtént, és nem csak a contoso.onmicrosoft.com alapértelmezett tartományát használja.
* Az Azure AD-bérlők alapértelmezés szerint 50 000 objektumot tesznek lehetővé. A tartomány ellenőrzésekor a korlát 300 000 objektumra nő. Ha még több objektumra van szüksége az Azure AD-ben, nyisson meg egy támogatási esetet, hogy a korlát továbbra is nagyobb legyen. Ha több mint 500 000 objektumra van szüksége, licencre van szüksége, például Microsoft 365, prémium szintű Azure AD vagy Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>A helyszíni adatfeldolgozás előkészítése
* A [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) használatával azonosíthatja a címtárban előforduló hibákat, például az ismétlődéseket és a formázási problémákat, mielőtt szinkronizálni kíván az Azure ad-val és a Microsoft 365ával.
* Tekintse át [Az Azure ad-ben engedélyezhető választható szinkronizálási funkciókat](how-to-connect-syncservice-features.md), és értékelje ki, hogy mely szolgáltatásokat kell engedélyezni.

### <a name="on-premises-active-directory"></a>Helyszíni Active Directory
* A Active Directory séma verziószámának és az erdő működési szintjének Windows Server 2003 vagy újabb verziójúnak kell lennie. A tartományvezérlők bármilyen verziót futtathatnak, amíg a séma verziója és az erdő szintű követelmények teljesülnek.
* Ha azt tervezi, hogy a szolgáltatás *jelszava visszaírási* használja, a tartományvezérlőknek Windows Server 2012 vagy újabb rendszerre kell esniük.
* Az Azure AD által használt tartományvezérlőnek írhatónak kell lennie. Írásvédett tartományvezérlő használata *nem támogatott*, és a Azure ad Connect nem hajtja végre az írási átirányítást.
* A helyszíni erdők vagy tartományok használata "pontozott" (a név tartalmazza a "." pontot) A NetBIOS-nevek *nem támogatottak*.
* Javasoljuk, hogy [engedélyezze a Active Directory Lomtárát](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>PowerShell végrehajtási házirend
A Azure Active Directory Connect aláírt PowerShell-parancsfájlokat futtat a telepítés részeként. Győződjön meg arról, hogy a PowerShell-végrehajtási házirend engedélyezi a parancsfájlok futtatását.

A telepítés során a javasolt végrehajtási szabályzat a "RemoteSigned".

A PowerShell végrehajtási házirendjének beállításával kapcsolatos további információkért lásd: [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).


### <a name="azure-ad-connect-server"></a>Azure AD Connect kiszolgáló
A Azure AD Connect kiszolgáló kritikus identitási adatait tartalmaz. Fontos, hogy a kiszolgálóhoz való rendszergazdai hozzáférés megfelelő védelemmel legyen ellátott. Kövesse a [privilegizált hozzáférés biztonságossá](/windows-server/identity/securing-privileged-access/securing-privileged-access)tételével kapcsolatos útmutatást. 

A Azure AD Connect-kiszolgálót 0. rétegű összetevőként kell kezelni, ahogy azt a [Active Directory felügyeleti modellben](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) dokumentálták 

További információ a Active Directory környezet biztonságossá tételéről: [ajánlott eljárások a Active Directory biztonságossá tételéhez](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Telepítési előfeltételek

- A Azure AD Connectt tartományhoz csatlakoztatott Windows Server 2012-es vagy újabb verzióra kell telepíteni. 
- Azure AD Connect nem telepíthető a Small Business Server vagy a Windows Server Essentials rendszerre, mielőtt 2019 (a Windows Server Essentials 2019 támogatott). A kiszolgálónak a Windows Server standard vagy magasabb szintűnek kell lennie. 
- A Azure AD Connect-kiszolgálónak teljes grafikus felhasználói felülettel kell rendelkeznie. A Azure AD Connect telepítése a Windows Server Core rendszeren nem támogatott. 
- A Azure AD Connect-kiszolgáló nem rendelkezhet a PowerShell-átírással Csoportházirend, ha a Azure AD Connect varázslóval felügyeli a Active Directory összevonási szolgáltatások (AD FS) (AD FS) konfigurációját. Ha a szinkronizálási konfiguráció kezeléséhez a Azure AD Connect varázslót használja, akkor engedélyezheti a PowerShell átírását. 
- Ha AD FS üzembe helyezése folyamatban van: 
    - Azok a kiszolgálók, amelyeken telepítve van a AD FS vagy a webalkalmazás-proxy, Windows Server 2012 R2 vagy újabb rendszernek kell lennie. A Távoli telepítéshez engedélyezni kell a Rendszerfelügyeleti webszolgáltatásokat ezeken a kiszolgálókon. 
    - Konfigurálnia kell a TLS/SSL-tanúsítványokat. További információ: [SSL/TLS protokollok és titkosító csomagok kezelése a AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) és [az SSL-tanúsítványok kezelése a ad FSban](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Konfigurálnia kell a névfeloldást. 
- Ha a globális rendszergazdák rendelkeznek MFA-támogatással, az URL-címnek szerepelnie kell https://secure.aadcdn.microsoftonline-p.com  a megbízható helyek listájában. A rendszer arra kéri, hogy adja hozzá ezt a helyet a megbízható helyek listájához, amikor a rendszer egy MFA-kérdés megadását kéri, és korábban még nem adta hozzá. Az Internet Explorer használatával adhatja hozzá a megbízható helyekhez.
- Ha Azure AD Connect Health szinkronizálást szeretne használni, győződjön meg arról, hogy a Azure AD Connect Health előfeltételei is teljesülnek. További információ: [Azure ad Connect Health ügynök telepítése](how-to-connect-health-agent-install.md).

#### <a name="harden-your-azure-ad-connect-server"></a>Azure AD Connect-kiszolgáló megerősítése 
Javasoljuk, hogy a biztonsági támadási felületet az IT-környezet ezen kritikus összetevője számára történő csökkentése érdekében a Azure AD Connect-kiszolgáló megerősítse. A javaslatok követése segít csökkenteni a szervezete biztonsági kockázatait.

- A Azure AD Connect a tartományvezérlővel és más 0. szintű erőforrásokkal azonos módon kezelhető. További információ: [Active Directory felügyeleti rétegek modellje](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- A Azure AD Connect-kiszolgálóhoz való rendszergazdai hozzáférés korlátozása csak tartományi rendszergazdák vagy más szigorúan ellenőrzött biztonsági csoportok számára.
- Hozzon létre egy [dedikált fiókot az összes, emelt szintű hozzáféréssel rendelkező személy számára](/windows-server/identity/securing-privileged-access/securing-privileged-access). A rendszergazdáknak nem szabad megkeresni a webet, ellenőrizniük az e-mailjeit, és a magas jogosultsági szintű fiókokkal a napi hatékonyságnövelő feladatokat kell megadniuk.
- Kövesse az emelt [szintű hozzáférés biztonságossá tétele](/windows-server/identity/securing-privileged-access/securing-privileged-access)című témakör útmutatását. 
- Az NTLM-hitelesítés használatának megtagadása a AADConnect-kiszolgálóval. A következőképpen teheti meg ezt: [az NTLM korlátozása a AADConnect-kiszolgálón és az](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) [NTLM korlátozása egy tartományon](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Győződjön meg arról, hogy minden gépnek egyedi helyi rendszergazdai jelszava van. További információ: [helyi rendszergazda jelszavas megoldás (kör)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) az egyes munkaállomásokon egyedi véletlenszerű jelszavakat konfigurálhat, és a kiszolgálókat ACL-védelemmel ellátott Active Directory tárolhatja. Csak a jogosult jogosult felhasználók olvashatják el vagy kérhetik a helyi rendszergazdai fiók jelszavának visszaállítását. A munkaállomásokon és kiszolgálókon a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=46899)kérheti le a kört. További útmutatást a környezet és az emelt szintű hozzáférési munkaállomások (mancsok) üzemeltetéséhez az [üzemeltetési szabványokban találhat a tiszta forrás elve alapján](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Dedikált emelt [szintű hozzáférésű munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) implementálása minden munkatárs számára, a szervezet információs rendszereihez emelt szintű hozzáféréssel. 
- A Active Directory-környezet támadási felületének csökkentése érdekében kövesse ezeket a [további irányelveket](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) .


### <a name="sql-server-used-by-azure-ad-connect"></a>Az Azure AD Connect által használt SQL-kiszolgáló
* Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Alapértelmezés szerint a SQL Server 2012 Express LocalDB (SQL Server Express) egy egyszerűsített verziója van telepítve. A SQL Server Express 10 GB-os mérethatárt tartalmaz, amely lehetővé teszi körülbelül 100 000 objektum kezelését. Ha nagyobb mennyiségű címtár-objektumot kell kezelnie, mutasson a telepítővarázsló SQL Server másik telepítésére. A SQL Server telepítésének típusa hatással lehet [Azure ad Connect teljesítményére](./plan-connect-performance-factors.md#sql-database-factors).
* Ha a SQL Server eltérő telepítését használja, a következő követelmények érvényesek:
  * A Azure AD Connect a SQL Server összes verzióját támogatja a 2012-es verzióról (a legújabb szervizcsomaggal) a SQL Server 2019. A Azure SQL Database adatbázisként *nem támogatott* .
  * Kis-és nagybetűket nem megkülönböztető SQL-rendezést kell használnia. Ezeket a rendezéseket a \_ nevükben CI_ azonosítjuk. A CS_ által azonosított kis-és nagybetűket megkülönböztető rendezés használata \_ *nem támogatott*.
  * SQL-példányon csak egy szinkronizálási motor tartozhat. Az SQL-példányok *nem támogatottak* a FIM/a következő szinkronizálással, az rsync vagy a Azure ad-Szinkronizálóval.

### <a name="accounts"></a>Fiókok
* A szolgáltatással integrálni kívánt Azure AD-bérlőhöz Azure AD globális rendszergazdai fiókkal kell rendelkeznie. Ennek a fióknak *iskolai vagy szervezeti fióknak* kell lennie, és nem lehet *Microsoft-fiók*.
* Ha az [expressz beállításokat](reference-connect-accounts-permissions.md#express-settings-installation) használja, vagy a verzióról a frissítésre van szüksége, vállalati rendszergazdai fiókkal kell rendelkeznie a helyszíni Active Directoryhoz.
* Ha az egyéni beállítások telepítési útvonalát használja, több lehetőség is rendelkezésre áll. További információ: [egyéni telepítési beállítások](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Kapcsolatok
* A Azure AD Connect-kiszolgálónak az intraneten és az interneten egyaránt DNS-feloldásra van szüksége. A DNS-kiszolgálónak képesnek kell lennie a nevek feloldására a helyszíni Active Directory és az Azure AD-végpontokon.
* Ha tűzfallal rendelkezik az intraneten, és meg kell nyitnia a portokat a Azure AD Connect-kiszolgálók és a tartományvezérlők között, további információért lásd: [Azure ad Connect portok](reference-connect-ports.md) .
* Ha a proxy vagy a tűzfal korlátozza, hogy mely URL-címek érhetők el, meg kell nyitni az [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) által dokumentált URL-címeket. Tekintse [meg a tűzfal vagy a proxykiszolgáló Azure Portal URL-safelist](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud)is.
  * Ha a Microsoft Cloud-ot Németországban vagy a Microsoft Azure Government felhőben használja, Azure AD Connect tekintse meg az URL-címek [szinkronizálási szolgáltatás példányainak szempontjait](reference-connect-instances.md) ismertető témakört.
* A Azure AD Connect (1.1.614.0 és újabb verzió) alapértelmezés szerint a TLS 1,2-et használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. Ha a TLS 1,2 nem érhető el az alapul szolgáló operációs rendszeren, Azure AD Connect fokozatosan visszakerül a régebbi protokollokra (TLS 1,1 és TLS 1,0).
* A 1.1.614.0 verzió előtt a Azure AD Connect alapértelmezés szerint a TLS 1,0-et használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. A TLS 1,2-re való váltáshoz kövesse a [tls 1,2 engedélyezése a Azure ad Connect számára](#enable-tls-12-for-azure-ad-connect)című témakör lépéseit.
* Ha kimenő proxyt használ az internethez való csatlakozáshoz, akkor a **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** fájl következő beállítását hozzá kell adni a telepítővarázsló számára, és Azure ad Connect a szinkronizálást, hogy csatlakozni tudjon az internethez és az Azure ad-hoz. Ezt a szöveget a fájl alján kell megadni. Ebben a kódban a *&lt; PROXYADDRESS &gt;* a tényleges proxy IP-címét vagy állomásnevét jelöli.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Ha a proxykiszolgáló hitelesítést igényel, a [szolgáltatási fióknak](reference-connect-accounts-permissions.md#adsync-service-account) a tartományban kell lennie. [Egyéni szolgáltatásfiók](how-to-connect-install-custom.md#install-required-components)megadásához használja a testreszabott beállítások telepítési útvonalát. A machine.config módosítására is szükség van. Ha ez a változás machine.config, a telepítővarázsló és a Szinkronizáló motor válaszol a proxykiszolgáló hitelesítési kéréseire. A telepítővarázsló összes lapján, a configure ( **Konfigurálás** ) lapon kívül a rendszer a bejelentkezett felhasználó hitelesítő adatait használja. A telepítővarázsló végén a **configure (Konfigurálás** ) lapon a környezet a létrehozott [szolgáltatásfiók](reference-connect-accounts-permissions.md#adsync-service-account) felé vált. A machine.config szakasznak így kell kinéznie:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Ha a proxy konfigurációját egy meglévő telepítő hajtja végre, akkor a **Microsoft Azure ad szinkronizálási szolgáltatást** egyszer kell újraindítani ahhoz, hogy a Azure ad Connect beolvassa a proxy konfigurációját, és frissítse a viselkedést. 
* Ha Azure AD Connect webes kérést küld az Azure AD-nak a címtár-szinkronizálás részeként, az Azure AD akár 5 percet is igénybe vehet. Gyakori, hogy a proxykiszolgálók a kapcsolat tétlen időtúllépési konfigurációját. Győződjön meg arról, hogy a konfiguráció legalább 6 percre van beállítva.

További információ: MSDN az [alapértelmezett proxy elemről](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
További információt a kapcsolattal kapcsolatos problémákkal kapcsolatban a [kapcsolódási problémák elhárítása](tshoot-connect-connectivity.md)című témakörben talál.

### <a name="other"></a>Egyéb
Nem kötelező: tesztelő felhasználói fiók használata a szinkronizálás ellenőrzéséhez.

## <a name="component-prerequisites"></a>Összetevők előfeltételei
### <a name="powershell-and-net-framework"></a>PowerShell és .NET-keretrendszer
A Azure AD Connect a Microsoft PowerShelltől és a .NET-keretrendszer 4.5.1-től függ. Erre a verzióra vagy egy újabb, a kiszolgálóra telepített verzióra van szükség. A Windows Server-verziótól függően végezze el a következő műveleteket:

* Windows Server 2012 R2
  * A Microsoft PowerShell alapértelmezés szerint telepítve van. Semmit nem kell tenni.
  * A .NET-keretrendszer 4.5.1-es és újabb kiadásai a Windows Updateon keresztül érhetők el. Győződjön meg arról, hogy telepítette a Windows Server legújabb frissítéseit a Vezérlőpulton.
* Windows Server 2012
  * A Microsoft PowerShell legújabb verziója a Windows Management Framework 4,0-es verziójában érhető el, amely a [Microsoft letöltőközpontból](https://www.microsoft.com/downloads)érhető el.
  * A .NET-keretrendszer 4.5.1-es és újabb kiadásai a [Microsoft letöltőközpontból](https://www.microsoft.com/downloads)érhetők el.


### <a name="enable-tls-12-for-azure-ad-connect"></a>A TLS 1,2 engedélyezése Azure AD Connect
A 1.1.614.0 verzió előtt a Azure AD Connect alapértelmezés szerint TLS 1,0-et használ a Sync Engine-kiszolgáló és az Azure AD közötti kommunikáció titkosításához. A .NET-alkalmazások a kiszolgálón alapértelmezés szerint a TLS 1,2 használatára is konfigurálhatók. További információ a TLS 1,2-ről: [Microsoft Security advisor 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Győződjön meg arról, hogy az operációs rendszeréhez telepítve van a .NET 4.5.1-es gyorsjavítás. További információ: [Microsoft biztonsági tanácsadó 2960358](/security-updates/SecurityAdvisories/2015/2960358). Lehet, hogy ez a gyorsjavítás vagy egy későbbi kiadás már telepítve van a kiszolgálón.

1. Az összes operációs rendszer esetében állítsa be ezt a beállításkulcsot, és indítsa újra a kiszolgálót.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Ha a TLS 1,2-et a Sync Engine-kiszolgáló és egy távoli SQL Server között is engedélyezni szeretné, győződjön meg arról, hogy a szükséges verziók telepítve vannak a [tls 1,2-támogatáshoz a Microsoft SQL Server számára](https://support.microsoft.com/kb/3135244).

### <a name="dcom-prerequisites-on-the-synchronization-server"></a>DCOM-előfeltételek a szinkronizációs kiszolgálón
A szinkronizálási szolgáltatás telepítése során Azure AD Connect ellenőrzi a következő beállításkulcs jelenlétét:

- HKEY_LOCAL_MACHINE: Software\Microsoft\Ole

A beállításkulcs alatt Azure AD Connect megtekintheti, hogy a következő értékek jelennek-e meg és nem sérültek-e: 

- [MachineAccessRestriction](https://docs.microsoft.com/windows/win32/com/machineaccessrestriction)
- [MachineLaunchRestriction](https://docs.microsoft.com/windows/win32/com/machinelaunchrestriction)
- [DefaultLaunchPermission](https://docs.microsoft.com/windows/win32/com/defaultlaunchpermission)

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Az összevonás telepítésének és konfigurálásának előfeltételei
### <a name="windows-remote-management"></a>Rendszerfelügyeleti webszolgáltatások
Ha Azure AD Connectt használ AD FS vagy a webalkalmazás-proxy (WAP) üzembe helyezéséhez, tekintse meg a következő követelményeket:

* Ha a célkiszolgáló tartományhoz csatlakozik, győződjön meg arról, hogy a Windows Távoli felügyelet engedélyezve van.
  * Egy emelt szintű PowerShell-parancssorablakban használja az parancsot `Enable-PSRemoting –force` .
* Ha a célkiszolgáló nem tartományhoz csatlakoztatott WAP-gép, több további követelmény van:
  * A célszámítógépen (WAP-gép):
    * Győződjön meg arról, hogy a Windows távfelügyeleti/WS-Management (WinRM) szolgáltatás fut a szolgáltatások beépülő modulon keresztül.
    * Egy emelt szintű PowerShell-parancssorablakban használja az parancsot `Enable-PSRemoting –force` .
  * Azon a számítógépen, amelyen a varázsló fut (ha a célszámítógép nem tartományhoz csatlakozik, vagy nem megbízható tartomány):
    * Egy emelt szintű PowerShell-parancssorablakban használja az parancsot `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * A Kiszolgálókezelőben:
      * DMZ WAP-gazdagép hozzáadása számítógép-készlethez. A Kiszolgálókezelőben válassza a **kezelés**  >  **Hozzáadás kiszolgálók** elemet, majd használja a **DNS** lapot.
      * A **Kiszolgálókezelő minden kiszolgáló** lapján kattintson a jobb gombbal a WAP-kiszolgálóra, majd válassza a **kezelés másként** lehetőséget. Adja meg a WAP-gép helyi (nem tartományi) hitelesítő adatait.
      * A távoli PowerShell-kapcsolat ellenőrzéséhez a **Kiszolgálókezelő minden kiszolgáló** lapján kattintson a jobb gombbal a WAP-kiszolgálóra, és válassza a **Windows PowerShell** elemet. Egy távoli PowerShell-munkamenetnek nyitva kell lennie a távoli PowerShell-munkamenetek létrehozásához.

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-tanúsítványokra vonatkozó követelmények
* Azt javasoljuk, hogy ugyanazt a TLS/SSL-tanúsítványt használja a AD FS farm összes csomópontján és az összes webalkalmazás-proxy kiszolgálón.
* A tanúsítványnak X509-tanúsítványnak kell lennie.
* Az összevonási kiszolgálókon önaláírt tanúsítványt is használhat tesztkörnyezetben tesztkörnyezetben. Éles környezetben azt javasoljuk, hogy a tanúsítványt egy nyilvános hitelesítésszolgáltatótól szerezze be.
  * Ha olyan tanúsítványt használ, amely nem nyilvánosan megbízható, győződjön meg arról, hogy az egyes webalkalmazás-proxy kiszolgálókon telepített tanúsítvány megbízható a helyi kiszolgálón és az összes összevonási kiszolgálón.
* A tanúsítvány identitásának meg kell egyeznie az összevonási szolgáltatás nevével (például sts.contoso.com).
  * Az identitás vagy a tulajdonos alternatív neve (SAN) dNSName típusú bővítmény, vagy ha nincsenek SAN-bejegyzések, a tulajdonos neve köznapi névként van megadva.
  * A tanúsítványban több SAN-bejegyzés is szerepelhet, ha az egyikük megegyezik az összevonási szolgáltatás nevével.
  * Ha Workplace Join használatát tervezi, további SAN-t kell megadnia a enterpriseregistration értékkel **.** ezt követően a szervezet egyszerű felhasználóneve (UPN) utótagja, például enterpriseregistration.contoso.com.
* A CryptoAPI következő generációs (CNG) kulcsokon és a kulcstároló-szolgáltatókon (KSP) alapuló tanúsítványok nem támogatottak. Ennek eredményeképpen egy kriptográfiai szolgáltató (CSP) alapján kell tanúsítványt használnia, nem KSP.
* A Wild-Card tanúsítványok támogatottak.

### <a name="name-resolution-for-federation-servers"></a>Névfeloldás összevonási kiszolgálókon
* Állítsa be a DNS-rekordokat a AD FS nevére (például sts.contoso.com) az intranet (belső DNS-kiszolgáló) és az extranet (nyilvános DNS a tartományregisztráló használatával) számára. Az intranetes DNS-rekord esetében ügyeljen arra, hogy a rekordokat és a nem CNAME rekordokat használja. Rekordok használata szükséges ahhoz, hogy a Windows-hitelesítés megfelelően működjön a tartományhoz csatlakoztatott gépről.
* Ha egynél több AD FS-kiszolgálót vagy webalkalmazás-proxykiszolgálót telepít, győződjön meg arról, hogy konfigurálta a terheléselosztó nevét, és hogy a AD FS neve (például sts.contoso.com) DNS-rekordjai a terheléselosztó felé mutatnak.
* Ahhoz, hogy a Windows beépített hitelesítése az Internet Explorer használatával működjön az intraneten, győződjön meg arról, hogy a AD FS neve (például sts.contoso.com) hozzá van adva az Internet Explorerben az intranetes zónához. Ezt a követelményt Csoportházirend és a tartományhoz csatlakoztatott összes számítógépen üzembe helyezheti.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect támogató összetevők
Azure AD Connect telepíti a következő összetevőket azon a kiszolgálón, amelyen a Azure AD Connect telepítve van. Ez a lista egy alapszintű expressz telepítéshez használható. Ha más SQL Server használatát választja a **szinkronizálási szolgáltatások telepítése** lapon, az SQL Express LocalDB nincs helyileg telepítve.

* Azure AD Connect Health
* Microsoft SQL Server 2012 parancssori segédeszközök
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 natív ügyfél
* Microsoft Visual C++ 2013 újraterjesztési csomag

## <a name="hardware-requirements-for-azure-ad-connect"></a>A Azure AD Connecthez szükséges hardverkövetelmények
A következő táblázat a Azure AD Connect szinkronizálására szolgáló számítógép minimális követelményeit mutatja be.

| Objektumok száma a Active Directoryban | CPU | Memory (Memória) | Merevlemez mérete |
| --- | --- | --- | --- |
| Kevesebb mint 10 000 |1,6 GHz-es |4 GB |70 GB |
| 10000 – 50000 |1,6 GHz-es |4 GB |70 GB |
| 50000 – 100000 |1,6 GHz-es |16 GB |100 GB |
| 100 000 vagy több objektum esetén a SQL Server teljes verzióját kötelező megadni. | | | |
| 100000 – 300000 |1,6 GHz-es |32 GB |300 GB |
| 300000 – 600000 |1,6 GHz-es |32 GB |450 GB |
| Több mint 600 000 |1,6 GHz-es |32 GB |500 GB |

AD FS-vagy webalkalmazás-proxykiszolgálót futtató számítógépekre vonatkozó minimális követelmények a következők:

* CPU: kétmagos 1,6 GHz-es vagy újabb
* Memória: 2 GB vagy magasabb
* Azure-beli virtuális gép: a2 vagy újabb konfiguráció

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
