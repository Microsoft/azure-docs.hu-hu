---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a ping Identity használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható Azure AD B2C hitelesítés a ping Identity használatával
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 94e7ae93d05ae8ee35028882e14d8da74814d833
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650226"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Oktatóanyag: a ping-identitás konfigurálása Azure Active Directory B2C a biztonságos hibrid hozzáféréshez

Ebből az oktatóanyagból megtudhatja, hogyan bővítheti Azure Active Directory (AD) B2C-t a  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) és a [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) használatával a hibrid hozzáférés biztosításához.

Számos meglévő webes tulajdonság, például az e-kereskedelmi webhelyek és az internetre elérhető webalkalmazások egy proxyrendszer mögé kerülnek üzembe, más néven fordított proxy rendszer. Ezek a proxykiszolgálók különböző funkciókat biztosítanak, például az előhitelesítést, a házirendek kényszerítését és a forgalom útválasztását. Példa a használati esetekre: a webalkalmazás védelme a bejövő webes forgalomból, és egységes munkamenet-felügyelet biztosítása az elosztott kiszolgálókon üzemelő példányok között.

A legtöbb esetben ez a konfiguráció egy olyan hitelesítési fordítási réteget tartalmaz, amely externalizes a hitelesítést a webalkalmazásból. A fordított proxyk bekapcsolják a hitelesített felhasználók környezetét a webalkalmazásokhoz, így egyszerűbb formában, például a fejléc értékeként törölhető vagy kivonatoló formában. Ebben a konfigurációban az alkalmazások nem használnak olyan iparági standard tokeneket, mint a Security Assertion Markup Language (SAML), a OAuth vagy az Open ID Connection (OIDC), hanem a proxytól a hitelesítési környezet biztosításához és a munkamenet karbantartásához a végfelhasználói ügynökkel, például a böngészővel vagy a natív alkalmazással. Mivel a szolgáltatás "Man-in-the-middle" néven fut, a proxyk biztosítják a végső munkamenet-vezérlést. Ez azt is jelenti, hogy a proxy szolgáltatásnak nagyon hatékonynak és méretezhetőnek kell lennie, és nem lehet szűk keresztmetszetnek lennie, vagy a proxy szolgáltatás mögötti alkalmazások meghibásodási pontja. A diagram egy tipikus fordított proxy megvalósításának és a kommunikáció folyamatának ábrázolása.

![a képen a fordított proxy implementációja látható](./media/partner-ping/reverse-proxy.png)

Ha olyan helyzetben van, ahol az ilyen konfigurációkban szeretné modernizálni az Identity platformot, az alábbi problémákat tapasztalhatja.

- Hogyan lehet leválasztani az alkalmazások modernizációjának erőfeszítéseit az Identity platform modernizációja alapján?

- Hogyan lehet párhuzamos környezetet létrehozni modern és örökölt hitelesítéssel, a modernizált Identity Service-szolgáltatótól?

  a. Hogyan vezethet a végfelhasználói élmény konzisztenciájához?

  b. Egyszeri bejelentkezéses felhasználói élmény biztosítása a meglévő alkalmazások között

A Azure AD B2C [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) -és [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) -technológiákkal való integrálásával megbeszéljük az ilyen problémák megoldásának módszerét.

## <a name="coexistence-environment"></a>Párhuzamos környezet

Egy technikailag életképes egyszerű megoldás, amely szintén költséghatékony, ha úgy konfigurálja a fordított proxy rendszerét, hogy a modernizált identitásrendszer használatára legyen konfigurálva, és delegálja a hitelesítést.  
Ebben az esetben a proxyk támogatják a modern hitelesítési protokollokat, és az átirányítási (passzív) hitelesítést használják, amely elküldi a felhasználót az új identitás-szolgáltatónak (identitásszolgáltató).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C identitás-szolgáltatóként

A Azure AD B2C olyan **házirendeket** határozhat meg, amelyek különböző felhasználói élményeket és viselkedéseket vezetnek be, amelyek a kiszolgáló végéről összeállított **felhasználói úton** is ismertek. Minden ilyen szabályzat egy protokoll-végpontot tesz elérhetővé, amely a hitelesítést úgy hajthatja végre, mintha identitásszolgáltató lenne. Bizonyos házirendek esetében nem szükséges különleges kezelés az alkalmazási oldalon. Az alkalmazás egyszerűen egy iparági szabványnak megfelelő hitelesítési kérést tesz elérhetővé az érdeklődési szabályzat által elérhető protokoll-specifikus hitelesítési végpont számára.  
A Azure AD B2C konfigurálható úgy, hogy ugyanazt a kiállítót több házirendben vagy egyedi kiállítóban ossza meg az egyes házirendek esetében. Mindegyik alkalmazás egy vagy több házirendre mutathat egy protokoll natív hitelesítési kérelmének elvégzésével, valamint a kívánt felhasználói viselkedésmódok, például a bejelentkezés, a regisztráció és a profil szerkesztéséhez. A diagramon az OIDC és az SAML alkalmazás munkafolyamatai láthatók.

