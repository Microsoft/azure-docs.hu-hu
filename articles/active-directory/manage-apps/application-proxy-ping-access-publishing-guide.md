---
title: Fejlécalapú hitelesítés PingAccess használatával az Azure AD-alkalmazásproxy
description: Alkalmazások közzététele a PingAccess és az Alkalmazásproxy használatával a fejlécalapú hitelesítés támogatásához.
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
ms.openlocfilehash: 23008d785c27b901f3487d3eddff7cb8e7529f6e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600080"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Fejlécalapú hitelesítés alkalmazásproxyval és PingAccess segítségével történő egyszeri bejelentkezéshez

Azure Active Directory (Azure AD) alkalmazásproxy a PingAccessvel, hogy az Azure AD-ügyfelek több alkalmazáshoz is hozzáférnek. A PingAccess az integrált fejlécalapú egyszeri bejelentkezésen kívül egy másik lehetőséget [is kínál.](application-proxy-configure-single-sign-on-with-headers.md)

## <a name="whats-pingaccess-for-azure-ad"></a>Mi az az Azure AD-hez tartozó PingAccess?

Az Azure AD-hez használható PingAccess segítségével hozzáférést és egyszeri bejelentkezést (SSO) adhat a felhasználóknak az olyan alkalmazásoknak, amelyek fejléceket használnak a hitelesítéshez. alkalmazásproxy úgy kezeli ezeket az alkalmazásokat, mint bármely más alkalmazást, az Azure AD használatával hitelesíti a hozzáférést, majd az összekötő szolgáltatáson keresztül halad át a forgalomon. A PingAccess az alkalmazások előtt található, és fejlécre fordítja le a hozzáférési jogkivonatot az Azure AD-ból. Az alkalmazás ezután az olvasható formátumban kapja meg a hitelesítést.

A felhasználók semmit sem fognak észre venni, amikor bejelentkeznek a vállalati alkalmazások használatára. Továbbra is bárhonnan dolgozhatnak bármilyen eszközön. A alkalmazásproxy a távoli forgalmat minden alkalmazáshoz a hitelesítés típusától függően irányítják, így továbbra is automatikusan el tudják egyensúlyba hozni a terhelést.

## <a name="how-do-i-get-access"></a>Hogyan kap hozzáférést?

Mivel ez a forgatókönyv a Azure Active Directory És a PingAccess közötti együttműködésből származik, mindkét szolgáltatáshoz licencre van szüksége. A prémium szintű Azure Active Directory azonban tartalmaznak egy alapszintű PingAccess-licencet, amely legfeljebb 20 alkalmazást fed le. Ha több mint 20 fejlécalapú alkalmazást kell közzétennie, vásárolhat egy további licencet a PingAccess szolgáltatásból.

További információ: Azure Active Directory [kiadása.](../fundamentals/active-directory-whatis.md)

## <a name="publish-your-application-in-azure"></a>Az alkalmazás közzététele az Azure-ban

