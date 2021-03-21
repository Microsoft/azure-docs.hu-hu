---
title: Telepítse a kapcsolati állapot ügynököket a Azure Active Directory
description: Ez a Azure AD Connect Health cikk a Active Directory összevonási szolgáltatások (AD FS) (AD FS) és a szinkronizálási ügynök telepítését ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3aff7b99dad910a9691eef2004df856ca883789
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224384"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health ügynök telepítése

Ebből a cikkből megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Active Directory (Azure AD) kapcsolódási állapotát. Az ügynökök letöltéséhez tekintse meg [ezeket az utasításokat](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Követelmények

A következő táblázat a Azure AD Connect Health használatának követelményeit sorolja fel.

| Követelmény | Leírás |
| --- | --- |
| Prémium szintű Azure AD (P1 vagy P2) előfizetésben van.  |A Azure AD Connect Health prémium szintű Azure AD (P1 vagy P2) funkció. További információ: [regisztráció a prémium szintű Azure ADra](../fundamentals/active-directory-get-started-premium.md). <br /><br />A 30 napos ingyenes próbaverzió indításához lásd: [próbaverzió indítása](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Ön globális rendszergazda az Azure AD-ben. |Alapértelmezés szerint csak a globális rendszergazdák telepíthetik és konfigurálhatják az állapotfigyelő ügynököket, érhetik el a portált, és a Azure AD Connect Healthon belül végezhetnek műveleteket. További információkért lásd: [Az Azure AD-címtár felügyelete](../fundamentals/active-directory-whatis.md). <br /><br /> Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával lehetővé teheti, hogy a szervezet más felhasználói hozzáférhessenek Azure AD Connect Healthhoz. További információ: [Azure RBAC for Azure ad Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Fontos**: használjon munkahelyi vagy iskolai fiókot az ügynökök telepítéséhez. Microsoft-fiók nem használható. További információ: [regisztráció az Azure-ba szervezetként](../fundamentals/sign-up-organization.md). |
| A Azure AD Connect Health ügynök telepítve van az egyes megcélzó kiszolgálókon. | Az állapotfigyelő szolgáltatásoknak a célként megadott kiszolgálókon kell telepíteniük és konfigurálniuk, hogy képesek legyenek az adatfogadásra és a figyelési és elemzési funkciók biztosítására. <br /><br />Ha például az Active Directory összevonási szolgáltatások (AD FS) (AD FS) infrastruktúra adatait szeretné lekérni, telepítenie kell az ügynököt a AD FS-kiszolgálóra és a webalkalmazás-proxy kiszolgálóra. Hasonlóképpen, ha a helyszíni Azure AD Domain Services (Azure AD DS) infrastruktúra adatait szeretné lekérni, telepítenie kell az ügynököt a tartományvezérlőkön.  |
| Az Azure szolgáltatási végpontok kimenő kapcsolattal rendelkeznek. | A telepítés és a futásidő során az ügynöknek kapcsolódnia kell az Azure AD Connect Health szolgáltatás végpontjaihoz. Ha a tűzfalak letiltják a kimenő kapcsolatot, adja hozzá a [kimenő kapcsolati végpontokat](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) az engedélyezési listához. |
|A kimenő kapcsolat IP-címeken alapul. | További információ az IP-címeken alapuló tűzfal-szűrésről: [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=56519).|
| A kimenő forgalom TLS-ellenőrzése szűrve vagy letiltva. | Előfordulhat, hogy az ügynök regisztrációs lépése vagy az adatfeltöltési műveletek sikertelenek lesznek, ha a hálózati réteg kimenő forgalmának TLS-ellenőrzése vagy leállítása történik. További információt a TLS- [ellenőrzés beállítása](/previous-versions/tn-archive/ee796230(v=technet.10))című témakörben talál. |
| A kiszolgálón a tűzfal portjai futtatják az ügynököt. |Az ügynöknek nyitva kell lennie a következő tűzfal-portok megnyitásához, hogy kommunikálni tudjon a Azure AD Connect Health szolgáltatási végpontokkal: <br /><li>443-as TCP-port</li><li>5671-es TCP-port</li> <br />Az ügynök legújabb verziója nem igényli a 5671-es portot. Frissítsen a legújabb verzióra, hogy csak a 443-es port legyen szükséges. További információ: [hibrid identitás szükséges portok és protokollok](./reference-connect-ports.md). |
| Ha az Internet Explorer fokozott biztonsága engedélyezve van, engedélyezze a megadott webhelyeket.  |Ha az Internet Explorer fokozott biztonsága engedélyezve van, engedélyezze a következő webhelyeket azon a kiszolgálón, amelyen az ügynököt telepíti:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>Az Azure AD által megbízhatónak tartott szervezet összevonási kiszolgálója (például https: \/ /STS.contoso.com)</li> <br />További információ: [az Internet Explorer konfigurálása](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Ha a hálózatban van proxyja, akkor tekintse meg a tábla végén megjelenő megjegyzést.|
| A PowerShell 4,0-es vagy újabb verziója telepítve van. | A Windows Server 2012 tartalmazza a PowerShell 3,0-es verzióját. Ez a verzió *nem* elegendő az ügynökhöz.</br></br> A Windows Server 2012 R2 és újabb verziók tartalmazzák a PowerShell megfelelő legújabb verzióját.|
|A FIPS (Federal Information Processing standard) le van tiltva.|Azure AD Connect Health ügynökök nem támogatják az FIPS-t.|

> [!IMPORTANT]
> A Windows Server Core nem támogatja a Azure AD Connect Health ügynök telepítését.


> [!NOTE]
> Ha magas rendelkezésre állású és korlátozott környezettel rendelkezik, további URL-címeket kell hozzáadnia, mint az Internet Explorer fokozott biztonsági szolgáltatásának táblázatos listája. Adja hozzá a következő szakaszban található táblázatban felsorolt URL-címeket is.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Kimenő kapcsolódás az Azure szolgáltatásvégpontokra

A telepítés és a futtatókörnyezet során az ügynöknek kapcsolódnia kell Azure AD Connect Health szolgáltatási végpontokhoz. Ha a tűzfalak letiltják a kimenő kapcsolatot, ügyeljen arra, hogy a következő táblázatban szereplő URL-címek alapértelmezés szerint ne legyenek letiltva. 

Ne tiltsa le ezen URL-címek biztonsági figyelését vagy ellenőrzését. Ehelyett engedélyezze a többi internetes forgalom engedélyezését. 

Ezek az URL-címek lehetővé teszik a Azure AD Connect Health szolgáltatásbeli végpontokkal folytatott kommunikációt. A cikk későbbi részében megtudhatja, hogyan [ellenőrizheti a kimenő kapcsolatokat](#test-connectivity-to-azure-ad-connect-health-service) a használatával `Test-AzureADConnectHealthConnectivity` .

| Tartományi környezet | Szükséges Azure-szolgáltatásvégpontok |
| --- | --- |
| Általános nyilvános | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-port: 5671 (ez a végpont nem szükséges az ügynök legújabb verziójában.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (ezt a végpontot csak felderítési célokra használja a rendszer a regisztráció során.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Germany | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (ezt a végpontot csak felderítési célokra használja a rendszer a regisztráció során.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (ezt a végpontot csak felderítési célokra használja a rendszer a regisztráció során.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>Az ügynök telepítése

Az Azure AD Connect Health-ügynök letöltése és telepítése: 

* Győződjön meg arról, hogy megfelel a Azure AD Connect Health [követelményeinek](how-to-connect-health-agent-install.md#requirements) .
* Ismerkedjen meg a AD FS Azure AD Connect Health használatával:
    * [Töltse le a AD FS Azure ad Connect Health ügynökét](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Tekintse meg a [telepítési utasításokat](#install-the-agent-for-ad-fs).
* Az Azure AD Connect Health használatának első lépései szinkronizáláshoz:
    * [Töltse le, és telepítse az Azure AD Connect legújabb verzióját.](https://go.microsoft.com/fwlink/?linkid=615771) A szinkronizálási állapot ügynöke a Azure AD Connect telepítésének részeként van telepítve (1.0.9125.0 vagy újabb verzió).
* Az Azure AD DS Azure AD Connect Health használatának első lépései:
    * [Töltse le az Azure AD DS Azure ad Connect Health-ügynökét](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Tekintse meg a [telepítési utasításokat](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Az ügynök telepítése AD FS

> [!NOTE]
> A AD FS-kiszolgálónak a szinkronizálási kiszolgálótól eltérőnek kell lennie. Ne telepítse a AD FS-ügynököt a Szinkronizáló kiszolgálóra.
>

Az ügynök telepítése előtt győződjön meg arról, hogy a AD FS-kiszolgáló állomásneve egyedi, és nem szerepel a AD FS szolgáltatásban.
Az ügynök telepítésének elindításához kattintson duplán a letöltött *. exe* fájlra. Az első ablakban válassza a **telepítés** lehetőséget.

![Képernyőfelvétel: a Azure AD Connect Health AD FS ügynök telepítési ablaka.](./media/how-to-connect-health-agent-install/install1.png)

A telepítés befejezése után válassza a **Konfigurálás most** lehetőséget.

![Képernyőfelvétel: a Azure AD Connect Health AD FS ügynök telepítésének megerősítő üzenete.](./media/how-to-connect-health-agent-install/install2.png)

Megnyílik egy PowerShell-ablak az ügynök regisztrációs folyamatának elindításához. Amikor a rendszer kéri, jelentkezzen be egy olyan Azure AD-fiókkal, amely rendelkezik az ügynök regisztrálásához szükséges engedélyekkel. Alapértelmezés szerint a globális rendszergazdai fiók rendelkezik engedéllyel.

![A Azure AD Connect Health AD FS bejelentkezési ablakát ábrázoló képernyőkép.](./media/how-to-connect-health-agent-install/install3.png)

A bejelentkezést követően a PowerShell folytatja. Amikor befejeződik, lezárhatja a PowerShellt. A konfigurálás befejeződött.

Ezen a ponton az ügynök szolgáltatásainak automatikusan el kell indulniuk ahhoz, hogy az ügynök biztonságosan töltse fel a szükséges adatok a felhőalapú szolgáltatásba.

Ha még nem teljesítette az összes előfeltételt, a figyelmeztetések megjelennek a PowerShell ablakban. Ügyeljen arra, hogy az ügynök telepítése előtt végezze el a [követelményeket](how-to-connect-health-agent-install.md#requirements) . Az alábbi képernyőképen láthatók a figyelmeztetések példái.

![Képernyőfelvétel: a Azure AD Connect Health AD FS configure script.](./media/how-to-connect-health-agent-install/install4.png)

Az ügynök telepítésének ellenőrzéséhez keresse meg a következő szolgáltatásokat a kiszolgálón. Ha a konfigurációt elvégezte, a szolgáltatásoknak már futniuk kell. Ellenkező esetben a rendszer leállítja őket, amíg a konfiguráció be nem fejeződik.

* Azure AD Connect Health AD FS Diagnostics szolgáltatás
* Azure AD Connect Health AD FS Insights szolgáltatás
* Azure AD Connect Health AD FS Monitoring szolgáltatás

![A Azure AD Connect Health AD FS Services szolgáltatást bemutató képernyőkép.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>AD FS naplózásának engedélyezése

> [!NOTE]
> Ez a szakasz csak AD FS kiszolgálókra vonatkozik. Ezeket a lépéseket nem kell követnie a webalkalmazás-proxy kiszolgálókon.
>

Az adatok gyűjtéséhez és elemzéséhez a használati elemzési szolgáltatásnak kell szerepelnie. Így a Azure AD Connect Health ügynöknek szüksége van a AD FS naplókban található információkra. Ezek a naplók alapértelmezés szerint nem engedélyezettek. A következő eljárásokkal engedélyezheti AD FS naplózását, és megkeresheti a AD FS-kiszolgálókon található AD FS naplókat.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Az AD FS naplózásának engedélyezése Windows Server 2012 R2 rendszeren

1. A kezdőképernyőn nyissa meg a **Kiszolgálókezelő alkalmazást**, majd nyissa meg a **helyi biztonsági házirendet**. Vagy a tálcán nyissa meg a **Kiszolgálókezelő alkalmazást**, majd válassza az **eszközök/helyi biztonsági házirend** elemet.
2. Lépjen a *biztonsági beállítások \ helyi felhasználói jogok kiosztása* mappába. Ezután kattintson duplán a **biztonsági naplózások előállítása** elemre.
3. A **Helyi biztonsági beállítások** lapon ellenőrizze, hogy az AD FS szolgáltatásfiók szerepel-e a listában. Ha nem szerepel a listában, válassza a **felhasználó vagy csoport hozzáadása** lehetőséget, és adja hozzá a listához. Ez után válassza az **OK** gombot.
4. A naplózás engedélyezéséhez nyisson meg egy parancssori ablakot emelt szintű jogosultságokkal. Ezt követően futtassa a következő parancsot: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Zárja be a **Helyi biztonsági házirend** lapot.
    >[!Important]
    >Az alábbi lépések csak az elsődleges AD FS-kiszolgálók esetében szükségesek. 
1. Nyissa meg az **AD FS kezelő** beépülő modulját. (A **Kiszolgálókezelőben** válassza az **eszközök**  >  lehetőséget. **AD FS felügyelet**.)
1. A **műveletek** ablaktáblán válassza a **Szerkesztés összevonási szolgáltatás tulajdonságok** elemet.
1. A **összevonási szolgáltatás tulajdonságai** párbeszédpanelen válassza az **események** lapot.
1. Jelölje be a **sikeres naplózás és a sikertelen műveletek naplózása** jelölőnégyzetet, majd kattintson **az OK gombra**.
1. A részletes naplózás a PowerShell használatával történő engedélyezéséhez használja a következő parancsot: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Az AD FS naplózásának engedélyezése Windows Server 2016 rendszeren

1. A kezdőképernyőn nyissa meg a **Kiszolgálókezelő alkalmazást**, majd nyissa meg a **helyi biztonsági házirendet**. Vagy a tálcán nyissa meg a **Kiszolgálókezelő alkalmazást**, majd válassza az **eszközök/helyi biztonsági házirend** elemet.
2. Nyissa meg a *biztonsági beállítások \ helyi felhasználói jogok kiosztása* mappát, majd kattintson duplán a **biztonsági naplózások előállítása** elemre.
3. A **Helyi biztonsági beállítások** lapon ellenőrizze, hogy az AD FS szolgáltatásfiók szerepel-e a listában. Ha nem szerepel a listában, válassza a **felhasználó vagy csoport hozzáadása** lehetőséget, és adja hozzá a AD FS szolgáltatásfiókot a listához. Ez után válassza az **OK** gombot.
4. A naplózás engedélyezéséhez nyisson meg egy parancssori ablakot emelt szintű jogosultságokkal. Ezt követően futtassa a következő parancsot: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Zárja be a **Helyi biztonsági házirend** lapot.
    >[!Important]
    >Az alábbi lépések csak az elsődleges AD FS-kiszolgálók esetében szükségesek.
1. Nyissa meg az **AD FS kezelő** beépülő modulját. (A **Kiszolgálókezelőben** válassza az **eszközök**  >  lehetőséget. **AD FS felügyelet**.)
1. A **műveletek** ablaktáblán válassza a **Szerkesztés összevonási szolgáltatás tulajdonságok** elemet.
1. A **összevonási szolgáltatás tulajdonságai** párbeszédpanelen válassza az **események** lapot.
1. Jelölje be a **sikeres naplózás és a sikertelen műveletek naplózása** jelölőnégyzetet, majd kattintson **az OK gombra**. A sikeres naplózást és a sikertelen naplózást alapértelmezés szerint engedélyezni kell.
1. Nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsot: 

    `Set-AdfsProperties -AuditLevel Verbose`

Az "alapszintű" naplózási szint alapértelmezés szerint engedélyezve van. További információ: [AD FS naplózási fejlesztés a Windows Server 2016-ben](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Az AD FS-naplók helyének meghatározása

1. Nyissa meg az **Eseménynaplót**.
2. Lépjen a **Windows-naplók** elemre, majd válassza a **Biztonság** elemet.
3. A jobb oldalon válassza az **aktuális naplók szűrése** lehetőséget.
4. Az **események forrásainál** válassza a **AD FS naplózás** lehetőséget.

    További információ a naplókkal kapcsolatban: [Operations questions](reference-connect-health-faq.md#operations-questions).

    ![Az aktuális napló szűrése ablakot ábrázoló képernyőkép. Az "eseményforrás" mezőben a "AD FS naplózás" lehetőség van kiválasztva.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Csoportházirenddel letiltható az AD FS-naplózás. Ha a AD FS naplózás le van tiltva, a bejelentkezési tevékenységekkel kapcsolatos használati elemzések nem érhetők el. Győződjön meg arról, hogy nincs olyan csoportházirend, amely letiltja AD FS naplózást.
>


## <a name="install-the-agent-for-sync"></a>Az ügynök telepítése szinkronizáláshoz

A szinkronizálás Azure AD Connect Health ügynöke automatikusan települ Azure AD Connect legújabb verziójában. Azure AD Connect szinkronizáláshoz való használatához [töltse le Azure ad Connect legújabb verzióját](https://www.microsoft.com/download/details.aspx?id=47594) , és telepítse.

Az ügynök telepítésének ellenőrzéséhez keresse meg a következő szolgáltatásokat a kiszolgálón. Ha végrehajtotta a konfigurációt, a szolgáltatásoknak már futniuk kell. Ellenkező esetben a rendszer leállítja a szolgáltatásokat, amíg a konfiguráció be nem fejeződik.

* Azure AD Connect Health Sync Insights szolgáltatás
* Azure AD Connect Health Sync Monitoring szolgáltatás

![A kiszolgálón futó szinkronizálási szolgáltatások futtatási Azure AD Connect Healthét bemutató képernyőkép.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Ne feledje, hogy a Azure AD Connect Health használatához prémium szintű Azure AD (P1 vagy P2) szükséges. Ha nincs prémium szintű Azure AD, a konfiguráció nem hajtható végre a Azure Portalban. További információt a [követelmények](how-to-connect-health-agent-install.md#requirements)című témakörben talál.
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Azure AD Connect Health manuális regisztrálása szinkronizáláshoz

Ha a szinkronizálási ügynök regisztrációjának Azure AD Connect Healthe sikertelen a Azure AD Connect sikeres telepítése után, akkor a PowerShell-paranccsal manuálisan is regisztrálhatja az ügynököt.

> [!IMPORTANT]
> Ezt a PowerShell-parancsot csak akkor használja, ha az ügynök regisztrációja sikertelen a Azure AD Connect telepítése után.
>
>

Manuálisan regisztrálja a Azure AD Connect Health-ügynököt a szinkronizáláshoz a következő PowerShell-parancs használatával. Az Azure AD Connect Health szolgáltatások csak az ügynök sikeres telepítése után indulnak el.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

A parancs a következő paramétereket fogadja:

* **AttributeFiltering**: `$true` (alapértelmezett), ha Azure ad Connect nem szinkronizálja az alapértelmezett attribútumot, és testre van szabva, hogy egy szűrt attribútum legyen beállítva. Egyéb esetben használja a t `$false` .
* **StagingMode**: `$false` (alapértelmezett), ha a Azure ad Connect-kiszolgáló *nem* átmeneti módban van. Ha a kiszolgáló átmeneti módban van konfigurálva, használja a következőt: `$true` .

Ha a rendszer hitelesítésre kéri, használja ugyanazt a globális rendszergazdai fiókot (például admin@domain.onmicrosoft.com ), amelyet a Azure ad Connect konfigurálásához használt.

## <a name="install-the-agent-for-azure-ad-ds"></a>Az Azure AD DS-ügynök telepítése

Az ügynök telepítésének elindításához kattintson duplán a letöltött *. exe* fájlra. Az első ablakban válassza a **telepítés** lehetőséget.

![A AD DS telepítési ablak Azure AD Connect Health ügynökét bemutató képernyőkép.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

A telepítés befejeződése után válassza a **Konfigurálás most** lehetőséget.

![Képernyőfelvétel: az Azure AD DS Azure AD Connect Health ügynökének telepítését befejező ablak.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Megnyílik egy parancssori ablak. A PowerShell futtatása `Register-AzureADConnectHealthADDSAgent` . Amikor a rendszer kéri, jelentkezzen be az Azure-ba.

![Képernyőfelvétel: az Azure AD DS Azure AD Connect Health ügynökének bejelentkezési ablaka.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

A bejelentkezést követően a PowerShell folytatja. Amikor befejeződik, lezárhatja a PowerShellt. A konfigurálás befejeződött.

Ezen a ponton a szolgáltatásoknak automatikusan el kell indulnia, így az ügynök figyelheti és gyűjtheti az adatokat. Ha még nem teljesítette az előző részekben ismertetett előfeltételeket, a figyelmeztetések megjelennek a PowerShell ablakban. Ügyeljen arra, hogy az ügynök telepítése előtt végezze el a [követelményeket](how-to-connect-health-agent-install.md#requirements) . Az alábbi képernyőképen láthatók a figyelmeztetések példái.

![Képernyőfelvétel: az Azure AD DS Configuration Azure AD Connect Health ügynökének figyelmeztetése.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Az ügynök telepítésének ellenőrzéséhez keresse meg a következő szolgáltatásokat a tartományvezérlőn:

* Azure AD Connect Health AD DS Insights szolgáltatás
* Azure AD Connect Health AD DS Monitoring szolgáltatás

Ha a konfigurációt elvégezte, ezeknek a szolgáltatásoknak már futniuk kell. Ellenkező esetben a rendszer leállítja őket, amíg a konfiguráció be nem fejeződik.

![A tartományvezérlőn futó szolgáltatásokat bemutató képernyőkép.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Az ügynök gyors telepítése több kiszolgálóra

1. Hozzon létre egy felhasználói fiókot az Azure AD-ben. Biztonságos IT jelszó használatával.
2. Rendelje hozzá a helyi Azure AD-fiók **tulajdonosi** szerepkörét Azure ad Connect Health a portál használatával. Kövesse [az alábbi lépéseket](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Rendelje hozzá a szerepkört az összes szolgáltatási példányhoz. 
3. Töltse le az *. exe* msi-fájlt a helyi tartományvezérlőn a telepítéshez.
4. Futtassa az alábbi parancsfájlt. Cserélje le a paramétereket az új felhasználói fiókra és a jelszavára. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

A befejezést követően a következő feladatok közül egyet vagy többet is eltávolíthat a helyi fiók eléréséhez: 
* Távolítsa el Azure AD Connect Health helyi fiókjához tartozó szerepkör-hozzárendelést.
* A helyi fiók jelszavának elforgatása. 
* Tiltsa le az Azure AD helyi fiókját.
* Törölje az Azure AD helyi fiókját.  

## <a name="register-the-agent-by-using-powershell"></a>Az ügynök regisztrálása a PowerShell használatával

A megfelelő ügynök *setup.exe* fájljának telepítése után a szerepkörtől függően az alábbi PowerShell-parancsokkal regisztrálhatja az ügynököt. Nyisson meg egy PowerShell-ablakot, és futtassa a megfelelő parancsot:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> A szuverén felhőkbe való regisztrációhoz használja a következő parancssort:
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


Ezek a parancsok `Credential` paraméterként fogadják el a regisztrációt nem interaktív módon, vagy a regisztráció befejezéséhez a Server Core-t futtató gépeken. Ne feledje, hogy:
* A rögzítést `Credential` egy paraméterként átadott PowerShell-változóban végezheti el.
* Megadhat bármely olyan Azure AD-identitást, amely jogosult az ügynökök regisztrálására, és amelyen *nincs* engedélyezve a többtényezős hitelesítés.
* Alapértelmezés szerint a globális rendszergazdák rendelkeznek az ügynökök regisztrálásához szükséges engedélyekkel. Ennek a lépésnek a végrehajtásához kevesebb jogosultsággal rendelkező identitás is engedélyezhető. További információ: [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Azure AD Connect Health-ügynökök konfigurálása HTTP-proxy használatára

Beállíthatja, hogy Azure AD Connect Health ügynökök HTTP-proxyval működjenek.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` nem támogatott. Az ügynök a Windows HTTP-szolgáltatások helyett System.Net használ a webes kérések létrehozásához.
> * A konfigurált HTTP-proxy címe a titkosított HTTPS-üzenetek továbbítására szolgál.
> * A hitelesített proxyk (HTTPBasic használatával) nem támogatottak.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Az ügynök proxy konfigurációjának módosítása

Ha a Azure AD Connect Health-ügynököt HTTP-proxy használatára szeretné konfigurálni, a következőket teheti:
* Importálja a meglévő proxybeállításokat.
* A proxycímek manuális meghatározása.
* Törölje a meglévő proxy konfigurációját.

> [!NOTE]
> A proxybeállítások frissítéséhez újra kell indítania a Azure AD Connect Health ügynök szolgáltatásait. Futtassa az alábbi parancsot:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Meglévő proxybeállítások importálása

Az Internet Explorer HTTP-proxy beállításainak importálásával a Azure AD Connect Health ügynökök használhatják a beállításokat. Az állapotfigyelő ügynököt futtató összes kiszolgálón futtassa a következő PowerShell-parancsot:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

A WinHTTP-proxybeállításokat importálhatja, hogy a Azure AD Connect Health ügynökök használhassák őket. Az állapotfigyelő ügynököt futtató összes kiszolgálón futtassa a következő PowerShell-parancsot:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Proxycímek manuális meghatározása

A proxykiszolgálót manuálisan is megadhatja. Az állapotfigyelő ügynököt futtató összes kiszolgálón futtassa a következő PowerShell-parancsot:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Bemutatunk egy példát: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

Ebben a példában:
* A `address` beállítás lehet DNS-feloldható kiszolgáló neve vagy IPv4-címe.
* Kihagyhatja `port` . Ha így tesz, a 443 az alapértelmezett port.

#### <a name="clear-the-existing-proxy-configuration"></a>A meglévő proxy konfigurációjának törlése

A meglévő proxykonfigurációt a következő parancs futtatásával törölheti:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Meglévő proxybeállítások olvasása

Az aktuális proxybeállításokat a következő parancs futtatásával olvashatja:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health szolgáltatáshoz való kapcsolódás tesztelése

Alkalmanként a Azure AD Connect Health ügynök elveszítheti a kapcsolatot a Azure AD Connect Health szolgáltatással. A kapcsolat elvesztésének okai lehetnek hálózati problémák, jogosultsági problémák és egyéb problémák.

Ha az ügynök két óránál hosszabb ideig nem tud adatküldést küldeni a Azure AD Connect Health szolgáltatásnak, a következő riasztás jelenik meg a portálon: "Állapotfigyelő szolgáltatás adatok nem naprakészek." 

A következő PowerShell-parancs futtatásával megtudhatja, hogy az érintett Azure AD Connect Health ügynök feltöltheti-e az adatok Azure AD Connect Health szolgáltatásba való feltöltését:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

A szerepkör-paraméter a következő értékeket veheti:

* ADFS
* Sync
* ADDS

> [!NOTE]
> A kapcsolódási eszköz használatához először regisztrálnia kell az ügynököt. Ha nem tudja befejezni az ügynök regisztrációját, győződjön meg arról, hogy teljesítette a Azure AD Connect Health összes [követelményét](how-to-connect-health-agent-install.md#requirements) . Az ügynök regisztrálása során a rendszer alapértelmezés szerint teszteli a kapcsolatot.
>
>

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő kapcsolódó cikkeket:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health műveletek](how-to-connect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](how-to-connect-health-sync.md)
* [Azure AD Connect Health használata az Azure-ban AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](reference-connect-health-version-history.md)
