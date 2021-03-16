---
title: 'Azure AD Connect: fiókok és engedélyek | Microsoft Docs'
description: Ez a témakör a használt és létrehozott fiókokat, valamint a szükséges engedélyeket ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f7466931537745fb188a3bdb05646bff19912e8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466252"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Fiókok és engedélyek

## <a name="accounts-used-for-azure-ad-connect"></a>Azure AD Connect használt fiókok

![fiókok – áttekintés](media/reference-connect-accounts-permissions/account5.png)

A Azure AD Connect 3 fiókot használ, hogy szinkronizálja a helyszíni vagy a Windows Server-Active Directory információit Azure Active Directoryre.  Ezek a fiókok a következők:

- **AD DS Connector-fiók**: adatok olvasása/írása a Windows serverbe Active Directory

- **AdSync szolgáltatásfiók**: a szinkronizálási szolgáltatás futtatásához és az SQL-adatbázis eléréséhez használatos

- **Azure ad Connector-fiók**: az adatok az Azure ad-be való írásához használatos

A Azure AD Connect futtatásához használt három fiókon kívül a Azure AD Connect telepítéséhez további fiókokra is szüksége lesz.  Ezek a következők:

- **Helyi rendszergazdai fiók**: az a rendszergazda, aki telepíti Azure ad Connect, és aki helyi rendszergazdai engedélyekkel rendelkezik a gépen.

- **AD DS vállalati rendszergazdai fiók**: igény szerint a "AD DS Connector-fiók" létrehozásához.

- **Azure ad globális rendszergazdai fiók**: az Azure ad Connector-fiók létrehozásához és az Azure ad konfigurálásához használatos.  A Azure Portal globális rendszergazdai fiókokat is megtekintheti.  Lásd: [Azure ad-szerepkör-hozzárendelések listázása](../../active-directory/roles/view-assignments.md).