Ez a cikk az alkalmazás első közzétételét teszi lehetővé ezzel a forgatókönyvvel. A közzétételi lépések részletezés mellett végigvezeti a alkalmazásproxy és a PingAccess alkalmazásproxy is. Ha már mindkét szolgáltatást konfigurálta, de szeretné, hogy frissítsen a közzétételi lépésekről, ugorjon az Add [your application to Azure AD with alkalmazásproxy](#add-your-application-to-azure-ad-with-application-proxy) (Alkalmazás hozzáadása az Azure AD-hez a alkalmazásproxy szakaszban.

> [!NOTE]
> Mivel ez a forgatókönyv az Azure AD és a PingAccess együttműködése, néhány utasítás a Ping Identity webhelyen található.

### <a name="install-an-application-proxy-connector"></a>Összekötő alkalmazásproxy telepítése

Ha már engedélyezte a alkalmazásproxy és telepített egy összekötőt, kihagyhatja ezt a szakaszt, és az Alkalmazás hozzáadása az Azure AD-hez a következővel: [alkalmazásproxy.](#add-your-application-to-azure-ad-with-application-proxy)

A alkalmazásproxy összekötő egy Windows Server-szolgáltatás, amely a távoli alkalmazottaktól származó forgalmat a közzétett alkalmazásokhoz irányítja. Részletesebb telepítési utasításokért [lásd: Oktatóanyag: Helyszíni](application-proxy-add-on-premises-application.md)alkalmazás hozzáadása táveléréshez alkalmazásproxy a Azure Active Directory.

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként. Megjelenik **Azure Active Directory Felügyeleti központ** lapja.
1. Válassza **Azure Active Directory**  >  **alkalmazásproxy**  >  **összekötő-szolgáltatás letöltése lehetőséget.** Megjelenik **alkalmazásproxy Összekötő letöltése** lap.

   ![Alkalmazásproxy-összekötő letöltése](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Kövesse a telepítési utasításokat.

Az összekötő letöltésének automatikusan engedélyeznie kell alkalmazásproxy címtárhoz való csatlakozást, de ha nem, válassza az **Enable alkalmazásproxy** lehetőséget.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Az alkalmazás hozzáadása az Azure AD-hez alkalmazásproxy

A következő két műveletet kell Azure Portal. Először közzé kell tennie az alkalmazást a alkalmazásproxy. Ezután be kell gyűjtenie néhány információt az alkalmazásról, amely a PingAccess lépései során használható.

#### <a name="publish-your-application"></a>Az alkalmazás közzététele

Először közzé kell tennie az alkalmazást. Ez a művelet a következőt foglalja magában:

- Helyszíni alkalmazás hozzáadása az Azure AD-hez
- Felhasználó hozzárendelése az alkalmazás teszteléséhez és a fejlécalapú SSO kiválasztásához
- Az alkalmazás átirányítási URL-címének beállítása
- Engedélyek megadása a felhasználók és más alkalmazások számára a helyszíni alkalmazás használatára

Saját helyszíni alkalmazás közzététele:

1. Ha az előző szakaszban nem, jelentkezzen be a Azure Active Directory [portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként.
1. Válassza **a Vállalati alkalmazások** Új  >  **alkalmazás** Helyszíni alkalmazás hozzáadása  >  **lehetőséget.** Megjelenik **a Saját helyszíni alkalmazás hozzáadása** lap.

   ![Saját helyszíni alkalmazás hozzáadása](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkkal. A beállításokhoz használja az alábbi útmutatást.

   > [!NOTE]
   > A lépés részletesebb bemutatójért lásd: Helyszíni alkalmazás hozzáadása az [Azure AD-hez.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)

   1. **Belső** URL-cím: Általában azt az URL-címet adja meg, amely az alkalmazás bejelentkezési oldalára viszi, amikor a vállalati hálózaton van. Ebben a forgatókönyvben az összekötőnek a PingAccess-proxyt kell az alkalmazás kezdőlapjaként kezelnie. Használja a következő formátumot: `https://<host name of your PingAccess server>:<port>` . A port alapértelmezés szerint 3000, de a PingAccessben konfigurálhatja.

      > [!WARNING]
      > Az ilyen típusú egyszeri bejelentkezéshez a belső URL-címnek a következőt kell használnia: , és `https` nem használhatja a következőt: `http` . Emellett az alkalmazások konfigurálásakor egy olyan korlátozás is érvényes, amely szerint két alkalmazásnak nem kell ugyanazokkal a belső URL-címével lennie, mivel így az alkalmazásproxy képes megkülönböztetést tartani az alkalmazások között.

   1. **Előhitelesítési módszer:** Válassza a **Azure Active Directory.**
   1. **URL fordítása a fejlécek között:** Válassza a **Nem lehetőséget.**

   > [!NOTE]
   > Ha ez az első alkalmazása, indítsa el a 3000-es portot, és ha módosítja a PingAccess konfigurációját, akkor frissítse ezt a beállítást. A további alkalmazások esetében a portnak meg kell egyeznie a PingAccessben konfigurált figyelővel. További információ a [PingAccess figyelőiről:](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Válassza a **Hozzáadás** lehetőséget. Megjelenik az új alkalmazás áttekintő oldala.

Most rendeljen hozzá egy felhasználót az alkalmazás teszteléséhez, és válassza a fejlécalapú egyszeri bejelentkezést:

1. Az alkalmazás oldalsávon válassza a **Felhasználók** és csoportok Felhasználók és csoportok hozzáadása  >    >  **\<Number> (Kijelölt) lehetőséget.** Megjelenik a felhasználók és csoportok listája, amelyek közül választhat.

   ![A felhasználók és csoportok listáját jeleníti meg](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Válasszon ki egy felhasználót az alkalmazásteszteléshez, majd válassza a **Kijelölés lehetőséget.** Győződjön meg arról, hogy a tesztfiók hozzáfér a helyszíni alkalmazáshoz.
1. Válassza a **Hozzárendelés** elemet.
1. Az alkalmazás oldalsávon válassza az **Egyszeri bejelentkezés**  >  **fejlécalapú lehetőséget.**

   > [!TIP]
   > Ha most először használ fejlécalapú egyszeri bejelentkezést, telepítenie kell a PingAccesst. Annak érdekében, hogy az Azure-előfizetés automatikusan hozzá legyen társítva a PingAccess-telepítéshez, használja az egyszeri bejelentkezési oldalon található hivatkozást a PingAccess letöltéséhez. Most megnyithatja a letöltési webhelyet, vagy később visszatérhet erre az oldalra.

   ![A fejlécalapú bejelentkezési képernyő és a PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Kattintson a **Mentés** gombra.

Ezután ellenőrizze, hogy az átirányítási URL-cím a külső URL-címre van-e állítva:

1. A **felügyeleti Azure Active Directory oldalsávjában** válassza **a** Azure Active Directory  >  **Alkalmazásregisztrációk** lehetőséget. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Válassza az Átirányítási **URI-k melletti** hivatkozást, amely a webes és nyilvános ügyfelek számára beállított átirányítási URI-k számát mutatja. Megjelenik **\<application name> a - Authentication** (Hitelesítés) lap.
1. Ellenőrizze, hogy az alkalmazáshoz korábban hozzárendelt külső URL-cím szerepel-e az **Átirányítási URI-k listában.** Ha nem, adja hozzá most a külső URL-címet egy webes átirányítási URI-típussal, majd válassza a **Mentés lehetőséget.**

A külső URL-cím mellett a külső URL-Azure Active Directory egy engedélyezett végpontját is hozzá kell adni az Átirányítási URI-k listához.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Végül állítsa be a helyszíni alkalmazást úgy, hogy a felhasználók olvasási, más alkalmazások pedig olvasási/írási hozzáféréssel is rendelkeziknak:

1. Az **Alkalmazásregisztrációk** oldalsávon válassza az **API-engedélyek** Engedély hozzáadása  >    >  **Microsoft** API-khoz a  >  **Microsoft Graph.** Megjelenik **az Api-engedélyek kérése** Microsoft Graph lap, amely a Windows-alkalmazások API-ját Azure Active Directory. 

   ![Megjeleníti a Request API permissions (API-engedélyek kérése) lapot](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Válassza **a Delegált engedélyek**  >  **User**  >  **User.Read lehetőséget.**
1. Válassza **az Application**  >    >  **Application.ReadWrite.All alkalmazásengedélyeket.**
1. Válassza **az Engedélyek hozzáadása lehetőséget.**
1. Az **API-engedélyek lapon** válassza a **Rendszergazdai jóváhagyás megadása lehetőséget. \<your directory name>**

#### <a name="collect-information-for-the-pingaccess-steps"></a>Információk gyűjtése a PingAccess lépéseiről

Az alkalmazás PingAccess segítségével való beállításához össze kell gyűjtenie ezt a három információt (az összes GUID azonosítót):

| Az Azure AD-mező neve | A PingAccess mező neve | Adatformátum |
| --- | --- | --- |
| **Alkalmazás (ügyfél) azonosítója** | **Ügyfél-azonosító** | GUID |
| **Címtár (bérlő) azonosítója** | **Kibocsátó** | GUID |
| `PingAccess key` | **Titkos ügyfél titkos** | Véletlenszerű sztring |

Az információk gyűjtéséhez:

1. A **felügyeleti Azure Active Directory oldalsávon** válassza **a** Azure Active Directory  >  **Alkalmazásregisztrációk** lehetőséget. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást. Megjelenik **Alkalmazásregisztrációk** alkalmazás kezdőlapja.

   ![Egy alkalmazás regisztrációja – áttekintés](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Az Alkalmazás **-azonosító értéke** mellett válassza  a Másolás a vágólapra ikont, majd másolja és mentse azt. Ezt az értéket később a PingAccess ügyfél-azonosítójaként adhatja meg.
1. A Címtár **-azonosító (bérlő)** értéke mellett válassza a **Másolás** a vágólapra lehetőséget is, majd másolja és mentse azt. Ezt az értéket később a PingAccess kiállítójaként adhatja meg.
1. Az alkalmazáshoz az alkalmazáshoz **Alkalmazásregisztrációk** oldalsávon válassza a **Tanúsítványok és** titkos kulcsok Új titkos ügyfél  >  **titkos ügyféltitk.** Megjelenik **az Ügyfél titkos gombra való** felvételének oldala.

   ![Megjeleníti a Titkos ügyfél titkos gombra való felvételét bemutató oldalt](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. A Leírás **mezőbe** írja be a következőt: `PingAccess key` .
1. A **Lejárat alatt** válassza ki, hogyan állítsa be a PingAccess-kulcsot: **1** év, **2 év,** vagy **Soha.**
1. Válassza a **Hozzáadás** lehetőséget. A PingAccess-kulcs megjelenik a titkos ügyfélkulcsok táblázatában egy véletlenszerű sztringgel, amely automatikusan kitölti a **VALUE** mezőt.
1. A PingAccess-kulcs **VALUE** (ÉRTÉK)  mezője mellett válassza a Másolás a vágólapra ikont, majd másolja és mentse azt. Ezt az értéket később a PingAccess titkos ügyféltitkának adja meg.

**Frissítse a `acceptMappedClaims` mezőt:**

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként.
1. Válassza **a Azure Active Directory**  >  **Alkalmazásregisztrációk** lehetőséget. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Az alkalmazás Alkalmazásregisztrációk **oldalának** oldalsávján válassza a **Jegyzékfájl lehetőséget.** Megjelenik az alkalmazás regisztrációjához szükséges JSON-jegyzékkód.
1. Keresse meg a `acceptMappedClaims` mezőt, és módosítsa az értéket `True` értékre.
1. Kattintson a **Mentés** gombra.

### <a name="use-of-optional-claims-optional"></a>Választható jogcímek használata (nem kötelező)

A nem kötelező jogcímek lehetővé teszik szabványos, de nem tartalmazott alapértelmezett jogcímek hozzáadását, amelyek minden felhasználóhoz és bérlőhöz tartoznak. Az alkalmazásjegyzék módosításával konfigurálhatja az alkalmazás opcionális jogcímeit. További információ: [Az Azure AD-alkalmazásjegyzék ismertetése](../develop/reference-app-manifest.md)

Példa a PingAccess által access_token e-mail-címnek a fiókba való bekérésére:

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

### <a name="use-of-claims-mapping-policy-optional"></a>Jogcím-leképezési szabályzat használata (nem kötelező)

[Az AzureAD-ban](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties) nem létező attribútumok jogcímleképezési szabályzata (előzetes verzió). A jogcím-leképezés lehetővé teszi a régi, a felhőbe telepített alkalmazások áttelepítését további egyéni jogcímek hozzáadásával, amelyek az ADFS-sel vagy a felhasználói objektumokkal vannak kiegészítve

Ahhoz, hogy az alkalmazás egyéni jogcímet használjon, és további mezőket is tartalmazjon, győződjön meg arról, hogy egyéni jogcím-leképezési szabályzatot is létrehozott, és hozzárendeli az [alkalmazáshoz.](../develop/active-directory-claims-mapping.md)

> [!NOTE]
> Egyéni jogcímek csak akkor használhatók, ha egyéni szabályzatot határoz meg és rendel hozzá az alkalmazáshoz. A szabályzatnak tartalmaznia kell az összes szükséges egyéni attribútumot.
>
> A szabályzatdefiníciót és -hozzárendelést a PowerShell vagy a Microsoft Graph. Ha ezeket a PowerShellben teszi meg, előfordulhat, hogy először a parancsot kell használnia, majd hozzárendelni az `New-AzureADPolicy` alkalmazáshoz az `Add-AzureADServicePrincipalPolicy` használatával. További információ: [Jogcím-hozzárendelési szabályzat hozzárendelése.](../develop/active-directory-claims-mapping.md)

Példa:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Egyéni jogcímek használatának engedélyezése a PingAccess számára

A PingAccess egyéni jogcímek használatára való engedélyezése nem kötelező, de kötelező, ha azt várja, hogy az alkalmazás további jogcímeket használjon.

Amikor a következő lépésben konfigurálja a PingAccesst, a létrehozott webes munkamenetnek (Settings->Access->Web Sessions)  a Kérelemprofilt ki kelljelölve, a Felhasználói attribútumok frissítése beállítást pedig Nemre kell **állítania** 

## <a name="download-pingaccess-and-configure-your-application"></a>A PingAccess letöltése és az alkalmazás konfigurálása

Most, hogy az összes beállítási lépést Azure Active Directory, továbbléphet a PingAccess konfigurálásához.

A forgatókönyv PingAccess részének részletes lépései a Ping Identity dokumentációjában folytatódnak. Kövesse a PingAccess konfigurálása az Azure AD-hez útmutatót a [pingelési](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) identitás webhelyén Microsoft Azure AD alkalmazásproxy közzétett alkalmazások védelméhez, és töltse le a [PingAccess legújabb verzióját.](https://www.pingidentity.com/en/lp/azure-download.html?)

Ezek a lépések segítenek a PingAccess telepítésében és a PingAccess-fiók beállításában (ha még nem rendelkezik ilyen fiókkal). Ezután egy Azure AD OpenID Connect-kapcsolat (OIDC) létrehozásához be kell állítania egy jogkivonat-szolgáltatót az Azure AD portálról kimásott **címtár- (bérlő-)** azonosító értékkel. Ezután webes munkamenet létrehozásához a PingAccess szolgáltatásban az alkalmazás **(ügyfél)** azonosítóját és `PingAccess key` értékeit kell használnia. Ezt követően beállíthatja az identitásleképezést, és létrehozhat egy virtuális gazdagépet, webhelyet és alkalmazást.

### <a name="test-your-application"></a>Az alkalmazás tesztelése

Az összes lépés befejezése után az alkalmazásnak futnia kell. A teszteléshez nyisson meg egy böngészőt, és keresse meg az alkalmazás Azure-ban való közzétételekor létrehozott külső URL-címet. Jelentkezzen be az alkalmazáshoz rendelt tesztfiókkal.

## <a name="next-steps"></a>Következő lépések

- [A PingAccess konfigurálása az Azure AD-hez a Microsoft Azure AD alkalmazásproxy](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Egyszeri bejelentkezés az alkalmazásokba a Azure Active Directory](what-is-single-sign-on.md)
- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)