![a képen a OIDC és az SAML implementációja látható](./media/partner-ping/azure-ad-identity-provider.png)

Habár az említett forgatókönyv jól működik a modernizált alkalmazások esetében, kihívást jelenthet, hogy az örökölt alkalmazások megfelelően átirányítsák a felhasználót, mert az alkalmazások hozzáférési kérelme nem tartalmazza a felhasználói élmény környezetét. A legtöbb esetben a proxy réteg vagy a webalkalmazáshoz tartozó integrált ügynök elfogja a hozzáférési kérelmet.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess fordított proxyként

Számos ügyfél fordított proxyként telepítette a PingAccess, hogy egy vagy több szerepkört játsszon a cikkben korábban említettek szerint. A PingAccess olyan közvetlen kérést tud elfoglalni, amely a webalkalmazás-kiszolgálón futó ügynöktől származik, vagy egy átirányítás.

A PingAccess konfigurálható a OIDC, a OAuth2 vagy az SAML használatával a felsőbb rétegbeli hitelesítésszolgáltató hitelesítésének végrehajtásához. Erre a célra egyetlen felsőbb rétegbeli identitásszolgáltató konfigurálható a PingAccess-kiszolgálón. Az alábbi ábrán ez a konfiguráció látható.

![a képen látható a PingAccess és a OIDC implementációja](./media/partner-ping/authorization-flow.png)

Egy tipikus Azure AD B2C üzemelő példányban, ahol több házirend is kikerül több **IDP**, kihívást jelent. Mivel a PingAccess-t csak egyetlen felsőbb rétegbeli identitásszolgáltató lehet konfigurálni.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate összevonási proxyként

A PingFederate egy vállalati identitás-híd, amely teljes mértékben konfigurálható hitelesítési szolgáltatóként vagy más, több felsőbb rétegbeli IDP proxyként, ha szükséges. A következő ábra ezt a képességet mutatja be.

![a képen a PingFederate implementációja látható](./media/partner-ping/pingfederate.png)

Ezzel a képességgel környezetfüggő/dinamikusan vagy deklaratív módon válthat egy bejövő kérelmet egy adott Azure AD B2C házirendre. A következő ábrán a protokollok sorozatának folyamata látható a konfigurációhoz.

