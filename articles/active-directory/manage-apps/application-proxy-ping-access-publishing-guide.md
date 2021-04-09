---
title: Fejléc-alapú hitelesítés a PingAccess for Azure AD Application Proxy
description: Alkalmazások közzététele a PingAccess és az App proxyval a fejléc-alapú hitelesítés támogatásához.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 4abef9c848a32d9fa6a34eabe407c4d10f913797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643796"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Fejlécalapú hitelesítés alkalmazásproxyval és PingAccess segítségével történő egyszeri bejelentkezéshez

Az Azure Active Directory (Azure AD) alkalmazásproxy partneri kapcsolatban áll a PingAccess, így az Azure AD-ügyfelek több alkalmazáshoz is hozzáférhetnek. A PingAccess egy másik lehetőséget biztosít [az integrált fejléc-alapú egyszeri bejelentkezésnél](application-proxy-configure-single-sign-on-with-headers.md).

## <a name="whats-pingaccess-for-azure-ad"></a>Mi a PingAccess az Azure AD-hez?

A PingAccess for Azure AD lehetővé teszi a felhasználók számára, hogy hozzáférést és egyszeri bejelentkezést (SSO) biztosítson a fejléceket használó alkalmazásokhoz a hitelesítéshez. Az alkalmazásproxy ezeket az alkalmazásokat, mint bármely más, az Azure AD használatával hitelesíti a hozzáférést, majd továbbítja a forgalmat az összekötő szolgáltatáson keresztül. A PingAccess az alkalmazások előtt ül, és lefordítja a hozzáférési jogkivonatot az Azure AD-ből egy fejlécbe. Az alkalmazás ezután az általa olvasható formátumban fogadja a hitelesítést.

A felhasználók nem láthatnak mást, amikor bejelentkeznek a céges alkalmazásai használatára. Továbbra is bárhonnan dolgozhatnak bármely eszközről. Az alkalmazásproxy összekapcsolja az összes alkalmazáshoz való közvetlen távoli forgalmat a hitelesítési típusuk nélkül, így továbbra is automatikusan betöltődik.

## <a name="how-do-i-get-access"></a>Hogyan a hozzáférés?

Mivel ez a forgatókönyv Azure Active Directory és PingAccess közötti partnerségből származik, mindkét szolgáltatáshoz licenc szükséges. Prémium szintű Azure Active Directory előfizetések azonban egy alapszintű PingAccess-licencet tartalmaznak, amely akár 20 alkalmazást is magában foglal. Ha több mint 20 fejléc-alapú alkalmazást kell közzétennie, vásárolhat további licencet a PingAccess.