- **SQL SA-fiók (nem kötelező)**: a AdSync-adatbázis létrehozásához használatos az SQL Server teljes verziójának használatakor.  Ez a SQL Server lehet helyi vagy távoli a Azure AD Connect telepítéshez.  Ez a fiók lehet a vállalati rendszergazda fiókja.  Az adatbázis üzembe helyezése mostantól az SQL-rendszergazda sávon kívül is elvégezhető, majd a Azure AD Connect rendszergazdája telepítheti az adatbázis-tulajdonosi jogosultságokkal.  További információ: [Install Azure ad Connect az SQL meghatalmazott rendszergazdai engedélyeivel](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> A build 1.4. # # #. # alapján már nem támogatott vállalati rendszergazda vagy tartományi rendszergazdai fiók használata AD DS-összekötő fiókként.  Ha olyan fiókot próbál meg megadni, amely vállalati rendszergazda vagy tartományi rendszergazda a **meglévő fiók használatakor**, hibaüzenetet fog kapni.

> [!NOTE]
> A ESAE felügyeleti erdőben Azure AD Connect használt rendszergazdai fiókok felügyelete (más néven "vörös erdő") támogatott.
> A dedikált felügyeleti erdők lehetővé teszik a szervezetek számára a rendszergazdai fiókok, munkaállomások és csoportok üzemeltetését az üzemi környezetnél erősebb biztonságú környezetben.
> Ha többet szeretne megtudni a dedikált adminisztratív erdőkről, tekintse meg a [ESAE felügyeleti erdő kialakítási megközelítését](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> A kezdeti beállítás után a globális rendszergazdai szerepkör nem szükséges, és az egyetlen szükséges fiók lesz a **címtár-szinkronizálási fiókok** szerepkör fiókja. Ez nem feltétlenül jelenti azt, hogy csak a globális rendszergazdai szerepkörrel rendelkező fiókot kívánja eltávolítani. Jobb megoldás, ha a szerepkört egy kevésbé hatékony szerepkörre módosítja, mivel a fiók teljes eltávolításával problémák léphetnek fel, ha újra futtatni kell a varázslót. A szerepkör jogosultságának csökkentésével bármikor újra megemelheti a jogosultságokat, ha újra kell használnia a Azure AD Connect varázslót. 

## <a name="installing-azure-ad-connect"></a>Azure AD Connect telepítése
A Azure AD Connect telepítővarázsló két különböző elérési utat biztosít:

* Az expressz beállítások területen a varázslónak több jogosultságra van szüksége.  Így egyszerűen beállíthatja a konfigurációt, anélkül, hogy felhasználókat kellene létrehoznia, vagy engedélyeket kell konfigurálnia.
* Az egyéni beállítások területen a varázsló további lehetőségeket és lehetőségeket kínál. Vannak azonban olyan helyzetek, amikor meg kell győződnie arról, hogy a megfelelő engedélyekkel rendelkezik.



## <a name="express-settings-installation"></a>Expressz beállítások telepítése
Az expressz beállításokban a telepítővarázsló a következőket kéri:

  - Vállalati rendszergazdai hitelesítő adatok AD DS
  - Az Azure AD globális rendszergazdai hitelesítő adatai

### <a name="ad-ds-enterprise-admin-credentials"></a>Vállalati rendszergazdai hitelesítő adatok AD DS
A AD DS vállalati rendszergazdai fiók a helyszíni Active Directory konfigurálására szolgál. Ezeket a hitelesítő adatokat a rendszer csak a telepítés során használja, és a telepítés befejezése után nem használja őket. A vállalati rendszergazdának, nem pedig a tartományi rendszergazdának meg kell győződnie arról, hogy az összes tartományban be van-e állítva az engedélyek a Active Directoryban.

Ha a-ről származó frissítést használja, a AD DS vállalati rendszergazdák hitelesítő adataival alaphelyzetbe állíthatja az rSync által használt fiók jelszavát. Szüksége lesz az Azure AD globális rendszergazdai hitelesítő adataira is.

### <a name="azure-ad-global-admin-credentials"></a>Az Azure AD globális rendszergazdai hitelesítő adatai
Ezeket a hitelesítő adatokat a rendszer csak a telepítés során használja, és a telepítés befejezése után nem használja őket. A rendszer az Azure ad-beli módosítások szinkronizálásához használt Azure AD Connector-fiók létrehozásához használatos. A fiók az Azure AD szolgáltatásban is lehetővé teszi a szinkronizálást.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS Connector-fiókhoz szükséges engedélyek az expressz beállításokhoz
Az AD DS-összekötő fiók a Windows Server AD-hoz való olvasásra és írásra van létrehozva, és az alábbi engedélyekkel rendelkezik az expressz beállításokkal történő létrehozáskor:

| Engedély | Alkalmazási cél |
| --- | --- |
| <li>Címtárbeli módosítások replikálása</li><li>A címtár összes módosításának replikálása |Jelszó kivonatának szinkronizálása |
| Az összes tulajdonság olvasása/írása felhasználó |Importálás és Exchange hibrid |
| Az összes tulajdonság olvasása/írása iNetOrgPerson |Importálás és Exchange hibrid |
| Az összes tulajdonság csoport olvasása/írása |Importálás és Exchange hibrid |
| Az összes tulajdonság olvasása/írása kapcsolat |Importálás és Exchange hibrid |
| Új jelszó létrehozása |A jelszó-visszaírási engedélyezésének előkészítése |

### <a name="express-installation-wizard-summary"></a>Az expressz telepítési varázsló összegzése

![Expressz telepítés](./media/reference-connect-accounts-permissions/express.png)

A következő összefoglalás az expressz telepítővarázsló lapjairól, a gyűjtött hitelesítő adatokról és azok használatáról.

| Varázsló lapja | Összegyűjtött hitelesítő adatok | Szükséges engedélyek | Használatban |
| --- | --- | --- | --- |
| N/A |A telepítő varázslót futtató felhasználó |A helyi kiszolgáló rendszergazdája |<li>Létrehozza a szinkronizálási szolgáltatás futtatásához használt ADSync-szolgáltatásfiókot. |
| Csatlakozás az Azure AD szolgáltatáshoz |Azure AD-címtár hitelesítő adatai |Globális rendszergazdai szerepkör az Azure AD-ben |<li>A szinkronizálás engedélyezése az Azure AD-címtárban.</li>  <li>A folyamatban lévő szinkronizálási műveletekhez használt Azure AD Connector-fiók létrehozása az Azure AD-ben.</li> |
| Csatlakozás az AD DS szolgáltatáshoz |Helyszíni Active Directory hitelesítő adatok |A vállalati rendszergazdák (EA) csoport tagja Active Directory |<li>Létrehozza a AD DS-összekötő fiókot a Active Directoryban, és engedélyt ad hozzá. Ez a létrehozott fiók a címtáradatok olvasására és írására szolgál a szinkronizálás során.</li> |


## <a name="custom-installation-settings"></a>Egyéni telepítési beállítások

Az egyéni beállítások telepítésével a varázsló további lehetőségeket és lehetőségeket kínál. 

### <a name="custom-installation-wizard-summary"></a>Egyéni telepítési varázsló összegzése

A következő összefoglalja az egyéni telepítővarázsló oldalait, a gyűjtött hitelesítő adatokat és azok használati feladatait.

![Képernyőkép, amely az egyéni telepítővarázsló lapjait jeleníti meg.](./media/reference-connect-accounts-permissions/customize.png)

| Varázsló lapja | Összegyűjtött hitelesítő adatok | Szükséges engedélyek | Használatban |
| --- | --- | --- | --- |
| N/A |A telepítő varázslót futtató felhasználó |<li>A helyi kiszolgáló rendszergazdája</li><li>Ha teljes SQL Server használ, a felhasználónak rendszergazda (SA) értékűnek kell lennie az SQL-ben</li> |Alapértelmezés szerint a a szinkronizálási motor szolgáltatásfiókként használt helyi fiókot hozza létre. A fiók csak akkor jön létre, ha a rendszergazda nem ad meg egy adott fiókot. |
| Szinkronizálási szolgáltatások telepítése, szolgáltatásfiók beállítás |AD-vagy helyi felhasználói fiók hitelesítő adatai |Felhasználó, a telepítővarázsló engedélyeket kap |Ha a rendszergazda megad egy fiókot, a rendszer ezt a fiókot használja a szinkronizálási szolgáltatáshoz. |
| Csatlakozás az Azure AD szolgáltatáshoz |Azure AD-címtár hitelesítő adatai |Globális rendszergazdai szerepkör az Azure AD-ben |<li>A szinkronizálás engedélyezése az Azure AD-címtárban.</li>  <li>A folyamatban lévő szinkronizálási műveletekhez használt Azure AD Connector-fiók létrehozása az Azure AD-ben.</li> |
| Csatlakoztassa a címtárakat |Helyszíni Active Directory hitelesítő adatok az Azure AD-hez csatlakozó összes erdőhöz |Az engedélyek attól függnek, hogy mely szolgáltatásokat engedélyezte, és hol található az AD DS-összekötő fiók létrehozása |Ez a fiók a címtár adatainak a szinkronizálás során történő olvasására és írására szolgál. |
| AD FS-kiszolgálók |A varázsló a listában szereplő összes kiszolgálónál a hitelesítő adatokat gyűjti, ha a varázslót futtató felhasználó bejelentkezési hitelesítő adatai nem elegendőek a csatlakozáshoz |Tartományi rendszergazda |A AD FS kiszolgálói szerepkör telepítése és konfigurálása. |
| Webalkalmazás-proxy kiszolgálók |A varázsló a listában szereplő összes kiszolgálónál a hitelesítő adatokat gyűjti, ha a varázslót futtató felhasználó bejelentkezési hitelesítő adatai nem elegendőek a csatlakozáshoz |Helyi rendszergazda a célszámítógépen |A WAP-kiszolgálói szerepkör telepítése és konfigurálása. |
| Proxy megbízhatóságának hitelesítő adatai |Összevonási szolgáltatás megbízhatósági hitelesítő adatai (a proxy által a megbízhatósági tanúsítvány regisztrálásához használt hitelesítő adatok az FS-ből |Tartományi fiók, amely a AD FS kiszolgáló helyi rendszergazdája |Az FS-WAP megbízhatósági tanúsítvány kezdeti beléptetése. |
| AD FS szolgáltatásfiók lap "tartományi felhasználói fiók használata" beállítás |AD felhasználói fiók hitelesítő adatai |Tartományi felhasználó |Az Azure AD felhasználói fiók, amelynek hitelesítő adatait a rendszer a AD FS szolgáltatás bejelentkezési fiókjának használja. |

### <a name="create-the-ad-ds-connector-account"></a>Az AD DS-összekötő fiók létrehozása

>[!IMPORTANT]
>A ADSyncConfig. psm1 nevű új PowerShell-modult a Build **1.1.880.0** (2018 augusztusában kiadott verzió) vezette be, amely a parancsmagok gyűjteményét tartalmazza, amely segítséget nyújt az Azure AD DS Connector-fiókhoz tartozó helyes Active Directory engedélyek konfigurálásában.
>
>További információ [: Azure ad Connect: AD DS-összekötő fiókjának konfigurálása engedély](how-to-connect-configure-ad-ds-connector-account.md)

A telepítés előtt a **címtár összekapcsolása** lapon megadott fióknak jelen kell lennie Active Directoryban.  Azure AD Connect verzió 1.1.524.0 és újabb verziója lehetővé teszi, hogy a Azure AD Connect varázsló létrehozza a Active Directoryhoz való kapcsolódáshoz használt **AD DS-összekötő fiókját** .  

Emellett meg kell adni a szükséges engedélyeket is. A telepítővarázsló nem ellenőrzi, hogy az engedélyek és a problémák csak a szinkronizálás során találhatók-e.

A szükséges engedélyek a választható funkcióktól függenek. Ha több tartománnyal rendelkezik, az engedélyeket az erdő összes tartományához meg kell adni. Ha nem engedélyezi ezen funkciók bármelyikét, az alapértelmezett **tartományi felhasználói** engedélyek elegendőek.

| Szolgáltatás | Engedélyek |
| --- | --- |
| MS-DS-ConsistencyGuid funkció |Írási engedélyek a tervezési fogalmakban dokumentált ms-DS-ConsistencyGuid attribútumhoz [– MS-DS-ConsistencyGuid használata sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Jelszó kivonatának szinkronizálása |<li>Címtárbeli módosítások replikálása</li>  <li>A címtár összes módosításának replikálása |
| Hibrid Exchange-telepítés |Írási engedélyek a felhasználók, csoportok és névjegyek [Exchange hibrid visszaírási](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dokumentált attribútumokhoz. |
| Exchange-levelezés nyilvános mappája |Olvasási engedélyek a nyilvános mappák Exchange- [levelezés nyilvános mappájában](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dokumentált attribútumokhoz. | 
| Jelszóvisszaíró |Írási engedélyek a felhasználók [jelszavas kezelésének megkezdése](../authentication/tutorial-enable-sspr-writeback.md) című dokumentumban ismertetett attribútumokhoz. |
| Eszközvisszaíró |A PowerShell-parancsfájllal megadott engedélyek a [Device visszaírási](how-to-connect-device-writeback.md)című cikkben leírtak szerint. |
| Group writeback (Csoportvisszaíró) |Lehetővé teszi, hogy visszaírási **Microsoft 365 csoportokat** egy olyan erdőhöz, amelyen telepítve van az Exchange.|

## <a name="upgrade"></a>Frissítés
Ha a Azure AD Connect egyik verziójáról egy új kiadásra frissít, a következő engedélyek szükségesek:

>[!IMPORTANT]
>A build 1.1.484 kezdve a Azure AD Connect egy regressziós hibát vezetett be, amelyhez rendszergazdai jogosultság szükséges az SQL-adatbázis frissítéséhez.  Ezt a hibát kijavítottuk a build 1.1.647.  Ha erre a buildre frissít, rendszergazdai jogosultságokra lesz szüksége.  A dbo engedélyei nem elegendőek.  Ha rendszergazdai jogosultságok nélkül kísérli meg Azure AD Connect frissítését, a frissítés sikertelen lesz, és a Azure AD Connect nem fog megfelelően működni.  A Microsoft tisztában van ezzel, és ezzel a megoldással működik.


| Rendszerbiztonsági tag | A szükséges engedélyek | Alkalmazási cél |
| --- | --- | --- |
| A telepítő varázslót futtató felhasználó |A helyi kiszolgáló rendszergazdája |Bináris fájlok frissítése. |
| A telepítő varázslót futtató felhasználó |ADSyncAdmins tagja |Módosítsa a szinkronizálási szabályokat és az egyéb beállításokat. |
| A telepítő varázslót futtató felhasználó |Ha teljes SQL Server-kiszolgálót használ: a szinkronizálási motor adatbázisának DBO (vagy hasonló) |Végezze el az adatbázis szintjének módosítását, például a táblázatok új oszlopokkal való frissítését. |

## <a name="more-about-the-created-accounts"></a>További információ a létrehozott fiókokról
### <a name="ad-ds-connector-account"></a>AD DS Connector-fiók
Ha az expressz beállításokat használja, akkor a rendszer a szinkronizáláshoz használt Active Directory hozza létre a fiókot. A létrehozott fiók az erdő gyökértartományában található a felhasználók tárolóban, és a neve **MSOL_tel** előtaggal van ellátva. A fiók olyan hosszú, összetett jelszóval jön létre, amely nem jár le. Ha a tartományban jelszavas házirend van, ügyeljen arra, hogy a fiókhoz hosszú és összetett jelszavak legyenek engedélyezve.

![AD-fiók](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Ha egyéni beállításokat használ, akkor a telepítés megkezdése előtt Ön a felelős a fiók létrehozásához.  Lásd: az AD DS-összekötő fiók létrehozása.

### <a name="adsync-service-account"></a>ADSync-szolgáltatásfiók
A szinkronizálási szolgáltatás más fiókokban is futhat. Egy **virtuális szolgáltatásfiók** (VSA), egy **csoportosan felügyelt szolgáltatásfiók** (gMSA/önállóan felügyelt szolgáltatásfiókot) vagy egy normál felhasználói fiók használatával futtatható. A támogatott beállítások a csatlakozás április 2017-os verziójával módosultak, amikor új telepítést végez. Ha Azure AD Connect korábbi kiadásáról frissít, ezek a további beállítások nem érhetők el.

| Fiók típusa | Telepítési lehetőség | Leírás |
| --- | --- | --- |
| [Virtual Service-fiók](#virtual-service-account) | Express és Custom, 2017 április és újabb | Ez a beállítás az összes expressz telepítéshez használatos, a tartományvezérlőn lévő telepítések kivételével. Egyéni esetén ez az alapértelmezett beállítás, kivéve, ha egy másik lehetőség van használatban. |
| [Csoportosan felügyelt szolgáltatásfiók](#group-managed-service-account) | Custom, 2017 április és újabb | Ha távoli SQL Servert használ, javasoljuk, hogy egy csoportosan felügyelt szolgáltatásfiókot használjon. |
| [Felhasználói fiók](#user-account) | Express és Custom, 2017 április és újabb | AAD_-előtaggal rendelkező felhasználói fiók csak a telepítés során jön létre, amikor a Windows Server 2008 rendszerre van telepítve, és a tartományvezérlőre van telepítve. |
| [Felhasználói fiók](#user-account) | Express és Custom, 2017 március és korábbi verziók | A telepítés során a rendszer létrehoz egy AAD_tel előre rögzített helyi fiókot. Egyéni telepítés használata esetén egy másik fiók is megadható. |

Ha a (z) 2017 március vagy korábbi verzióról származó buildet használ, ne állítsa alaphelyzetbe a szolgáltatásfiók jelszavát, mivel a Windows biztonsági okokból elpusztítja a titkosítási kulcsokat. A fiók nem módosítható más fiókra Azure AD Connect újratelepítése nélkül. Ha az 2017 április vagy újabb verzióra frissít egy buildre, akkor a szolgáltatás fiókjának jelszava módosítható, de a használt fiók nem módosítható.

> [!Important]
> A szolgáltatásfiókot csak az első telepítéskor lehet beállítani. A szolgáltatásfiók módosítása a telepítés befejeződése után nem támogatott.

Ez a szinkronizálási szolgáltatásfiók alapértelmezett, ajánlott és támogatott beállításainak táblázata.

Jelmagyarázat:

- A **Bold** az alapértelmezett beállítást jelzi, a legtöbb esetben az ajánlott beállítást.
- A *dőlt* érték azt jelzi, hogy a javasolt beállítás nem az alapértelmezett beállítás.
- 2008 – alapértelmezett beállítás a Windows Server 2008 rendszerre való telepítéskor
- Nem félkövérrel támogatott beállítás
- Helyi fiók – helyi felhasználói fiók a kiszolgálón
- Tartományi fiók – tartományi felhasználói fiók
- Önállóan felügyelt szolgáltatásfiókot – [önálló felügyelt szolgáltatásfiók](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA – [csoportosan felügyelt szolgáltatásfiók](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Egyéni | Távoli SQL</br>Egyéni |
| --- | --- | --- | --- |
| **tartományhoz csatlakoztatott számítógép** | **VSA**</br>Helyi fiók (2008) | **VSA**</br>Helyi fiók (2008)</br>Helyi fiók</br>Tartományi fiók</br>Önállóan felügyelt szolgáltatásfiókot, gMSA | **gMSA**</br>Tartományi fiók |
| **Tartományvezérlő** | **Tartományi fiók** | *gMSA*</br>**Tartományi fiók**</br>Önállóan felügyelt szolgáltatásfiókot| *gMSA*</br>**Tartományi fiók**|

#### <a name="virtual-service-account"></a>Virtual Service-fiók
A virtuális szolgáltatásfiók olyan speciális fióktípus, amely nem rendelkezik jelszóval, és a Windows felügyeli.

![A virtuális szolgáltatásfiókot (VSA) megjelenítő képernyőkép.](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

A VSA olyan forgatókönyvekkel használható, amelyekben a Szinkronizáló motor és az SQL ugyanazon a kiszolgálón található. Ha a távoli SQL-t használja, azt javasoljuk, hogy ehelyett egy csoportosan felügyelt szolgáltatásfiókot használjon.

Ehhez a szolgáltatáshoz Windows Server 2008 R2 vagy újabb rendszer szükséges. Ha a Azure AD Connect telepítését a Windows Server 2008-es verzióra telepíti, akkor a telepítés helyett egy [felhasználói fiók](#user-account) használatára kerül vissza.

#### <a name="group-managed-service-account"></a>Csoportosan felügyelt szolgáltatásfiók
Ha távoli SQL Servert használ, azt javasoljuk, hogy egy **csoportosan felügyelt szolgáltatásfiókot** használjon. A Active Directory csoportosan felügyelt szolgáltatásfiók előkészítésével kapcsolatos további információkért lásd: [csoportosan felügyelt szolgáltatásfiókok áttekintése](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

Ha ezt a beállítást szeretné használni, a [szükséges összetevők telepítése](how-to-connect-install-custom.md#install-required-components) lapon jelölje be a **meglévő szolgáltatásfiók használata** jelölőnégyzetet, és válassza a **felügyelt szolgáltatásfiók** lehetőséget.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
[Önálló felügyelt szolgáltatásfiók](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))használata is támogatott. Ezek azonban csak a helyi gépen használhatók, és nem használhatók fel az alapértelmezett Virtual Service-fiókra.

Ehhez a szolgáltatáshoz a Windows Server 2012-es vagy újabb verziója szükséges. Ha régebbi operációs rendszert kell használnia, és a távoli SQL-t kell használnia, [felhasználói fiókot](#user-account)kell használnia.

#### <a name="user-account"></a>Felhasználói fiók
A telepítővarázsló létrehoz egy helyi szolgáltatásfiókot (kivéve, ha megadja az egyéni beállításokban használandó fiókot). A fiók előre rögzített **AAD_** és a tényleges szinkronizálási szolgáltatás futtatására szolgál. Ha a Azure AD Connect tartományvezérlőre telepíti, a fiók a tartományban jön létre. A **AAD_** szolgáltatás fiókjának a tartományban kell lennie, ha:
   - SQL Servert futtató távoli kiszolgálót használ
   - hitelesítést igénylő proxyt használ

![Szinkronizáló szolgáltatás fiókja](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A fiók olyan hosszú, összetett jelszóval jön létre, amely nem jár le.

Ez a fiók biztonságos módon tárolja a többi fiók jelszavait. Az egyéb fiókok jelszavait az adatbázisban titkosítva tárolja a rendszer. A titkosítási kulcsokhoz tartozó titkos kulcsokat a Windows adatvédelmi API (DPAPI) használatával védi a titkosítási szolgáltatásoknak a titkos kulcs titkosításával.

Ha teljes SQL Server használ, a szolgáltatásfiók a létrehozott adatbázis DBO a Szinkronizáló motorhoz. A szolgáltatás nem fog működni más engedélyekkel. Létrejön egy SQL-bejelentkezés is.

A fiók a fájlokhoz, a kulcsokhoz és a szinkronizálási motorhoz kapcsolódó egyéb objektumokhoz is biztosít engedélyeket.

### <a name="azure-ad-connector-account"></a>Azure AD Connector-fiók
Létrejön egy fiók az Azure AD-ben a szinkronizálási szolgáltatás használatára. Ezt a fiókot a megjelenített nevével lehet azonosítani.

![Képernyőkép, amely az Azure AD-fiókot jeleníti meg.](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Annak a kiszolgálónak a neve, amelyen a fiók használatban van, a Felhasználónév második részében azonosítható. A képen a kiszolgálónév a DC1. Ha átmeneti kiszolgálóval rendelkezik, az egyes kiszolgálók saját fiókkal rendelkeznek.

A fiók olyan hosszú, összetett jelszóval jön létre, amely nem jár le. Olyan speciális szerepkörű címtár- **szinkronizálási fiókokat** kap, amelyek csak a címtár-szinkronizálási feladatok végrehajtásához szükségesek. Ez a speciális beépített szerepkör nem adható meg a Azure AD Connect varázslón kívül. A Azure Portal megjeleníti ezt a fiókot a szerepkör **felhasználójának**.

Az Azure AD-ben legfeljebb 20 szinkronizálási szolgáltatásfiók szerepel. Az Azure ad-beli meglévő Azure AD-szolgáltatásfiókok listájának lekéréséhez futtassa a következő Azure AD PowerShell-parancsmagot: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

A nem használt Azure AD-szolgáltatásfiókok eltávolításához futtassa a következő Azure AD PowerShell-parancsmagot: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>A fenti PowerShell-parancsok használata előtt telepítenie kell a [Graph modulhoz tartozó Azure Active Directory PowerShellt](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) , és csatlakoznia kell az Azure ad-példányához a [AzureAD](/powershell/module/azuread/connect-azuread) használatával

Az Azure AD Connector-fiók jelszavának kezelésével és alaphelyzetbe állításával kapcsolatos további információkért lásd: [a Azure ad Connect fiók kezelése](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Kapcsolódó dokumentáció
Ha nem olvassa be a helyszíni [identitások Azure Active Directory használatával történő integrálásához](whatis-hybrid-identity.md)szükséges dokumentációt, a következő táblázat a kapcsolódó témakörökre mutató hivatkozásokat tartalmaz.

|Témakör |Hivatkozás|  
| --- | --- |
|Az Azure AD Connect letöltése | [Az Azure AD Connect letöltése](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Telepítés gyorsbeállítások használatával | [Az Azure AD Connect gyorstelepítése](how-to-connect-install-express.md)|
|Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](./how-to-connect-install-custom.md)|
|Frissítés a DirSync szolgáltatásról | [Frissítés az Azure AD szinkronizáló eszközéről (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|A telepítés után | [A telepítés ellenőrzése és licencek hozzárendelése](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
