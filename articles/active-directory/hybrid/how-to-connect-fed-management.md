---
title: Azure AD Connect – AD FS felügyelet és testreszabás | Microsoft Docs
description: A Azure AD Connect és a PowerShell használatával AD FS a felügyeletet a felhasználói AD FS bejelentkezési felületének Azure AD Connectával és testreszabásával.
keywords: AD FS, ADFS, AD FS felügyelet, HRE-kapcsolat, csatlakozási, bejelentkezés, AD FS testreszabás, megbízhatóság javítása, M365, összevonás, függő entitás
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0c8c40e370579100c562e0289c97e3f5ce4236
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91274112"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Active Directory összevonási szolgáltatások (AD FS) kezelése és testreszabása Azure AD Connect használatával
Ez a cikk a Active Directory összevonási szolgáltatások (AD FS) (AD FS) Azure Active Directory (Azure AD) használatával történő kezelését és testreszabását ismerteti. Emellett olyan gyakori AD FS feladatokat is tartalmaz, amelyeket a AD FS farmok teljes konfigurálásához lehet szükség.

| Témakör | Mire terjed ki |
|:--- |:--- |
| **AD FS kezelése** | |
| [A megbízhatóság javítása](#repairthetrust) |Az összevonási megbízhatóság javítása a Microsoft 365 használatával. |
| [Összevonása az Azure AD-vel alternatív bejelentkezési azonosító használatával](#alternateid) | Összevonás konfigurálása alternatív bejelentkezési azonosító használatával  |
| [AD FS-kiszolgáló hozzáadása](#addadfsserver) |AD FS Farm kibontása további AD FS-kiszolgálóval. |
| [AD FS webalkalmazás-proxy kiszolgáló hozzáadása](#addwapserver) |AD FS Farm kibontása egy további webalkalmazás-proxy (WAP) kiszolgálóval. |
| [Összevont tartomány hozzáadása](#addfeddomain) |Összevont tartomány hozzáadása. |
| [A TLS/SSL-tanúsítvány frissítése](how-to-connect-fed-ssl-update.md)| Egy AD FS Farm TLS/SSL-tanúsítványának frissítése. |
| **AD FS testreszabása** | |
| [Egyéni vállalati embléma vagy illusztráció hozzáadása](#customlogo) |AD FS bejelentkezési oldal testreszabása vállalati emblémával és illusztrációval. |
| [Bejelentkezési Leírás hozzáadása](#addsignindescription) |Bejelentkezési oldal leírásának hozzáadása. |
| [AD FSi jogcím szabályainak módosítása](#modclaims) |AD FS jogcímek módosítása különböző összevonási forgatókönyvekhez. |

## <a name="manage-ad-fs"></a>AD FS kezelése
A Azure AD Connect varázslóval számos AD FS kapcsolódó feladatot is végrehajthat Azure AD Connect minimális felhasználói beavatkozással. Miután befejezte a Azure AD Connect telepítését a varázsló futtatásával, további feladatok elvégzéséhez futtassa újra a varázslót.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>A megbízhatóság javítása 
A Azure AD Connect segítségével megtekintheti a AD FS és az Azure AD megbízhatóságának aktuális állapotát, és megteheti a szükséges lépéseket a megbízhatóság kijavításához. Az Azure AD és a AD FS megbízhatóság javításához kövesse az alábbi lépéseket.

1. Válassza ki a **HRE és az ADFS-megbízhatóság javítása** elemet a további feladatok listájából.
   ![HRE-és ADFS-megbízhatóság javítása](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. A **Kapcsolódás az Azure ad-hoz** lapon adja meg az Azure ad globális rendszergazdai hitelesítő adatait, és kattintson a **tovább** gombra.
   ![Képernyőfelvétel: a "kapcsolódás az Azure AD szolgáltatáshoz" oldal, például a megadott hitelesítő adatokkal.](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. A **távelérési hitelesítő adatok** lapon adja meg a tartományi rendszergazda hitelesítő adatait.

   ![Képernyőkép, amely a "távelérési hitelesítő adatok" lapot mutatja, például a megadott hitelesítő adatokat.](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Miután rákattintott a **tovább** gombra, Azure ad Connect ellenőrzi a tanúsítvány állapotát, és megjeleníti a problémákat.

    ![Tanúsítványok állapota](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    A **konfigurálásra kész** lapon a megbízhatóság javításához végrehajtandó műveletek listája látható.

    ![Képernyőfelvétel: a "készen áll a konfigurálásra" oldal, amely a műveletek listáját tartalmazza.](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. A megbízhatóság kijavításához kattintson a **telepítés** gombra.

> [!NOTE]
> Azure AD Connect csak önaláírt tanúsítványokat tud kijavítani vagy fellépni. Azure AD Connect nem tudja kijavítani a külső gyártótól származó tanúsítványokat.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Összevonása az Azure AD-vel a AlternateID használatával 
Javasoljuk, hogy a helyszíni egyszerű felhasználónevet (UPN) és a Felhőbeli egyszerű felhasználónevet is megtartsa. Ha a helyszíni UPN nem útválasztón alapuló tartományt használ (pl. Contoso. local) vagy a helyi alkalmazás függőségei miatt nem módosítható, javasoljuk, hogy állítson be alternatív bejelentkezési azonosítót. Az alternatív bejelentkezési azonosító lehetővé teszi a bejelentkezési élmény konfigurálását, amelyben a felhasználók az egyszerű felhasználónévtől (például a levelezéstől) eltérő attribútummal jelentkezhetnek be. Az egyszerű felhasználónevek Azure AD Connect alapértelmezett értéke a Active Directory userPrincipalName attribútuma. Ha az egyszerű felhasználónévhez más attribútumot is választ, és a AD FS használatával egyesítő, Azure AD Connect konfigurálja AD FS a helyettesítő bejelentkezési AZONOSÍTÓhoz. Alább látható egy példa arra, hogy egy másik attribútumot válasszon a felhasználó egyszerű neveként:

![Alternatív azonosító attribútum kiválasztása](./media/how-to-connect-fed-management/attributeselection.png)

A AD FS alternatív bejelentkezési AZONOSÍTÓjának konfigurálása két fő lépésből áll:
1. **Adja meg a kiállítási jogcímek megfelelő készletét**: az Azure ad függő entitás megbízhatóságában lévő kiállítási jogcím szabályai úgy módosulnak, hogy a kiválasztott userPrincipalName attribútumot használják a felhasználó alternatív azonosítójával.
2. **Alternatív bejelentkezési azonosító engedélyezése a AD FS konfigurációban**: a rendszer frissíti a AD FS konfigurációt, hogy a AD FS a megfelelő erdőkben lévő felhasználókat a helyettesítő azonosító használatával keresse meg. Ezt a konfigurációt AD FS Windows Server 2012 R2 (KB2919355) vagy újabb rendszereken támogatja. Ha a AD FS-kiszolgálók 2012 R2, Azure AD Connect ellenőrzi a szükséges KB jelenlétét. Ha a KB-ot nem észleli a rendszer, a konfiguráció befejezése után egy figyelmeztetés jelenik meg, ahogy az alábbi ábrán is látható:

    ![Figyelmeztetés a 2012R2 hiányzó KB-ról](./media/how-to-connect-fed-management/kbwarning.png)

    Ha hiányzó TUDÁSBÁZIS esetén szeretné kijavítani a konfigurációt, telepítse a szükséges [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) , majd javítsa ki a megbízhatóságot a [javítási hre és a AD FS megbízhatóság](#repairthetrust)használatával.

> [!NOTE]
> A alternateID és a manuális konfigurálásával kapcsolatos további információkért olvassa el a [másik bejelentkezési azonosító konfigurálása](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) című témakört.

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>AD FS-kiszolgáló hozzáadása 

> [!NOTE]
> AD FS kiszolgáló hozzáadásához Azure AD Connect szükséges a PFX-tanúsítvány. Ezért ezt a műveletet csak akkor hajthatja végre, ha a AD FS farmot Azure AD Connect használatával konfigurálta.

1. Válassza **a további összevonási kiszolgáló telepítése** lehetőséget, majd kattintson a **tovább** gombra.

   ![További összevonási kiszolgáló](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. A **Kapcsolódás az Azure ad-hoz** lapon adja meg az Azure ad globális rendszergazdai hitelesítő adatait, és kattintson a **tovább** gombra.

   ![Képernyőkép, amely a "kapcsolódás az Azure AD-hoz" lapot mutatja a megadott hitelesítő adatokkal.](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Adja meg a tartományi rendszergazda hitelesítő adatait.

   ![Tartományi rendszergazdai hitelesítő adatok](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect kéri a PFX-fájl jelszavának megadását, miközben az új AD FS Farm konfigurálását Azure AD Connect. Kattintson a **jelszó megadása** gombra a pfx-fájl jelszavának megadásához.

   ![Képernyőfelvétel: az "SSL-tanúsítvány megadása" oldal megnyitása a "tanúsítvány jelszava" ablak megnyitásával.](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Képernyőfelvétel: "SSL-tanúsítvány megadása" oldal, miután megadta a PFX-fájlhoz tartozó jelszót.](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. A **AD FS kiszolgálók** lapon adja meg a AD FS farmhoz hozzáadandó kiszolgáló nevét vagy IP-címét.

   ![Kiszolgálók AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Kattintson a **tovább** gombra, és folytassa a **konfigurálást** a végső oldalon. Miután Azure AD Connect befejezte a kiszolgálók hozzáadását a AD FS farmhoz, lehetősége lesz ellenőrizni a kapcsolatot.

   ![Képernyőfelvétel: a "készen áll a konfigurálásra" oldal, amely a "telepítés" gombra kattintás után végrehajtandó műveletek listáját jeleníti meg.](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![A "telepítés befejezése" lapot tartalmazó képernyőkép, amelynek "az intranetes konfiguráció ellenőrzése sikeres volt" üzenet jelenik meg. ](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>AD FS WAP-kiszolgáló hozzáadása 

> [!NOTE]
> WAP-kiszolgáló hozzáadásához Azure AD Connect szükséges a PFX-tanúsítvány. Ezért ezt a műveletet csak akkor hajthatja végre, ha a AD FS farmot Azure AD Connect használatával konfigurálta.

1. Válassza a **webalkalmazás-proxy telepítése** lehetőséget az elérhető feladatok listájából.

   ![Webalkalmazás-proxy üzembe helyezése](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Adja meg az Azure globális rendszergazdai hitelesítő adatait.

   ![Képernyőfelvétel: a "kapcsolódás az Azure AD-hoz" oldal, egy példa felhasználónevével és jelszavával.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Az **SSL-tanúsítvány megadása** lapon adja meg a AD FS Farm Azure ad Connect használatával való konfigurálásakor megadott pfx-fájl jelszavát.
   ![Tanúsítvány jelszava](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![TLS/SSL-tanúsítvány meghatározása](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Adja hozzá a kiszolgálót a WAP-kiszolgálóként való hozzáadáshoz. Mivel előfordulhat, hogy a WAP-kiszolgáló nem csatlakozik a tartományhoz, a varázsló kéri a rendszergazdai hitelesítő adatok megadását a hozzáadott kiszolgáló számára.

   ![Felügyeleti kiszolgáló hitelesítő adatai](./media/how-to-connect-fed-management/WapServer5.PNG)

5. A **proxy megbízhatóságának hitelesítő adatai** lapon adja meg a rendszergazdai hitelesítő adatokat a proxy megbízhatóságának konfigurálásához és a AD FS Farm elsődleges kiszolgálójának eléréséhez.

   ![Proxy megbízhatóságának hitelesítő adatai](./media/how-to-connect-fed-management/WapServer6.PNG)

6. A **készen áll a konfigurálásra** lapon a varázsló megjeleníti a végrehajtandó műveletek listáját.

   ![Képernyőfelvétel: a "készen áll a konfigurálásra" oldal, amely a végrehajtandó műveletek listáját jeleníti meg.](./media/how-to-connect-fed-management/WapServer7.PNG)

7. A konfigurálás befejezéséhez kattintson a **telepítés** gombra. A konfiguráció befejezése után a varázsló lehetőséget ad a kiszolgálók kapcsolatának ellenőrzésére. A kapcsolat ellenőrzéséhez kattintson az **ellenőrzés** gombra.

   ![A telepítés befejeződött](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Összevont tartomány hozzáadása 

Egyszerűen hozzáadhat egy tartományt az Azure AD-vel összevont tartományhoz Azure AD Connect használatával. Azure AD Connect hozzáadja a tartományt az összevonáshoz, és módosítja a jogcím szabályait, hogy megfelelően tükrözze a kiállítót, ha több, az Azure AD-vel összevont tartománnyal rendelkezik.

1. Összevont tartomány hozzáadásához válassza a **további Azure ad-tartomány hozzáadása** feladatot.

   ![További Azure AD-tartomány](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. A varázsló következő lapján adja meg az Azure AD globális rendszergazdai hitelesítő adatait.

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. A **távelérési hitelesítő adatok** lapon adja meg a tartományi rendszergazdai hitelesítő adatokat.

   ![Távelérési hitelesítő adatok](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. A következő lapon a varázsló megjeleníti azon Azure AD-tartományok listáját, amelyekkel összevonása a helyszíni címtárat. Válassza ki a tartományt a listából.

   ![Azure AD-tartomány](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    A tartomány kiválasztása után a varázsló megfelelő információkat biztosít a varázsló által végrehajtandó további műveletekről és a konfiguráció hatásáról. Bizonyos esetekben, ha olyan tartományt választ ki, amely még nem lett ellenőrizve az Azure AD-ben, a varázsló információt nyújt a tartomány ellenőrzéséhez. További részletekért tekintse [meg az Egyéni tartománynév hozzáadása a Azure Active Directoryhoz](../fundamentals/add-custom-domain.md) című témakört.

5. Kattintson a **Tovább** gombra. A **konfigurálásra kész** lapon látható azoknak a műveleteknek a listája, amelyeket a Azure ad Connect végrehajt. A konfigurálás befejezéséhez kattintson a **telepítés** gombra.

   ![Ready to configure (Konfigurálásra kész)](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> A hozzáadott összevont tartományba tartozó felhasználókat szinkronizálni kell, mielőtt be tudnak jelentkezni az Azure AD-be.

## <a name="ad-fs-customization"></a>AD FS testreszabása
A következő szakaszokban részletesen ismertetjük azokat a gyakori feladatokat, amelyeket a AD FS bejelentkezési oldalának testreszabásakor végre kell hajtania.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Egyéni vállalati embléma vagy illusztráció hozzáadása 
A **bejelentkezési** lapon megjelenő cég emblémájának módosításához használja a következő Windows PowerShell-parancsmagot és szintaxist.

> [!NOTE]
> Az embléma ajánlott méretei 260 x 35 \@ 96 dpi, amely nem haladja meg a 10 KB-ot.

```azurepowershell-interactive
Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}
```

> [!NOTE]
> A *TargetName* paraméter megadása kötelező. Az AD FS nevű alapértelmezett téma a default (alapértelmezett).

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Bejelentkezési Leírás hozzáadása 
A **bejelentkezési oldal** leírásának hozzáadásához használja a következő Windows PowerShell-parancsmagot és szintaxist.

```azurepowershell-interactive
Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"
```

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>AD FSi jogcím szabályainak módosítása 
A AD FS támogatja az egyéni jogcím-szabályok létrehozásához használható, sokoldalú jogcímek nyelvét. További információ: a [jogcím-szabály nyelvének szerepe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807118(v=ws.11)).

Az alábbi szakaszok azt ismertetik, hogyan írhat egyéni szabályokat az Azure AD-hez és a AD FS-összevonáshoz kapcsolódó egyes forgatókönyvekhez.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Megváltoztathatatlan azonosító az attribútumban található értéktől függ.
Azure AD Connect lehetővé teszi, hogy olyan attribútumot határozzon meg, amelyet forrásként kell használni az objektumok Azure AD-vel való szinkronizálásakor. Ha az egyéni attribútum értéke nem üres, előfordulhat, hogy egy nem módosítható azonosító jogcímet szeretne kiállítani.

Például kiválaszthatja az **MS-DS-consistencyguid** attribútumot a forrás-horgonyhoz, és a **ImmutableID** -et **MS-DS-consistencyguid-** ként adja meg, ha az attribútumnak van egy értéke. Ha nem áll rendelkezésre érték az attribútumhoz, a **ObjectGUID** a nem változtatható azonosítóként adja ki. Az egyéni jogcím-szabályok készletét a következő szakaszban leírtak szerint hozhatja létre.

**1. szabály: lekérdezési attribútumok**

```claim-rule-language
c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
=> add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);
```

Ebben a szabályban az **MS-DS-consistencyguid** és a **ObjectGUID** értékeit kérdezi le a felhasználó számára a Active Directory. Módosítsa az áruház nevét egy megfelelő tároló nevére a AD FS üzemelő példányában. Módosítsa a jogcím típusát a **ObjectGUID** és az **MS-DS-consistencyguid** számára meghatározott megfelelő jogcím-típusra is.

Emellett a **Hozzáadás** és a nem **probléma** használatával nem adhat hozzá kimenő hibát az entitáshoz, és az értékeket közbenső értékként használhatja. A jogcímet egy későbbi szabályban kell kiadnia, miután létrehozta a megváltoztathatatlan AZONOSÍTÓként használandó értéket.

**2. szabály: annak ellenőrzése, hogy létezik-e ms-DS-consistencyguid a felhasználó számára**

```claim-rule-language
NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
=> add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");
```

Ez a szabály egy **idflag** nevű ideiglenes jelzőt határoz meg, amely **useguid** van beállítva, ha nincs **MS-DS-consistencyguid** feltöltve a felhasználó számára. A mögöttes logika az a tény, hogy AD FS nem engedélyezi az üres jogcímeket. Tehát a jogcímek hozzáadásakor `http://contoso.com/ws/2016/02/identity/claims/objectguid` és `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` az 1. szabályban csak akkor fejeződik be egy **msdsconsistencyguid** -jogcím, ha az érték a felhasználó számára van feltöltve. Ha nincs feltöltve, AD FS úgy látja, hogy üres értékkel fog rendelkezni, és azonnal eldobja. Az összes objektum **ObjectGUID** fog rendelkezni, így az 1. szabály végrehajtása után a jogcím mindig ott marad.

**3. szabály: az MS-DS-consistencyguid nem módosítható azonosító kiadása, ha van**

```claim-rule-language
c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);
```

Ez egy implicit **létezési** vizsgálat. Ha a jogcím értéke létezik, ezt a problémát nem módosítható AZONOSÍTÓként adja ki. Az előző példa a **NameIdentifier** jogcímet használja. Ezt a megfelelő jogcím-típusra kell módosítania a környezetben megváltoztathatatlan AZONOSÍTÓhoz.

**4. szabály: a objectGuid nem változtatható AZONOSÍTÓként való kiadása, ha az MS-DS-consistencyGuid nincs jelen**

```claim-rule-language
c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
&& c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);
```

Ebben a szabályban egyszerűen ellenőrzi az ideiglenes jelző **idflag**. Ön dönti el, hogy a jogcímet az értéke alapján állítja-e ki.

> [!NOTE]
> Ezeknek a szabályoknak a sorszáma fontos.

### <a name="sso-with-a-subdomain-upn"></a>SSO altartománybeli egyszerű felhasználónévvel

Az [új összevont tartomány hozzáadása](how-to-connect-fed-management.md#addfeddomain)című témakörben leírtak szerint több tartományt is hozzáadhat összevontként Azure ad Connect használatával. Azure AD Connect a 1.1.553.0 és a legújabb verzió automatikusan létrehozza a megfelelő issuerID. Ha nem tudja használni a Azure AD Connect 1.1.553.0 vagy legújabb verzióját, ajánlott az [Azure ad RPT jogcím-szabályok](https://aka.ms/aadrptclaimrules) eszközzel létrehozni és beállítani az Azure ad függő entitás megbízhatóságának megfelelő jogcímeket.

## <a name="next-steps"></a>Következő lépések
További információ a [felhasználói bejelentkezési lehetőségekről](plan-connect-user-signin.md).