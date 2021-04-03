---
title: Azure Active Directory Connect telepítésének testreszabása
description: Ez a cikk a Azure AD Connect egyéni telepítési beállításait ismerteti. Ezeknek az utasításoknak a használatával telepítheti az Active Directoryt az Azure AD Connecten keresztül.
services: active-directory
keywords: mi az Azure AD Connect, az Active Directory telepítése, az Azure AD szükséges összetevői
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096351"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Azure Active Directory Connect egyéni telepítése
Ha további beállításokra van szüksége a telepítéshez, használja a Azure Active Directory (Azure AD)-csatlakozás *egyéni beállításait* . Akkor használja ezeket a beállításokat, ha például több erdővel rendelkezik, vagy ha választható szolgáltatásokat szeretne konfigurálni. Egyéni beállításokat használhat minden olyan esetben, amikor az [expressz telepítés](how-to-connect-install-express.md) nem felel meg az üzembe helyezési vagy a topológiai igényeknek.

Előfeltételek:
- [Azure ad Connect letöltése](https://go.microsoft.com/fwlink/?LinkId=615771).
- Hajtsa végre a [Azure ad Connect: hardver és előfeltételek](how-to-connect-install-prerequisites.md)című témakör előfeltételeinek lépéseit. 
- Győződjön meg arról, hogy rendelkezik [Azure ad Connect fiókokban és engedélyekben](reference-connect-accounts-permissions.md)leírt fiókokkal.

## <a name="custom-installation-settings"></a>Egyéni telepítési beállítások 

Ha egyéni telepítést kíván beállítani a Azure AD Connecthoz, ugorjon végig a varázsló azon oldalain, amelyeket a következő szakaszok ismertetnek.

### <a name="express-settings"></a>Gyorsbeállítások
Az **expressz beállítások** lapon válassza a **Testreszabás** lehetőséget a testreszabott beállítások telepítésének elindításához.  A cikk további részében végigvezeti az egyéni telepítési folyamaton. Az alábbi hivatkozásokra kattintva gyorsan elérheti egy adott oldal információit:

- [Szükséges összetevők](#install-required-components)
- [Felhasználói bejelentkezés](#user-sign-in)
- [Kapcsolódás az Azure AD-hez](#connect-to-azure-ad)
- [Szinkronizálás](#sync-pages)

### <a name="install-required-components"></a>Szükséges összetevők telepítése
A szinkronizálási szolgáltatások telepítésekor a választható konfigurációs szakasz nem jelölhető ki. A Azure AD Connect mindent automatikusan beállít. Beállítja a SQL Server 2012 Express LocalDB-példányt, létrehozza a megfelelő csoportokat, és hozzárendeli az engedélyeket. Ha módosítani szeretné az alapértelmezett beállításokat, törölje a megfelelő mezőket.  A következő táblázat összefoglalja ezeket a beállításokat, és hivatkozásokat tartalmaz a további információkhoz. 

![A Azure AD Connect szükséges telepítési összetevők választható kiválasztásait bemutató képernyőkép.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Választható konfiguráció | Leírás |
| --- | --- |
|Egyéni telepítési hely meghatározása| Lehetővé teszi a Azure AD Connect alapértelmezett telepítési útvonalának módosítását.|
| Meglévő SQL Server használata |Itt adhatja meg a SQL Server nevét és a példány nevét. Akkor válassza ezt a lehetőséget, ha már rendelkezik egy használni kívánt adatbázis-kiszolgálóval. A **példánynév** mezőben adja meg a példány nevét, a vesszőt és a portszámot, ha a SQL Server-példányon nincs engedélyezve a böngészés.  Ezután adja meg a Azure AD Connect adatbázis nevét.  Az SQL-jogosultságok határozzák meg, hogy létre lehet-e hozni új adatbázist, vagy az SQL-rendszergazdának előre kell létrehoznia az adatbázist.  Ha SQL Server rendszergazdai (SA) engedélyekkel rendelkezik, tekintse meg a [Azure ad Connect telepítése meglévő adatbázis használatával](how-to-connect-install-existing-database.md)című témakört.  Ha delegált engedélyekkel (DBO) rendelkezik, tekintse meg [a Azure ad Connect telepítése SQL meghatalmazott rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md)című témakört. |
| Meglévő szolgáltatásfiók használata |Alapértelmezés szerint a Azure AD Connect virtuális szolgáltatásfiókot biztosít a szinkronizációs szolgáltatásokhoz. Ha SQL Server távoli példányát használja, vagy egy hitelesítést igénylő proxyt használ, akkor a tartományban *felügyelt szolgáltatásfiók* vagy jelszóval védett szolgáltatásfiók használható. Ezekben az esetekben adja meg a használni kívánt fiókot. A telepítés futtatásához SA-nak kell lennie az SQL-ben, hogy bejelentkezési hitelesítő adatokat lehessen létrehozni a szolgáltatásfiók számára. További információ: [Azure ad Connect-fiókok és-engedélyek](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>A legújabb Build használatával az SQL-rendszergazda kiépítheti a sávon kívüli adatbázist. Ezután a Azure AD Connect rendszergazda telepítheti az adatbázis-tulajdonosi jogosultságokkal.  További információkért lásd: [Azure ad Connect telepítése SQL meghatalmazott rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md).|
| Egyéni szinkronizálási csoportok megadása |Alapértelmezés szerint a szinkronizálási szolgáltatások telepítésekor a Azure AD Connect négy, a kiszolgálón helyi csoportot hoz létre. Ezek a csoportok rendszergazdák, operátorok, Tallózás és jelszó-visszaállítás. Itt megadhatja a saját csoportjait. A csoportoknak a kiszolgálón helyinek kell lenniük. Nem a tartományban találhatók. |
|Szinkronizálási beállítások importálása (előzetes verzió)|Lehetővé teszi a beállítások importálását Azure AD Connect egyéb verzióiból.  További információ: [Azure ad Connect konfigurációs beállításainak importálása és exportálása](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Felhasználói bejelentkezés
A szükséges összetevők telepítése után válassza ki a felhasználók egyszeri bejelentkezési módszerét. Az alábbi táblázat röviden ismerteti az elérhető lehetőségeket. A bejelentkezési módok teljes leírásáért lásd: [Felhasználói bejelentkezés](plan-connect-user-signin.md).

![A "felhasználói bejelentkezés" oldalt megjelenítő képernyőkép. A "jelszó-kivonat szinkronizálása" beállítás be van jelölve.](./media/how-to-connect-install-custom/usersignin4.png)

| Egyszeri bejelentkezés lehetősége | Leírás |
| --- | --- |
| Jelszókivonat szinkronizálása |A felhasználók a helyszíni hálózaton használt jelszóval jelentkezhetnek be a Microsoft Cloud servicesbe, például a Microsoft 365ba. A felhasználói jelszavak szinkronizálása az Azure AD-vel jelszó-kivonattal történik. A hitelesítés a felhőben történik. További információ: jelszó- [kivonat szinkronizálása](how-to-connect-password-hash-synchronization.md). |
|Átmenő hitelesítés|A felhasználók a helyszíni hálózaton használt jelszóval jelentkezhetnek be a Microsoft Cloud servicesbe, például a Microsoft 365ba.  A felhasználói jelszavakat a rendszer a helyszíni Active Directory tartományvezérlőre való átadással ellenőrzi.
| Összevonás az AD FS rendszerrel |A felhasználók a helyszíni hálózaton használt jelszóval jelentkezhetnek be a Microsoft Cloud servicesbe, például a Microsoft 365ba.  A rendszer átirányítja a felhasználókat a helyszíni Azure Directory összevonási szolgáltatások (AD FS) példányára a bejelentkezéshez. A hitelesítés a helyszínen történik. |
| Összevonás a PingFederate-tel|A felhasználók a helyszíni hálózaton használt jelszóval jelentkezhetnek be a Microsoft Cloud servicesbe, például a Microsoft 365ba.  A felhasználókat a rendszer átirányítja a helyszíni PingFederate-példányra a bejelentkezéshez. A hitelesítés a helyszínen történik. |
| Nincs konfigurálás |Nincs telepítve vagy konfigurálva a felhasználói bejelentkezési funkció. Akkor válassza ezt a lehetőséget, ha már rendelkezik harmadik féltől származó összevonási kiszolgálóval vagy más megoldással. |
|Egyszeri bejelentkezés engedélyezése|Ez a beállítás a jelszó-kivonatoló szinkronizálással és az átmenő hitelesítéssel is elérhető. Egyszeri bejelentkezési élményt nyújt az asztali felhasználók számára a vállalati hálózatokon. További információ: [egyszeri bejelentkezés](how-to-connect-sso.md). </br></br>**Megjegyzés:** AD FS ügyfelek esetén ez a lehetőség nem érhető el. AD FS már azonos szintű egyszeri bejelentkezést biztosít.</br>

### <a name="connect-to-azure-ad"></a>Csatlakozás az Azure AD szolgáltatáshoz
A **Kapcsolódás az Azure ad-hoz** lapon adja meg a globális rendszergazdai fiókot és jelszót. Ha az előző lapon a **AD FS összevonása** lehetőséget választotta, ne jelentkezzen be egy olyan fiókkal, amely az összevonás engedélyezésére tervezett tartományban van. 

Előfordulhat, hogy az Azure AD-bérlőhöz tartozó fiókot szeretne használni az alapértelmezett *onmicrosoft.com* -tartományban. Ezt a fiókot csak akkor használja a rendszer, ha létrehoz egy szolgáltatásfiókot az Azure AD-ben. A telepítés befejezése után a rendszer nem használja.
  
![A "kapcsolódás az Azure AD szolgáltatáshoz" oldalra mutató képernyőkép.](./media/how-to-connect-install-custom/connectaad.png)

Ha a globális rendszergazdai fiókjában engedélyezve van a többtényezős hitelesítés, akkor a bejelentkezési ablakban adja meg újra a jelszót, és el kell végeznie a többtényezős hitelesítési kihívást. A kihívás lehet ellenőrző kód vagy telefonhívás.  

![A "kapcsolódás az Azure AD szolgáltatáshoz" oldalra mutató képernyőkép. A többtényezős hitelesítés mező kéri a felhasználót egy kód megadására.](./media/how-to-connect-install-custom/connectaadmfa.png)

A globális rendszergazdai fiók is engedélyezheti a [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) szolgáltatást.

Ha hibát észlel, vagy problémákat tapasztal a kapcsolattal kapcsolatban, tekintse meg a [kapcsolódási problémák elhárítása](tshoot-connect-connectivity.md)című témakört.

## <a name="sync-pages"></a>Lapok szinkronizálása

A következő szakaszok a **szinkronizálás** szakaszban található lapokat ismertetik.

### <a name="connect-your-directories"></a>Csatlakoztassa a címtárakat
Active Directory Domain Services (Azure AD DS) szolgáltatáshoz való kapcsolódáshoz Azure AD Connect szükséges egy olyan fiók erdő-neve és hitelesítő adatai, amely rendelkezik megfelelő engedélyekkel.

![Képernyőfelvétel: "a címtárak összekapcsolása" oldal megjelenítése.](./media/how-to-connect-install-custom/connectdir01.png)

Miután beírja az erdő nevét, és kiválasztja a  **könyvtár hozzáadása** lehetőséget, megjelenik egy ablak. A következő táblázat ismerteti a beállításait.

| Beállítás | Leírás |
| --- | --- |
| Új fiók létrehozása | Hozza létre azt az Azure AD DS-fiókot, amelyhez Azure AD Connect csatlakoznia kell a Active Directory erdőhöz a címtár-szinkronizálás során. Ha ezt a beállítást választja, adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát.  Azure AD Connect a megadott vállalati rendszergazdai fiókot használja a szükséges Azure AD DS-fiók létrehozásához. A tartományt NetBIOS-formátumban vagy FQDN formátumban is megadhatja. Adja meg a *FABRIKAM\administrator* vagy a *FABRIKAM. com\administrator* értéket. |
| Meglévő fiók használata | Olyan meglévő Azure AD DS-fiókot adjon meg, amelyet Azure AD Connect a címtár-szinkronizálás során a Active Directory erdőhöz való csatlakozáshoz használhat. A tartományt NetBIOS-formátumban vagy FQDN formátumban is megadhatja. Adja meg a *FABRIKAM\syncuser* vagy a *FABRIKAM. com\syncuser* értéket. Ez a fiók lehet normál felhasználói fiók, mert csak az alapértelmezett olvasási engedélyekre van szükség. A forgatókönyvtől függően azonban további engedélyekre lehet szükség. További információ: [Azure ad Connect-fiókok és-engedélyek](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Képernyőfelvétel: a "címtár összekötése" és a D erdőszintű fiók ablaka, ahol új fiókot hozhat létre, vagy egy meglévő fiókot használhat.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> A build 1.4.18.0 nem használhat vállalati vagy tartományi rendszergazdai fiókot Azure AD DS Connector-fiókként. Ha a **meglévő fiók használata** lehetőséget választja, ha vállalati rendszergazdai fiókot vagy tartományi rendszergazdai fiókot próbál meg beírni, a következő hibaüzenet jelenik meg: "az ad-erdő fiókjához tartozó vállalati vagy tartományi rendszergazdai fiók használata nem engedélyezett. Azure AD Connect hozza létre a fiókot, vagy megadhatja a megfelelő engedélyekkel rendelkező szinkronizálási fiókot. "
>

### <a name="azure-ad-sign-in-configuration"></a>Azure AD-bejelentkezés konfigurálása
Az **Azure ad bejelentkezési konfiguráció** lapján tekintse át a helyszíni Azure AD DS egyszerű felhasználónév (UPN) tartományait. Ezek az UPN-tartományok ellenőrizve lettek az Azure AD-ben. Ezen a lapon konfigurálhatja a userPrincipalName használandó attribútumot.

![Képernyőfelvétel: nem ellenőrzött tartományok az "Azure A D bejelentkezési konfiguráció" lapján.](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Tekintse át a **nem hozzáadott** vagy **nem ellenőrzött** összes tartományt. Győződjön meg arról, hogy az Ön által használt tartományok ellenőrizve lettek az Azure AD-ben. A tartományok ellenőrzése után válassza a körkörös frissítés ikont. További információ: [a tartomány hozzáadása és ellenőrzése](../fundamentals/add-custom-domain.md).

A felhasználók a *userPrincipalName* attribútumot használják, amikor bejelentkeznek az Azure ad-ba és Microsoft 365ba. Az Azure AD-nek a felhasználók szinkronizálása előtt ellenőriznie kell a tartományokat, más néven UPN-utótagot. A Microsoft azt javasolja, hogy tartsa meg az alapértelmezett attribútumot a userPrincipalName. 

Ha a userPrincipalName attribútum nem irányítható, és nem ellenőrizhető, akkor kijelölhet egy másik attribútumot is. Például a bejelentkezési azonosítót tartalmazó attribútumként válassza az e-mail lehetőséget. Ha a userPrincipalName-től eltérő attribútumot használ, az más néven egy *másik azonosító*. 

A másodlagos azonosító attribútum értékének az RFC 822 szabványt kell követnie. Alternatív azonosítót is használhat jelszó-kivonatoló szinkronizálással, átmenő hitelesítéssel és összevonással. Active Directoryban az attribútum nem definiálható többértékű, még akkor is, ha csak egyetlen értékkel rendelkezik. A másodlagos AZONOSÍTÓval kapcsolatos további információkért lásd [: átmenő hitelesítés: gyakori kérdések](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> Az átmenő hitelesítés engedélyezésekor legalább egy ellenőrzött tartománnyal rendelkeznie kell az egyéni telepítési folyamat folytatásához.

> [!WARNING]
> A helyettesítő azonosítók nem kompatibilisek az összes Microsoft 365 munkaterheléssel. További információ: [alternatív bejelentkezési azonosítók konfigurálása](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Tartományok és szervezeti egységek szűrése
Alapértelmezés szerint minden tartomány és szervezeti egység (OU) szinkronizálva van. Ha nem szeretne egyes tartományokat vagy szervezeti egységeket szinkronizálni az Azure AD-vel, törölheti a megfelelő beállításokat.  

![A tartomány és az O U szűrési oldalát bemutató képernyőkép.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Ezen a lapon a tartományalapú és szervezeti egység szerinti szűrés konfigurálható. Ha módosítani kívánja a módosításokat, tekintse meg a [tartományalapú szűrés](how-to-connect-sync-configure-filtering.md#domain-based-filtering) és a [szervezeti egység szerinti szűrés](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)című témakört. Bizonyos szervezeti egységek elengedhetetlenek a működéshez, ezért a kijelölést kell hagyni.

Ha OU-alapú szűrést használ a 1.1.524.0-nál régebbi Azure AD Connect verziónál, a rendszer alapértelmezés szerint szinkronizálja az új szervezeti egységeket. Ha nem szeretné, hogy a rendszer szinkronizálja az új szervezeti egységeket, az alapértelmezett viselkedést a [szervezeti egység szerinti szűrés](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) lépés után módosíthatja. Azure AD Connect 1.1.524.0 vagy újabb rendszer esetén megadhatja, hogy szeretné-e szinkronizálni az új szervezeti egységeket.

Ha [csoportos szűrést](#sync-filtering-based-on-groups)szeretne használni, győződjön meg róla, hogy a csoportot a csoport tartalmazza, és nem szűri a szervezeti egység szerinti szűrés használatával. A rendszer kiértékeli a szervezeti egység szűrését a csoport alapú szűrés kiértékelése előtt.

Az is lehetséges, hogy egyes tartományok nem érhetők el a tűzfal korlátozásai miatt. A rendszer alapértelmezés szerint kijelöli a tartományokat, és figyelmeztetést jelenít meg.  

![A nem elérhető tartományokat bemutató képernyőkép.](./media/how-to-connect-install-custom/unreachable.png)  

Ha ezt a figyelmeztetést látja, győződjön meg arról, hogy ezek a tartományok valóban nem érhetők el, és a figyelmeztetés várható.

### <a name="uniquely-identifying-your-users"></a>Felhasználók egyedi azonosítása

A **felhasználók azonosítása** lapon válassza ki, hogyan szeretné azonosítani a felhasználókat a helyszíni címtárakban, és hogyan azonosíthatja őket a sourceAnchor attribútum használatával.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Válassza ki, hogyan történjen a felhasználók azonosítása a helyszíni címtárakban
Az *erdők közötti egyezés* használatával megadhatja, hogy az Azure AD DS-erdők felhasználói hogyan legyenek megjelenítve az Azure ad-ben. Előfordulhat, hogy egy felhasználó csak egyszer jelenik meg az összes erdőben, vagy az engedélyezett és a letiltott fiókok kombinációjával rendelkezik. A felhasználók egyes erdőkben esetleg kapcsolattartóként is szerepelhetnek.

![Képernyőfelvétel: az oldal, ahol egyedi módon azonosíthatja a felhasználókat.](./media/how-to-connect-install-custom/unique2.png)

| Beállítás | Leírás |
| --- | --- |
| [A felhasználók csak egyszer jelennek meg az összes erdőben](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Minden felhasználó külön objektumként van létrehozva az Azure AD szolgáltatásban. Az objektumok nincsenek csatlakoztatva a metaverse-ban. |
| [Mail attribute (Mail attribútum)](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ez a beállítás összekapcsolja a felhasználókat és a kapcsolattartókat, amennyiben a levél attribútum ugyanazzal az értékkel rendelkezik különböző felhőkben. Akkor használja ezt a beállítást, ha a névjegyek a GALSync használatával lettek létrehozva. Ha ezt a beállítást választja, akkor a felhasználói objektumok, amelyek levelezési attribútuma nincs feltöltve, nincs szinkronizálva az Azure AD-vel. |
| [ObjectSID és msExchangeMasterAccountSID/msRTCSIP-OriginatorSID attribútumok](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ez a beállítás összeköt egy fiókerdőben található engedélyezett felhasználót egy erőforráserdőben található letiltott felhasználóval. Az Exchange ezt a konfigurációt csatolt postaládaként ismeri. Ezt a lehetőséget akkor használhatja, ha csak a Lync szolgáltatást használja, és ha az Exchange nem szerepel az erőforrás-erdőben. |
| SAMAccountName és MailNickName attribútumai |Ez a beállítás olyan attribútumokhoz csatlakozik, amelyekben a felhasználó bejelentkezési azonosítója várható. |
| Adott attribútum kiválasztása |Ez a beállítás lehetővé teszi, hogy kiválassza a saját attribútumát. Ha ezt a beállítást választja, a rendszer nem szinkronizálja az Azure AD-vel az olyan felhasználói objektumokat, amelyek (kijelölt) attribútuma nincs feltöltve. **Korlátozás:** Ehhez a beállításhoz csak a metaverse-ben már szereplő attribútumok érhetők el. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Válassza ki, hogyan azonosíthatók a felhasználók a forrás-horgony használatával
A *sourceAnchor* attribútum nem módosítható a felhasználói objektumok élettartama során. Ez az elsődleges kulcs, amely összekapcsolja a helyszíni felhasználót a felhasználóval az Azure AD-ben.

| Beállítás | Leírás |
| --- | --- |
| A forrás-horgony kezelése az Azure-ban | Válassza ezt a lehetőséget, ha azt szeretné, hogy az Azure AD válassza ki az attribútumot. Ha ezt a beállítást választja, Azure AD Connect alkalmazza a sourceAnchor attribútum kiválasztási logikáját, amely az [MS-DS-ConsistencyGuid SourceAnchor használatával](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)van leírva. Az egyéni telepítés befejeződése után megtekintheti, hogy melyik attribútumot választotta ki a sourceAnchor attribútumként. |
| Adott attribútum kiválasztása | Válassza ezt a lehetőséget, ha meg szeretné adni egy meglévő AD-attribútumot a sourceAnchor attribútumként. |

Mivel a sourceAnchor attribútum nem módosítható, ki kell választania egy megfelelő attribútumot. Egy jó jelölt az objectGUID. Ez az attribútum nem változik, kivéve, ha a felhasználói fiókot erdő vagy tartomány között helyezi át. Ne válasszon olyan attribútumokat, amelyek változhatnak, amikor egy személy házas vagy módosította a hozzárendeléseket. 

Nem használhat olyan attribútumot, amely tartalmaz egy jelet (@), így nem használhatja az e-maileket és a userPrincipalName. Az attribútum megkülönbözteti a kis-és nagybetűket is, így ha egy objektumot az erdők között helyez át, ügyeljen arra, hogy a kis-és nagybetűket megőrizze A bináris attribútumok Base64 kódolású, de más típusú attribútumok a kódolatlan állapotban maradnak. 

Az összevonási forgatókönyvek és néhány Azure AD-felület esetében a sourceAnchor attribútum más néven *immutableID*. 

További információ a forrás-horgonyról: [tervezési fogalmak](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Szinkronizálás szűrése csoportok alapján
A szűrési csoportok funkció lehetővé teszi, hogy csak az objektumok egy kis részhalmazát szinkronizálja egy próba számára. A szolgáltatás használatához hozzon létre egy csoportot a Active Directory helyi példányában. Ezután adja hozzá a felhasználókat és csoportokat, amelyeket közvetlen tagokként kíván szinkronizálni az Azure AD szolgáltatásba. Később felhasználókat adhat hozzá, vagy eltávolíthat felhasználókat ebből a csoportból az Azure AD-ben található objektumok listájának fenntartásához. 

A szinkronizálni kívánt összes objektumnak a csoport közvetlen tagjának kell lennie. A felhasználóknak, csoportoknak, névjegyeknek, illetve számítógépeknek vagy eszközöknek mind közvetlen tagoknak kell lenniük. A beágyazott csoporttagság nincs feloldva. Amikor tagként vesz fel egy csoportot, csak maga a csoport lesz hozzáadva. A tagjait nem adja hozzá a rendszer.

![Képernyőkép az oldalról, ahol kiválaszthatja, hogyan szűrheti a felhasználókat és az eszközöket.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Ez a funkció csak a próbaüzem központi telepítését támogatja. Ne használja teljes üzemi környezetben.
>

A teljes éles üzembe helyezés során nehéz lenne fenntartani egyetlen csoportot és annak összes objektumát a szinkronizáláshoz. A szűrési csoportok funkció helyett használja a [szűrés konfigurálása](how-to-connect-sync-configure-filtering.md)című témakörben leírt módszerek egyikét.

### <a name="optional-features"></a>Választható szolgáltatások
A következő oldalon kiválaszthatja a forgatókönyvhöz választható szolgáltatásokat.

>[!WARNING]
>Azure AD Connect 1.0.8641.0 és korábbi verziók az Azure Access Control Service a jelszó-visszaírási.  A szolgáltatás 2018. november 7-én megszűnt.  Ha a Azure AD Connect ezen verzióinak valamelyikét használja, és engedélyezte a jelszó visszaírási, a felhasználók elveszítik a jelszavukat a szolgáltatás kivonásakor. A Azure AD Connect ezen verziói nem támogatják a jelszó-visszaírási.
>
>További információ: [áttelepítés az Azure Access Control Serviceról](../azuread-dev/active-directory-acs-migration.md).
>
>Ha a jelszó visszaírási szeretné használni, töltse le a [Azure ad Connect legújabb verzióját](https://www.microsoft.com/download/details.aspx?id=47594).

![Képernyőfelvétel: a "választható funkciók" oldal.](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Ha Azure AD-szinkronizáló vagy közvetlen szinkronizálás (az rSync) aktív, ne aktiválja a Azure AD Connect visszaírási funkcióit.



| Választható szolgáltatások | Leírás |
| --- | --- |
| Hibrid Exchange-telepítés |Az Exchange hibrid üzembe helyezési funkciója lehetővé teszi az Exchange-postaládák párhuzamos használatát a helyszínen és a Microsoft 365. Azure AD Connect szinkronizálja az Azure AD-beli [attribútumok](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) egy adott készletét a helyszíni címtárba. |
| Exchange-levelezés nyilvános mappái | Az Exchange-levelezés nyilvános mappák funkciója lehetővé teszi, hogy szinkronizálja a levelezésre képes nyilvános mappák objektumait az Active Directory helyszíni példányáról az Azure AD-be. |
| Azure AD app and attribute filtering (Azure AD alkalmazás- és attribútumszűrés) |Az Azure AD-alkalmazás és az attribútumok szűrésének engedélyezésével testreszabhatja a szinkronizált attribútumok készletét. Ez a beállítás két további konfigurációs oldallal bővíti a varázslót. További információkért lásd: [Azure AD alkalmazás- és attribútumszűrés](#azure-ad-app-and-attribute-filtering). |
| Jelszókivonat szinkronizálása |Ha az összevonás lehetőséget választotta bejelentkezési megoldásként, akkor engedélyezheti a jelszó-kivonatolási szinkronizálást. Ezt követően használhatja biztonsági mentési lehetőségként.  </br></br>Ha az átmenő hitelesítést választotta, akkor engedélyezheti ezt a beállítást, így biztosíthatja a régebbi ügyfelek támogatását és a biztonsági mentést.</br></br> További információ: jelszó- [kivonat szinkronizálása](how-to-connect-password-hash-synchronization.md).|
| Jelszóvisszaíró |Ezzel a beállítással biztosíthatja, hogy az Azure AD-ből származó jelszó-változtatások vissza legyenek írva a helyszíni címtárba. További részletekért lásd: [A jelszókezelés első lépései](../authentication/tutorial-enable-sspr.md). |
| Group writeback (Csoportvisszaíró) |Ha Microsoft 365-csoportokat használ, akkor az Active Directory helyszíni példányában is megjelenítheti a csoportokat. Ez a beállítás csak akkor érhető el, ha a Active Directory helyszíni példányán van Exchange. További információ: [Azure ad Connect csoport visszaírási](how-to-connect-group-writeback.md).|
| Eszközvisszaíró |Feltételes hozzáférési forgatókönyvek esetén ezzel a beállítással visszaírhatja az Azure AD-beli objektumokat az Active Directory helyszíni példányára. További információkért lásd: [Eszközvisszaírás engedélyezése az Azure AD Connectben](how-to-connect-device-writeback.md). |
| Directory extension attribute sync (Címtárbővítmény-attribútumok szinkronizálása) |Ezzel a beállítással szinkronizálhatja a megadott attribútumokat az Azure AD-be. További információkért lásd: [Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD app and attribute filtering (Azure AD alkalmazás- és attribútumszűrés)
Ha szeretné korlátozni, hogy mely attribútumok legyenek szinkronizálva az Azure AD-be, először válassza ki a használni kívánt szolgáltatásokat. Ha megváltoztatja ezen a lapon a beállításokat, explicit módon ki kell választania egy új szolgáltatást a telepítővarázsló újbóli futtatásával.

![Az opcionális Azure A D alkalmazások funkcióit megjelenítő képernyőkép.](./media/how-to-connect-install-custom/azureadapps2.png)

Az előző lépésben kiválasztott szolgáltatások alapján ezen a lapon az összes szinkronizált attribútum látható. Ez a lista az összes szinkronizált objektumtípus kombinációja. Ha bizonyos attribútumokra van szüksége, hogy ne legyen szinkronizálva, törölje a kijelölést ezekből az attribútumokból.

![Az opcionális Azure A D attribútumok funkcióit megjelenítő képernyőkép.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Az attribútumok eltávolítása hatással lehet a funkcióra. Ajánlott eljárások és javaslatok: [szinkronizálandó attribútumok](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Címtárbővítmény-attribútumok szinkronizálása
A sémát kiterjesztheti az Azure AD-ben a szervezet által hozzáadott egyéni attribútumok, illetve a Active Directory egyéb attribútumainak használatával. A szolgáltatás használatához a **választható szolgáltatások** lapon válassza a **címtár-kiterjesztés attribútumának szinkronizálása** lehetőséget. A **Címtárszolgáltatások** lapon a szinkronizálandó attribútumok közül választhat.

>[!NOTE]
>Az **elérhető attribútumok** mező megkülönbözteti a kis-és nagybetűket.

![A "Directory-bővítmények" oldal képernyőképe.](./media/how-to-connect-install-custom/extension2.png)

További információkért lásd: [Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése
Az **egyszeri bejelentkezés** lapon a jelszó-szinkronizálással vagy átmenő hitelesítéssel használhatja az egyszeri bejelentkezést. Ezt a lépést egyszer kell végrehajtania az Azure AD-vel szinkronizált összes erdőhöz. A konfiguráció két lépésből áll:

1.  Hozza létre a szükséges számítógépfiókot a Active Directory helyi példányában.
2.  Konfigurálja az ügyfélszámítógépek intranetes zónáját az egyszeri bejelentkezés támogatásához.

#### <a name="create-the-computer-account-in-active-directory"></a>A számítógépfiók létrehozása a helyszíni Active Directoryban
A Azure AD Connectban hozzáadott összes erdőhöz tartományi rendszergazdai hitelesítő adatokat kell megadnia, hogy a számítógépfiók az egyes erdőkben is létrehozható legyen. A rendszer csak a fiók létrehozásához használja a hitelesítő adatokat. Nincsenek tárolva és nem használhatók semmilyen más művelethez. Adja hozzá a hitelesítő adatokat az **egyszeri bejelentkezés engedélyezése** lapon, az alábbi képen látható módon.

![Képernyőfelvétel: az "egyszeri bejelentkezés engedélyezése" oldal. A rendszer hozzáadja az erdő hitelesítő adatait.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Kihagyhatja az olyan erdőket, amelyeken nem kívánja használni az egyszeri bejelentkezést.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Az intranet zóna konfigurálása az ügyfélszámítógépekhez
Annak biztosítása érdekében, hogy az ügyfél automatikusan bejelentkezik az intranet zónába, győződjön meg arról, hogy az URL-cím az intranet zóna része. Ez a lépés biztosítja, hogy a tartományhoz csatlakoztatott számítógép automatikusan küldjön Kerberos-jegyet az Azure AD-nek, amikor a vállalati hálózathoz csatlakozik.

Csoportházirend felügyeleti eszközöket tartalmazó számítógépen:

1.  Nyissa meg a Csoportházirend felügyeleti eszközöket.
2.  Szerkessze a csoportházirendet, amelyet az összes felhasználóra alkalmazni fog. Például az alapértelmezett tartományi házirend.
3.  Lépjen a **felhasználói konfiguráció**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Internet Explorer**  >  **Internet Vezérlőpult**  >  **biztonsági oldalára**. Ezután válassza **a hely – zóna hozzárendelési lista** lehetőséget.
4.  Engedélyezze a szabályzatot. Ezután a párbeszédpanelen adja meg a érték nevét `https://autologon.microsoftazuread-sso.com` és értékét `1` . A telepítőnek a következő képhez hasonlóan kell kinéznie.
  
    ![Az intranetes zónákat bemutató képernyőkép.](./media/how-to-connect-install-custom/sitezone.png)

6.  Kattintson kétszer **az OK gombra** .

## <a name="configuring-federation-with-ad-fs"></a>AD FS-összevonás konfigurálása
Néhány kattintással beállíthatja, hogy a Azure AD Connect AD FS. A Kezdés előtt a következőkre lesz szüksége:

* Windows Server 2012 R2 vagy újabb verzió az összevonási kiszolgálóhoz. A távoli felügyeletet engedélyezni kell.
* A webalkalmazás-proxy kiszolgáló Windows Server 2012 R2 vagy újabb verziója. A távoli felügyeletet engedélyezni kell.
* TLS/SSL-tanúsítvány a használni kívánt összevonási szolgáltatás neveként (például sts.contoso.com).

>[!NOTE]
>A AD FS Farm TLS/SSL-tanúsítványát Azure AD Connect akkor is frissítheti, ha nem használja az összevonási megbízhatóságának kezelésére.

### <a name="ad-fs-configuration-prerequisites"></a>AD FS konfigurálásának előfeltételei
A AD FS Farm Azure AD Connect használatával történő konfigurálásához győződjön meg arról, hogy a WinRM engedélyezve van a távoli kiszolgálókon. Győződjön meg arról, hogy végrehajtotta az [összevonási előfeltételek](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)további feladatait. Ügyeljen arra is, hogy kövesse az [Azure ad Connect és az összevonási/WAP-kiszolgálók](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) táblában felsorolt portok követelményeit.

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Új AD FS farm létrehozása vagy meglévő AD FS farm használata
Meglévő AD FS-farmot is használhat, vagy létrehozhat egy újat. Ha úgy dönt, hogy újat hoz létre, meg kell adnia a TLS/SSL-tanúsítványt. Ha a TLS/SSL-tanúsítványt jelszó védi, a rendszer kéri, hogy adja meg a jelszót.

![A "A D F S Farm" oldalát ábrázoló képernyőkép](./media/how-to-connect-install-custom/adfs1.png)

Ha meglévő AD FS Farm használatát választja, akkor megjelenik az a lap, amelyen konfigurálhatja a AD FS és az Azure AD közötti megbízhatósági kapcsolatot.

>[!NOTE]
>A Azure AD Connect használatával csak egy AD FS Farm kezelhető. Ha van egy meglévő összevonási megbízhatósági kapcsolat, ahol az Azure AD konfigurálva van a kiválasztott AD FS farmon, Azure AD Connect újból létrehozza a bizalmi kapcsolatot.

### <a name="specify-the-ad-fs-servers"></a>Az AD FS kiszolgálók megadása
Válassza ki azokat a kiszolgálókat, amelyeken telepíteni szeretné a AD FS. A kapacitás igényeitől függően egy vagy több kiszolgálót is hozzáadhat. Mielőtt beállítja ezt a konfigurációt, csatlakoztassa az összes AD FS-kiszolgálót a Active Directoryhoz. Ez a lépés nem szükséges a webalkalmazás-proxy kiszolgálókhoz. 

A Microsoft a teszt- és próbatelepítésekhez egyetlen AD FS-kiszolgáló üzembe helyezését javasolja. A kezdeti konfigurálást követően további kiszolgálókat adhat hozzá és telepíthet úgy, hogy a méretezési igények kielégítéséhez Azure AD Connect újra futtassa.

> [!NOTE]
> Mielőtt beállítja ezt a konfigurációt, győződjön meg arról, hogy az összes kiszolgáló csatlakoztatva van egy Azure AD-tartományhoz.
>


![Az "összevonási kiszolgálók" lapot ábrázoló képernyőkép.](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>A webalkalmazás-proxy kiszolgálók megadása
Adja meg a webalkalmazás-proxy kiszolgálókat. A webalkalmazás-proxykiszolgáló üzembe helyezése a peremhálózati hálózaton történik, az extraneten. Támogatja az extranetes hitelesítési kéréseket. A kapacitás igényeitől függően egy vagy több kiszolgálót is hozzáadhat. 

A Microsoft egyetlen webalkalmazás-proxykiszolgáló telepítését javasolja tesztelési és kísérleti környezetekhez. A kezdeti konfigurálást követően további kiszolgálókat adhat hozzá és telepíthet úgy, hogy a méretezési igények kielégítéséhez Azure AD Connect újra futtassa. Javasoljuk, hogy megfelelő számú proxykiszolgálót biztosítson az intranetes hitelesítés kielégítéséhez.

> [!NOTE]
> - Ha a használt fiók nem helyi rendszergazda a webalkalmazás-proxy kiszolgálókon, a rendszer rendszergazdai hitelesítő adatokat kér.
> - A webalkalmazás-proxy kiszolgálók megadását megelőzően ellenőrizze, hogy van-e HTTP/HTTPS-kapcsolat a Azure AD Connect-kiszolgáló és a webalkalmazás-proxy kiszolgáló között.
> - Győződjön meg arról, hogy HTTP/HTTPS-kapcsolat van a webalkalmazás-kiszolgáló és a AD FS-kiszolgáló között, hogy lehetővé váljon a hitelesítési kérelmek átfolyása.
>


![A webalkalmazás-proxy kiszolgálók lapot ábrázoló képernyőfelvétel.](./media/how-to-connect-install-custom/adfs3.png)

A rendszer felszólítja a hitelesítő adatok megadására, hogy a webalkalmazás-kiszolgáló biztonságos kapcsolatot létesítsen a AD FS-kiszolgálóval. A hitelesítő adatoknak helyi rendszergazdai fiókkal kell rendelkezniük a AD FS kiszolgálón.

![Képernyőkép a "hitelesítő adatok" oldalról. A rendszergazdai hitelesítő adatok a username (Felhasználónév) és a Password (jelszó) mezőben vannak megadva.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Szolgáltatásfiók megadása az AD FS szolgáltatáshoz
A AD FS szolgáltatáshoz tartományi szolgáltatásfiók szükséges a felhasználók hitelesítéséhez és a felhasználói adatok kereséséhez a Active Directoryban. A szolgáltatásfiókok két típusát tudja támogatni:

* **Csoportosan felügyelt szolgáltatásfiók**: Ez a fióktípus a Windows Server 2012-es AD DS lett bevezetve. Ez a fióktípus olyan szolgáltatásokat nyújt, mint például a AD FS. Egyetlen fiók, amelyben nem kell rendszeresen frissítenie a jelszót. Ezt a lehetőséget akkor alkalmazza, ha Windows Server 2012 tartományvezérlőkkel rendelkezik a tartományban, amelyhez az AD FS-kiszolgálók tartoznak.
* **Tartományi felhasználói fiók**: az ilyen típusú fiókhoz meg kell adnia egy jelszót, és rendszeresen frissítenie kell a lejáratakor. Ezt a lehetőséget csak akkor használja, ha nem rendelkezik Windows Server 2012 tartományvezérlővel abban a tartományban, amelyhez az AD FS-kiszolgálók tartoznak.

Ha a **csoportosan felügyelt szolgáltatásfiók létrehozása** lehetőséget választotta, és ezt a szolgáltatást soha nem használták Active Directoryban, adja meg a vállalati rendszergazda hitelesítő adatait. A hitelesítő adatok a kulcstároló indításához és a szolgáltatás az Active Directoryban való engedélyezéséhez szükségesek.

> [!NOTE]
> Azure AD Connect ellenőrzi, hogy a AD FS szolgáltatás már regisztrálva van-e a tartomány egyszerű szolgáltatásnév (SPN) néven.  Az Azure AD DS nem engedélyezi, hogy az ismétlődő SPN-ket regisztrálják egyszerre.  Ha a rendszer duplikált SPN-t talál, addig nem folytathatja a folytatást, amíg el nem távolítja az egyszerű szolgáltatásnevet.

![Képernyőfelvétel: "A D F S szolgáltatás fiókja" oldal.](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Válassza ki a összevonása kívánt Azure AD-tartományt
A AD FS és az Azure AD közötti összevonási kapcsolat beállításához használja az **Azure ad-tartomány** lapot. Itt úgy konfigurálhatja a AD FSt, hogy biztonsági jogkivonatokat biztosítson az Azure AD-nek. Az Azure AD-t úgy is konfigurálhatja, hogy megbízzon a jogkivonatok ebből a AD FS-példányból. 

Ezen az oldalon csak egyetlen tartományt lehet konfigurálni a kezdeti telepítésben. További kiszolgálókat később, az Azure AD Connect ismételt futtatásával konfigurálhat.

![Képernyőkép, amely az "Azure A D tartomány" oldalt jeleníti meg.](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Az összevonáshoz kiválasztott Azure AD-tartomány ellenőrzése
Ha kijelöli a összevonása tartományt, a Azure AD Connect információt nyújt a nem ellenőrzött tartományok ellenőrzéséhez. További információ: [a tartomány hozzáadása és ellenőrzése](../fundamentals/add-custom-domain.md).

![Képernyőfelvétel: az "Azure A D tartomány" oldal, beleértve a tartomány ellenőrzéséhez használható információkat is.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect megpróbálja ellenőrizni a tartományt a konfigurációs fázisban. Ha nem adja hozzá a szükséges tartománynévrendszer-(DNS-) rekordokat, a konfiguráció nem hajtható végre.
>


## <a name="configuring-federation-with-pingfederate"></a>PingFederate-összevonás konfigurálása
A PingFederate-t néhány kattintással Azure AD Connect is konfigurálhatja. A következő előfeltételek szükségesek:
- PingFederate 8,4 vagy újabb.  További információ: [PingFederate-integráció Azure Active Directory és Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- TLS/SSL-tanúsítvány a használni kívánt összevonási szolgáltatás neveként (például sts.contoso.com).

### <a name="verify-the-domain"></a>A tartomány hitelesítése
Miután a PingFederate használatával beállította az összevonást, a rendszer arra kéri, hogy ellenőrizze a összevonása kívánt tartományt.  Válassza ki a tartományt a legördülő menüből.

![Képernyőkép, amely az "Azure A D tartomány" oldalt jeleníti meg. A példában a "contoso.com" tartomány van kiválasztva.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>A PingFederate-beállítások exportálása


Konfigurálja az PingFederate-t összevonási kiszolgálóként az egyes összevont Azure-tartományokhoz.  Válassza a **Beállítások exportálása** lehetőséget, hogy ezeket az információkat a PingFederate rendszergazdájával ossza meg.  Az összevonási kiszolgáló rendszergazdája frissíti a konfigurációt, majd megadja a PingFederate-kiszolgáló URL-címét és portszámát, így Azure AD Connect ellenőrizheti a metaadatok beállításait.  

![Képernyőfelvétel: a "PingFederate Settings" (beállítások megjelenítése) oldal. A lap tetején az "exportálási beállítások" gomb jelenik meg.](./media/how-to-connect-install-custom/ping2.png)

Az érvényesítéssel kapcsolatos problémák megoldásához forduljon a PingFederate-rendszergazdához.  Az alábbi képen egy olyan PingFederate-kiszolgálóval kapcsolatos információ látható, amely nem rendelkezik érvényes megbízhatósági kapcsolattal az Azure-ban.

![A kiszolgáló információit megjelenítő képernyőfelvétel: a PingFederate-kiszolgáló található, de az Azure szolgáltatói kapcsolatai hiányoznak vagy le vannak tiltva.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Az összevonási kapcsolat ellenőrzése
Azure AD Connect megkísérli érvényesíteni az előző lépésben a PingFederate metaadataiból lekért hitelesítési végpontokat.  Azure AD Connect először megkísérli a végpontok feloldását a helyi DNS-kiszolgálók használatával.  Ezután megkísérli feloldani a végpontokat egy külső DNS-szolgáltató használatával.  Az érvényesítéssel kapcsolatos problémák megoldásához forduljon a PingFederate-rendszergazdához.  

![Képernyőkép a "kapcsolat ellenőrzése" oldalról.](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Összevonási bejelentkezés ellenőrzése
Végezetül az újonnan konfigurált összevont bejelentkezési folyamat ellenőrzéséhez bejelentkezhet az összevont tartományba. Ha a bejelentkezés sikeres, akkor a PingFederate-val való összevonása sikeresen konfigurálva van.

![Képernyőkép az "összevont bejelentkezés ellenőrzése" oldalról. Az alján található üzenet sikeres bejelentkezést jelez.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Configure (Konfigurálás) és Verify (Ellenőrzés) lap
A konfigurálás a configure ( **Konfigurálás** ) lapon történik.

> [!NOTE]
> Ha az összevonást konfigurálta, akkor a telepítés folytatása előtt ellenőrizze, hogy az [összevonási kiszolgálók névfeloldását](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) is konfigurálta-e.
>



![Képernyőfelvétel: a "készen áll a konfigurálásra" oldal.](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Átmeneti üzemmód használata
Egy új szinkronizálási kiszolgáló állítható be párhuzamosan, átmeneti módban. Ha ezt a beállítást szeretné használni, akkor csak egy szinkronizálási kiszolgáló tud exportálni a felhő egyik könyvtárába. Ha azonban egy másik kiszolgálóról szeretne áttérni, például egy olyan kiszolgálóról, amelyen fut az rSync, akkor engedélyezheti a Azure AD Connect átmeneti módban. 

Az előkészítési beállítás engedélyezésekor a Szinkronizáló motor a szokásos módon importálja és szinkronizálja az adatokat. De nem exportálja az Azure AD-be vagy a Active Directoryba. Átmeneti módban a jelszó-szinkronizálás funkció és a jelszó visszaírási funkció le van tiltva.

![Képernyőfelvétel: az átmeneti üzemmód engedélyezése lehetőség.](./media/how-to-connect-install-custom/stagingmode.png)

Átmeneti módban elvégezheti a szükséges módosításokat a Szinkronizáló motoron, és áttekintheti az exportálandó tartalmat. Ha a konfiguráció megfelelőnek tűnik, futtassa le újra a telepítővarázslót, és tiltsa le az átmeneti módot. 

Az adatok mostantól az Azure AD-be lettek exportálva a-kiszolgálóról. Mindenképpen tiltsa le ezzel egy időben a másik kiszolgálót, hogy egyszerre csak egy kiszolgáló exportáljon aktívan.

További információkért lásd: [Átmeneti mód](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Összevonási konfiguráció ellenőrzése
Azure AD Connect ellenőrzi a DNS-beállításokat az **ellenőrzés** gomb kiválasztásakor. A következő beállításokat ellenőrzi:

* **Intranetes kapcsolat**
    * Összevonási FQDN feloldása: Azure AD Connect ellenőrzi, hogy a DNS fel tudja-e oldani az összevonási teljes tartománynevet a kapcsolat biztosításához. Ha Azure AD Connect nem tudja feloldani a teljes tartománynevet, akkor az ellenőrzés sikertelen lesz. Az ellenőrzés befejezéséhez győződjön meg arról, hogy a DNS-rekord megtalálható az összevonási szolgáltatás teljes TARTOMÁNYNEVÉnél.
    * A DNS-rekord: Azure AD Connect ellenőrzi, hogy az összevonási szolgáltatás rendelkezik-e rekorddal. Egy rekord hiányában az ellenőrzés sikertelen lesz. Az ellenőrzés befejezéséhez hozzon létre egy rekordot (nem CNAME rekordot) az összevonási FQDN számára.
* **Extranetes kapcsolat**
    * Összevonási FQDN feloldása: Azure AD Connect ellenőrzi, hogy a DNS fel tudja-e oldani az összevonási teljes tartománynevet a kapcsolat biztosításához.

      ![A "telepítés befejezése" lapot ábrázoló képernyőkép.](./media/how-to-connect-install-custom/completed.png)

      ![A "telepítés befejezése" lapot ábrázoló képernyőkép. Egy üzenet jelzi, hogy az intranetes konfiguráció ellenőrzése megtörtént.](./media/how-to-connect-install-custom/adfs7.png)

A végpontok közötti hitelesítés ellenőrzéséhez manuálisan végezze el az alábbi tesztek valamelyikét:

* A szinkronizálás befejeződése után Azure AD Connect az **összevont bejelentkezés ellenőrzése** további feladatot a kiválasztott helyszíni felhasználói fiók hitelesítésének ellenőrzéséhez.
* Az intraneten lévő, tartományhoz csatlakoztatott gépről ellenőrizze, hogy be tud-e jelentkezni egy böngészőből. Kapcsolódás a következőhöz: https://myapps.microsoft.com . Ezután használja a bejelentkezett fiókot a bejelentkezés ellenőrzéséhez. A beépített Azure AD DS rendszergazdai fiók nincs szinkronizálva, és nem használható az ellenőrzéshez.
* Győződjön meg arról, hogy be tud jelentkezni az extranetes eszközről. Egy otthoni gépen vagy mobileszközön kapcsolódjon a következőhöz: https://myapps.microsoft.com . Ezután adja meg a hitelesítő adatait.
* Ellenőrizze a gazdag ügyféllel való bejelentkezést. Kapcsolódás a következőhöz: https://testconnectivity.microsoft.com . Ezután válassza az **Office 365**  >  **Office 365 egyetlen Sign-On teszt** lehetőséget.

## <a name="troubleshoot"></a>Hibaelhárítás
Ez a szakasz olyan hibaelhárítási információkat tartalmaz, amelyek akkor használhatók, ha probléma van a Azure AD Connect telepítésekor.

Azure AD Connect telepítésének testreszabásakor a **szükséges összetevők telepítése** lapon válassza a **meglévő SQL Server használata** lehetőséget. A következő hibaüzenet jelenhet meg: "a ADSync-adatbázis már tartalmaz információt, ezért nem írható felül. Távolítsa el a meglévő adatbázist, és próbálkozzon újra. "

![A "szükséges összetevők telepítése" lapot megjelenítő képernyőkép. Hiba jelenik meg az oldal alján.](./media/how-to-connect-install-custom/error1.png)

Ez a hiba akkor jelenik meg, ha egy *AdSync* nevű adatbázis már létezik a megadott SQL Server SQL-példányán.

Ez a hiba általában a Azure AD Connect eltávolítása után jelenik meg.  Az adatbázis nem törlődik a SQL Servert futtató számítógépről a Azure AD Connect eltávolításakor.

A probléma megoldásához:

1. Keresse meg a ADSync-adatbázist, amelyet a rendszer a eltávolítása előtt Azure AD Connect használni. Győződjön meg arról, hogy az adatbázis már nincs használatban.

2. Az adatbázis biztonsági mentése.

3. Az adatbázis törlése:
    1. Az SQL-példányhoz való kapcsolódáshoz használjon **Microsoft SQL Server Management Studio** . 
    1. Keresse meg a **AdSync** -adatbázist, és kattintson rá a jobb gombbal.
    1. A helyi menüben válassza a **Törlés** lehetőséget.
    1. Az adatbázis törléséhez kattintson **az OK gombra** .

![Képernyőfelvétel: Microsoft SQL Server Management Studio. A D szinkronizálás ki van választva.](./media/how-to-connect-install-custom/error2.png)

A ADSync-adatbázis törlése után a telepítés megismétléséhez válassza a **telepítés** lehetőséget.

## <a name="next-steps"></a>Következő lépések
A telepítés befejezése után jelentkezzen ki a Windows rendszerből. Ezután jelentkezzen be újra a Synchronization Service Manager vagy a szinkronizációs szabály szerkesztőjének használata előtt.

Most, hogy telepítette Azure AD Connect, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).

További információ a telepítés során engedélyezett funkciókról: a véletlen törlés és a [Azure ad Connect Health](how-to-connect-health-sync.md) [megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) .

További információ az egyéb általános témakörökről: [Azure ad Connect Sync: Scheduler](how-to-connect-sync-feature-scheduler.md) és a helyszíni [identitások integrálása az Azure ad-vel](whatis-hybrid-identity.md).