További információ: [Azure Active Directory kiadások](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Az alkalmazás közzététele az Azure-ban

Ez a cikk azt ismerteti, hogy a felhasználók első alkalommal teszik közzé az alkalmazást ebben a forgatókönyvben. A közzétételi lépések részletes ismertetése mellett végigvezeti Önt az alkalmazásproxy és a PingAccess első lépésein is. Ha már konfigurálta mindkét szolgáltatást, de a közzétételi lépéseket szeretné frissíteni, ugorjon az [alkalmazás hozzáadása az Azure ad](#add-your-application-to-azure-ad-with-application-proxy) -hez az alkalmazásproxy szakaszban.

> [!NOTE]
> Mivel ez a forgatókönyv az Azure AD és a PingAccess közötti együttműködés, néhány útmutató létezik a ping Identity webhelyen.

### <a name="install-an-application-proxy-connector"></a>Alkalmazásproxy-összekötő telepítése

Ha engedélyezte az alkalmazásproxy engedélyezését, és már telepített egy összekötőt, ugorja át ezt a szakaszt, és lépjen az [alkalmazás hozzáadása az Azure ad-hez az alkalmazásproxy](#add-your-application-to-azure-ad-with-application-proxy)használatával lehetőségre.

Az alkalmazásproxy-összekötő egy olyan Windows Server-szolgáltatás, amely a távoli alkalmazottaktól a közzétett alkalmazásokba irányítja a forgalmat. Részletesebb telepítési útmutatót a következő témakörben talál [: oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez az Application proxyn keresztül Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként. Megjelenik a **Azure Active Directory felügyeleti központ** lap.
1. Válassza az **Azure Active Directory**  >  **alkalmazásproxy**  >  **letöltése összekötő szolgáltatást**. Megjelenik az **alkalmazásproxy-összekötő letöltési** lapja.

   ![Alkalmazásproxy-összekötő letöltése](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Kövesse a telepítési utasításokat.

Az összekötő letöltésének automatikusan engedélyeznie kell az alkalmazásproxy-t a címtárban, de ha nem, akkor válassza az **alkalmazásproxy engedélyezése** lehetőséget.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Alkalmazás felvétele az Azure AD-be alkalmazásproxy használatával

A Azure Portal két műveletet kell végrehajtania. Először közzé kell tennie az alkalmazást az Application proxyval. Ezután össze kell gyűjtenie az alkalmazással kapcsolatos adatokat, amelyeket a PingAccess lépések során használhat.

#### <a name="publish-your-application"></a>Az alkalmazás közzététele

Először közzé kell tennie az alkalmazást. Ez a művelet a következőket foglalja magában:

- Helyszíni alkalmazás hozzáadása az Azure AD-hez
- Felhasználó kiosztása az alkalmazás teszteléséhez és a fejléc-alapú egyszeri bejelentkezés kiválasztásához
- Az alkalmazás átirányítási URL-címének beállítása
- Engedélyek megadása a felhasználók és más alkalmazások számára a helyszíni alkalmazás használatához

Saját helyszíni alkalmazás közzététele:

1. Ha nem az utolsó szakaszban, jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként.
1. Válassza a **vállalati alkalmazások**  >  **új alkalmazás**  >  **helyi alkalmazás hozzáadása lehetőséget**. Megjelenik a **saját helyszíni alkalmazás hozzáadása** lap.

   ![Saját helyszíni alkalmazás hozzáadása](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkkal. Az alábbi útmutatást használhatja a beállításokhoz.

   > [!NOTE]
   > A lépés részletes ismertetését lásd: helyszíni [alkalmazás hozzáadása az Azure ad-](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)hez.

   1. **Belső URL-cím**: általában megadja azt az URL-címet, amely az alkalmazás bejelentkezési lapjára viszi, amikor a vállalati hálózaton dolgozik. Ebben az esetben az összekötőnek a PingAccess-proxyt kell kezelnie az alkalmazás első oldalán. Használja ezt a formátumot: `https://<host name of your PingAccess server>:<port>` . Alapértelmezés szerint a port 3000, de a PingAccess-ben is konfigurálható.

      > [!WARNING]
      > Az ilyen típusú egyszeri bejelentkezéshez a belső URL-címnek kell használnia, `https` és nem használható `http` . Emellett meg kell adni egy korlátozást, ha egy olyan alkalmazást konfigurál, amely nem rendelkezik két alkalmazással ugyanazzal a belső URL-címmel, mivel ez lehetővé teszi az alkalmazás-proxy számára az alkalmazások közötti különbségtételt.

   1. **Előhitelesítési módszer**: válassza a **Azure Active Directory** lehetőséget.
   1. **URL-cím lefordítása a fejlécekben**: válassza a **nem** lehetőséget.

   > [!NOTE]
   > Ha ez az első alkalmazása, az 3000-es porton kezdje el, és térjen vissza a beállítás frissítéséhez, ha módosítja a PingAccess-konfigurációt. További alkalmazások esetén a portnak meg kell egyeznie a PingAccess-ben konfigurált figyelővel. További információ a [PingAccess lévő figyelőkről](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Válassza a **Hozzáadás** lehetőséget. Megjelenik az új alkalmazás Áttekintés lapja.

Most rendeljen hozzá egy felhasználót az alkalmazás teszteléséhez, és válassza a fejléc-alapú egyszeri bejelentkezés lehetőséget:

1. Az alkalmazás-oldalsávon válassza a **felhasználók és csoportok**  >  **felhasználói**  >  **felhasználók és csoportok hozzáadása lehetőséget ( \<Number> kiválasztva)**. Megjelenik a felhasználók és csoportok listája.

   ![A felhasználók és csoportok listájának megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Válasszon ki egy felhasználót az alkalmazás teszteléséhez, és válassza a **kiválasztás** lehetőséget. Győződjön meg arról, hogy a teszt fiók hozzáfér a helyszíni alkalmazáshoz.
1. Válassza a **Hozzárendelés** elemet.
1. Az alkalmazás-oldalsávon válassza az **egyszeri bejelentkezés**  >  **fejléc-alapú** lehetőséget.

   > [!TIP]
   > Ha első alkalommal használja a fejléc-alapú egyszeri bejelentkezést, telepítenie kell a PingAccess. Annak ellenőrzéséhez, hogy az Azure-előfizetés automatikusan társítva van-e a PingAccess-telepítéshez, a PingAccess letöltéséhez használja az egyszeri bejelentkezési oldalon található hivatkozást. Most már megnyithatja a letöltési helyet, vagy később is visszatérhet erre a lapra.

   ![Fejléc-alapú bejelentkezési képernyő és PingAccess megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Kattintson a **Mentés** gombra.

Ezután ellenőrizze, hogy az átirányítási URL-cím be van-e állítva a külső URL-címre:

1. A **Azure Active Directory felügyeleti központ** oldalsávján válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk** lehetőséget. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Válassza az **átirányítási URI**-k elem melletti hivatkozást, amely megjeleníti a webes és a nyilvános ügyfelek számára beállított átirányítási URI-k számát. Megjelenik a **\<application name> -Authentication** lap.
1. Győződjön meg arról, hogy az alkalmazáshoz hozzárendelt külső URL-cím az **átirányítási URI** -k listájában van-e. Ha nem, adja hozzá a külső URL-címet most a **webes** ÁTirányítási URI-típus használatával, majd válassza a **Mentés** lehetőséget.

A külső URL-címen kívül az átirányítási URI-k listájához fel kell venni Azure Active Directory a külső URL-címen található engedélyezési végpontot is.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Végül állítsa be a helyszíni alkalmazást úgy, hogy a felhasználók olvasási hozzáféréssel rendelkezzenek, és más alkalmazásokhoz írási/olvasási hozzáférésük van:

1. Az alkalmazáshoz tartozó **Alkalmazásregisztrációk** oldalsávon válassza az **API-engedélyek**  >  **Hozzáadás engedély**  >  **Microsoft API**-k  >  **Microsoft Graph** lehetőséget. Megjelenik a **Microsoft Graph** **API-engedélyei** lap, amely a Windows Azure Active Directory API-kat tartalmazza.

   ![Megjeleníti a kérelem API-engedélyei lapot.](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Válassza a **delegált engedélyek**  >  **felhasználói**  >  **felhasználó. olvasás** lehetőséget.
1. Válassza az **Application permissions**  >  **Application**  >  **Application. ReadWrite. All** elemet.
1. Válassza az **engedélyek hozzáadása** lehetőséget.
1. Az **API-engedélyek** lapon válassza a **\<your directory name> rendszergazdai jóváhagyás megadása** lehetőséget.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Információk gyűjtése a PingAccess lépéseiről

Az alkalmazás PingAccess való beállításához össze kell gyűjtenie ezeket a három információt (az összes GUID-azonosítót):

| Az Azure AD-mező neve | PingAccess mező neve | Adatformátum |
| --- | --- | --- |
| **Alkalmazás (ügyfél) azonosítója** | **Ügyfél-azonosító** | GUID |
| **Címtár (bérlő) azonosítója** | **Kibocsátó** | GUID |
| `PingAccess key` | **Ügyfél titka** | Véletlenszerű karakterlánc |

Az adatok összegyűjtése:

1. A **Azure Active Directory felügyeleti központ** oldalsávján válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk** lehetőséget. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást. Megjelenik az alkalmazás **Alkalmazásregisztrációk** lapja.

   ![Az alkalmazások regisztrációjának áttekintése](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Az **alkalmazás (ügyfél) azonosítójának** értéke mellett válassza a **Másolás vágólapra** ikont, majd másolja és mentse. Ezt az értéket később a PingAccess ügyfél-AZONOSÍTÓJAként kell megadni.
1. A **könyvtár (bérlő) azonosítójának** értékeként válassza a **Másolás a vágólapra** lehetőséget, majd másolja és mentse. Ezt az értéket később a PingAccess kiállítójának kell megadnia.
1. Az alkalmazás **Alkalmazásregisztrációk** oldalsávján válassza a **tanúsítványok és titkos kulcsok**  >  **új ügyfél titka** lehetőséget. Megjelenik az **ügyfél titkos kulcsának hozzáadása** lap.

   ![Az ügyfél titkos kulcsának hozzáadása oldal megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. A **Leírás** mezőbe írja be a következőt: `PingAccess key` .
1. A **lejárat** szakaszban válassza ki a PingAccess kulcs beállításának módját: **1 év**, **2 év** vagy **soha**.
1. Válassza a **Hozzáadás** lehetőséget. A PingAccess kulcs az ügyfél titkos kulcsainak táblázatában jelenik meg, és egy véletlenszerű karakterláncot, amely az **érték** mezőben kitöltést eredményez.
1. A PingAccess kulcs **értéke** mező mellett válassza a **Másolás vágólapra** ikont, majd másolja és mentse. Ezt az értéket később is megadhatja a PingAccess-ügyfél titkos kulcsaként.

**A `acceptMappedClaims` mező frissítése:**

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként.
1. Válassza ki **Azure Active Directory**  >  **Alkalmazásregisztrációk**. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Az alkalmazás **Alkalmazásregisztrációk** oldalának oldalsávján válassza a **jegyzékfájl** lehetőséget. Megjelenik az alkalmazás regisztrálásához tartozó JSON-kód.
1. Keresse meg a `acceptMappedClaims` mezőt, és módosítsa az értéket a következőre: `True` .
1. Kattintson a **Mentés** gombra.

### <a name="use-of-optional-claims-optional"></a>Választható jogcímek használata (nem kötelező)

A választható jogcímek lehetővé teszik, hogy a felhasználók és a bérlők által biztosított szabványos, de nem belefoglalt alapértelmezett jogcímeket adja hozzá. Az alkalmazás jegyzékfájljának módosításával opcionális jogcímeket konfigurálhat az alkalmazáshoz. További információ: az [Azure ad-alkalmazás jegyzékfájljának ismertetése.](../develop/reference-app-manifest.md)

Például az e-mail-cím belefoglalása a access_tokenba, amelyet a PingAccess fog használni:

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Jogcím-hozzárendelési szabályzat használata (opcionális)

A [jogcím-hozzárendelési szabályzat (előzetes verzió)](../develop/active-directory-claims-mapping.md#claims-mapping-policy-properties) olyan attribútumok esetében, amelyek nem léteznek a AzureAD-ben. A jogcímek leképezése lehetővé teszi a régi helyszíni alkalmazások áttelepítése a felhőbe olyan további egyéni jogcímek hozzáadásával, amelyeket az ADFS vagy a felhasználói objektumok támogatnak.

Ahhoz, hogy az alkalmazás egyéni jogcímet használjon, és további mezőket is tartalmazzon, győződjön meg arról, hogy [létrehozta az egyéni jogcím-hozzárendelési szabályzatot, és hozzárendelte azt az alkalmazáshoz](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Egyéni jogcím használatához egyéni szabályzatot is meg kell adni, és hozzá kell rendelni az alkalmazáshoz. Ennek a szabályzatnak tartalmaznia kell az összes kötelező egyéni attribútumot.
>
> A házirend-definíciót és a hozzárendelést a PowerShell vagy a Microsoft Graph használatával végezheti el. Ha ezeket a PowerShellben végzi el, előfordulhat, hogy először a alkalmazáshoz kell használnia, `New-AzureADPolicy` majd hozzá kell rendelnie az alkalmazást `Add-AzureADServicePrincipalPolicy` . További információ: jogcím- [hozzárendelési szabályzat-hozzárendelés](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Példa:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Egyéni jogcímek használatának engedélyezése a PingAccess

A PingAccess egyéni jogcímek használatára való engedélyezése nem kötelező, de szükség van rá, ha azt szeretné, hogy az alkalmazás további jogcímeket használjon.

Ha a következő lépésben konfigurálja a PingAccess, a létrehozandó webes munkamenetnek (Settings->Access->web Sessions) tartalmaznia kell a **kérelem profilját** , és a **nem** értékre állított **felhasználói attribútumokat kell frissítenie** .

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess letöltése és az alkalmazás konfigurálása

Most, hogy végrehajtotta az összes Azure Active Directory telepítési lépést, áthelyezheti a PingAccess konfigurálásához.

A forgatókönyv PingAccess részének részletes lépései a ping Identity dokumentációjában olvashatók. Kövesse a PingAccess konfigurálása az Azure AD-hoz című témakör utasításait a ping Identity webhelyen [Microsoft Azure ad alkalmazásproxy használatával közzétett alkalmazások számára](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) , és töltse le a [PingAccess legújabb verzióját](https://www.pingidentity.com/en/lp/azure-download.html?).

Ezek a lépések segítséget nyújtanak a PingAccess telepítéséhez és egy PingAccess-fiók beállításához (ha még nem rendelkezik ilyennel). Ezután egy Azure AD OpenID Connect-(OIDC-) kapcsolat létrehozásához állítson be egy jogkivonat-szolgáltatót az Azure AD-portálról másolt **címtár-(bérlői) azonosító** értékkel. Következő lépésként hozzon létre egy webes munkamenetet a PingAccess, és használja az **alkalmazás (ügyfél) azonosítóját** és `PingAccess key` értékeit. Ezután beállíthatja az identitás-hozzárendelést, és létrehozhat egy virtuális gazdagépet, webhelyet és alkalmazást.

### <a name="test-your-application"></a>Az alkalmazás tesztelése

Az összes lépés elvégzése után az alkalmazásnak futnia kell. A teszteléshez nyisson meg egy böngészőt, és navigáljon arra a külső URL-címre, amelyet az alkalmazás Azure-ban való közzétételekor hozott létre. Jelentkezzen be az alkalmazáshoz rendelt tesztelési fiókkal.

## <a name="next-steps"></a>Következő lépések

- [A PingAccess konfigurálása az Azure AD-hoz a Microsoft Azure AD alkalmazásproxy használatával közzétett alkalmazások védelemmel való ellátásához](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](what-is-single-sign-on.md)
- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)