![a rendszerkép a PingAccess és a PingFederate munkafolyamatot jeleníti meg](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, szerezzen be egy [ingyenes fiókot](https://azure.microsoft.com/free/).

- Egy [Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

- Docker-tárolókban vagy közvetlenül Azure-beli virtuális gépeken üzembe helyezett PingAccess és PingFederate.

## <a name="connectivity"></a>Kapcsolat

Győződjön meg arról, hogy a következő csatlakozik.

- **PingAccess-kiszolgáló** – képes kommunikálni a PingFederate-kiszolgálóval, az ügyféloldali böngészővel, a OIDC, a OAuth jól ismert és kulcsok felderítésével, amelyet a Azure ad B2C szolgáltatás és a PingFederate-kiszolgáló tesz közzé.

- **PingFederate-kiszolgáló** – képes kommunikálni a PingAccess-kiszolgálóval, az ügyféloldali böngészővel, a OIDC, a OAuth jól ismert és kulcsok felderítésével, amelyet a Azure ad B2C szolgáltatás tesz közzé.

- **Örökölt vagy header-alapú AuthN-alkalmazás** – képes kommunikálni a PingAccess-kiszolgálóval és a-ból.

- **SAML függő entitás alkalmazása** – képes elérni a böngésző adatforgalmát az ügyfélről. Hozzáférhet a Azure AD B2C szolgáltatás által közzétett SAML-összevonási metaadatokhoz.

- **Modern alkalmazás** – képes elérni a böngésző adatforgalmát az ügyfélről. Hozzáférhet a Azure AD B2C szolgáltatás által közzétett, jól ismert és kulcsok felderítésére szolgáló OIDC.

- **REST API** – képes elérni a forgalmat egy natív vagy webes ügyfélről. Hozzáférhet a Azure AD B2C szolgáltatás által közzétett, jól ismert és kulcsok felderítésére szolgáló OIDC.

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C konfigurálása

Erre a célra használhatja az alapszintű felhasználói folyamatokat vagy a speciális Identity Enterprise Framework-(IEF-) szabályzatokat. A PingAccess a **kibocsátó** értéke alapján hozza létre a metaadat-végpontot a [webfinger](https://tools.ietf.org/html/rfc7033) -alapú felderítési konvenció használatával.
Az egyezmény követéséhez frissítse a Azure AD B2C kiállítói frissítést a felhasználói folyamatok házirend tulajdonságaival.

![a rendszerkép a jogkivonat-beállításokat jeleníti meg](./media/partner-ping/token-setting.png)

A speciális szabályzatokban ez a **IssuanceClaimPattern** metaadat-elemmel konfigurálható a [JWT-jogkivonat kiállítói technikai profiljában](./jwt-issuer-technical-profile.md)lévő **AuthorityWithTfp** értékre.

## <a name="configure-pingaccesspingfederate"></a>PingAccess/PingFederate konfigurálása

A következő szakasz ismerteti a szükséges konfigurációt.
A diagram az integráció általános felhasználói folyamatát szemlélteti.

![a képen a PingAccess és a PingFederate integrációja látható](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>A PingFederate konfigurálása jogkivonat-szolgáltatóként

Ha a PingFederate-t a PingAccess jogkivonat-szolgáltatóként szeretné konfigurálni, akkor a PingFederate és a PingAccess közötti kapcsolatot a rendszer a PingAccess és a PingFederate közötti kapcsolattal követi.  
A konfigurációs lépésekről [ebben a cikkben](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) talál további információt.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>PingAccess-alkalmazás konfigurálása a fejléc-alapú hitelesítéshez

Létre kell hozni egy PingAccess alkalmazást a cél webalkalmazáshoz a fejléc-alapú hitelesítéshez. Hajtsa végre az alábbi lépéseket.

#### <a name="step-1--create-a-virtual-host"></a>1. lépés – virtuális gazdagép létrehozása

>[!IMPORTANT]
>Ennek a megoldásnak a konfigurálásához minden alkalmazáshoz létre kell hozni a virtuális gazdagépet. A konfigurációs szempontokkal és azok hatásaival kapcsolatos további információkért tekintse meg a [legfontosabb szempontokat](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

A virtuális gazdagépek létrehozásához kövesse az alábbi lépéseket:

1. Válassza a **Beállítások**  >  **hozzáférés**  >  **virtuális gazdagépek** lehetőséget.

2. Válassza a **virtuális gazdagép hozzáadása** lehetőséget.

3. A gazdagép mezőben adja meg az alkalmazás URL-címének FQDN részét.

4. A port mezőben adja meg a **443** értéket.

5. Válassza a **Mentés** lehetőséget

#### <a name="step-2--create-a-web-session"></a>2. lépés – webes munkamenet létrehozása

Webes munkamenet létrehozásához kövesse az alábbi lépéseket:

1. Navigáljon a **Beállítások**  >  **hozzáférés**  >  **webes munkamenetekhez**

2. Válassza a **webes munkamenet hozzáadása** lehetőséget

3. Adja meg a webes munkamenet **nevét** .

4. Válassza ki a **cookie típusát**, vagy **aláírt JWT** vagy **titkosított JWT**

5. Egyedi érték megadása a **célközönség** számára

6. Az **ügyfél-azonosító** mezőben adja meg az **Azure ad-alkalmazás azonosítóját**

7. Az **ügyfél titkos** kulcsa mezőben adja meg az alkalmazáshoz az Azure ad-ben generált **kulcsot** .

8. Nem kötelező – egyéni jogcímeket hozhat létre és használhat a Microsoft Graph API-val. Ha ezt választja, válassza a **speciális** lehetőséget, és törölje a **kérelem profilját** , és **frissítse a felhasználói attribútumok** beállításait. További információ az egyéni jogcímek használatáról: [egyéni jogcím használata](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

9. Válassza a **Mentés** lehetőséget

#### <a name="step-3--create-identity-mapping"></a>3. lépés – identitás-hozzárendelés létrehozása

>[!NOTE]
>Az identitás-hozzárendelés egynél több alkalmazással is használható, ha egynél több alkalmazás is ugyanazt az adathalmazt várja a fejlécben.

Az identitás-hozzárendelés létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a **Beállítások**  >  **hozzáférési**  >  **identitásának megfeleltetéseit**

2. Válassza az **identitás-hozzárendelés hozzáadása** lehetőséget

3. **Név** megadása

4. Válassza ki a fejléc- **identitás megfeleltetésének** identitás-hozzárendelési típusát

5. Az **attribútum-leképezési** táblában határozza meg a szükséges leképezéseket. Példa:

   Attribútum neve | Fejléc neve |
   |-------|--------|
   |UPN | x – userprinciplename |
   |e-mail   |    x – e-mail  |
   |OID   | x-OID  |
   |SCP   |     x hatókör |
   |AMR    |    x – AMR    |

6. Válassza a **Mentés** lehetőséget

#### <a name="step-4--create-a-site"></a>4. lépés – hely létrehozása

>[!NOTE]
>Bizonyos konfigurációk esetében előfordulhat, hogy a helyek több alkalmazást is tartalmazhatnak. Egy hely több alkalmazással is használható, ahol szükséges.

Egy hely létrehozásához kövesse az alábbi lépéseket:

1. Ugrás a **fő**  >  **webhelyekre**

2. Válassza a **hely hozzáadása** lehetőséget

3. Adja meg a hely **nevét**

4. Adja meg a hely **célját**. A cél az a állomásnév: Port pár, amely az alkalmazást üzemeltető kiszolgáló. Ne adja meg az alkalmazás elérési útját ebben a mezőben. Egy alkalmazás például a következő https://mysite:9999/AppName mysite lesz: 9999

5. Jelezze, hogy a cél biztonságos kapcsolatokat vár-e.

6. Ha a cél biztonságos kapcsolatokat vár, állítsa a megbízható tanúsítvány csoportot úgy, hogy az **megbízható** legyen.

7. Válassza a **Mentés** lehetőséget

#### <a name="step-5--create-an-application"></a>5. lépés – alkalmazás létrehozása

Az alábbi lépéseket követve hozzon létre egy alkalmazást a PingAccess-ben minden olyan Azure-alkalmazáshoz, amelyet szeretne védelemmel ellátni.

1. Ugrás a **fő**  >  **alkalmazásokra**

2. Válassza az **alkalmazás hozzáadása** lehetőséget.

3. Adja meg az alkalmazás **nevét**

4. Igény szerint megadhatja az alkalmazás **leírását** is

5. Határozza meg az alkalmazás **környezeti gyökerét** . Egy alkalmazás esetében például https://mysite:9999/AppName a/AppName. környezeti gyökere lesz A környezet gyökerének perjel (/) karakterrel kell kezdődnie, nem végződhet perjel (/) karakterrel, és lehet több réteg mélysége is, például/Apps/MyApp.

6. Válassza ki a létrehozott **virtuális gazdagépet** .

   >[!NOTE]
   >A virtuális gazdagép és a környezeti gyökér kombinációjának egyedinek kell lennie a PingAccess-ben.

7. Válassza ki a létrehozott **webes munkamenetet**

8. Válassza ki a létrehozott **helyet** , amely az alkalmazást tartalmazza

9. Válassza ki a létrehozott **identitás-leképezést**

10. Válassza az **engedélyezve** lehetőséget a hely mentéskor való engedélyezéséhez

11. Válassza a **Mentés** lehetőséget

### <a name="configure-the-pingfederate-authentication-policy"></a>A PingFederate hitelesítési szabályzatának konfigurálása

Konfigurálja a PingFederate hitelesítési házirendjét úgy, hogy az a Azure AD B2C bérlők által biztosított több IDP összevonása

1. Hozzon létre egy szerződést a IDP és az SP közötti attribútumok áthidalása érdekében. További információ: az [összevonási központ és a hitelesítési házirend-szerződések](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). Valószínűleg csak egy szerződést kell megadnia, hacsak az SP-nek eltérő attribútumokra van szüksége az egyes identitásszolgáltató.

2. Mindegyik identitásszolgáltató hozzon létre egy identitásszolgáltató-kapcsolatot a identitásszolgáltató és a PingFederate között az összevonási központ SP-ként.

3. A **cél munkamenet-leképezési** ablakban adja hozzá a megfelelő hitelesítési házirendi szerződéseket a identitásszolgáltató-kapcsolathoz.

4. A **választók** ablakban konfiguráljon egy hitelesítési választót. Tekintse meg például az **azonosító első adapterének** egy példányát, amely az egyes identitásszolgáltató a megfelelő identitásszolgáltató-kapcsolatban rendeli hozzá egy hitelesítési házirendben.

5. Hozzon létre egy SP-kapcsolatot a PingFederate, az összevonási központ és a identitásszolgáltató között, valamint az SP-t.

6. Adja hozzá a megfelelő hitelesítési házirendi szerződést az SP-kapcsolatban a **hitelesítési forrás leképezése** ablakban.

7. Az egyes identitásszolgáltató együttműködve kapcsolódjon az PingFederate-hez, az összevonási központ pedig SP-ként.

8. Működjön együtt az SP-vel a PingFederate, az összevonási központ identitásszolgáltató való kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
