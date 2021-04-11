---
title: TypingDNA Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálhatja Azure AD B2C hitelesítését a TypingDNA, hogy segítsen az identitások ellenőrzésében és a felhasználók begépelési mintáján alapuló ellenőrzésben. a a többtényezős hitelesítést kényszerítő azonosító-ellenőrzési megoldásokkal segíti a 2. fizetési szolgáltatások (PSD2) irányelvének SCA-követelményeinek való megfelelést.
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: afaac48d263082a17c7ba58996f4000366de0211
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256548"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Oktatóanyag a TypingDNA konfigurálásához a Azure Active Directory B2C

Ebből az útmutatóból megtudhatja, hogyan integrálhat egy minta online fizetési alkalmazást Azure Active Directory B2C a TypingDNA ALKALMAZÁSsal. A TypingDNA alkalmazás használatával Azure AD B2C [ügyfeleink a PSD2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) -tranzakció követelményeinek teljesítését a billentyűleütés-dinamika és az ügyfelek erős hitelesítése révén. További információt a TypingDNA [itt](https://www.typingdna.com/)talál.

 Azure AD B2C a TypingDNA technológiáit használja a felhasználók begépelési jellemzőinek rögzítéséhez, és az egyes hitelesítések ismeretének megírásához és elemzéséhez. Ez egy olyan védelmi réteget ad hozzá, amely a hitelesítés riskiness és a kockázati szintek kiértékelésével kapcsolatos. Azure AD B2C hivatkozhat más mechanizmusokra, hogy további bizalmat biztosítson a felhasználónak, aki az Azure AD MFA meghívásával, az e-mailek ellenőrzésének kényszerítésével, vagy bármely más, a forgatókönyvhöz tartozó egyéni logikával való használatra hivatkozik.

>[!NOTE]
> Ez a minta szabályzat a [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Packen alapul.

## <a name="scenario-description"></a>Forgatókönyv leírása

![Képernyőkép a TypingDNA architektúra diagramról](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Regisztráció

1. Azure AD B2C lapok a TypingDNA JavaScript-kódtár használatával rögzítik a felhasználó gépelési mintáját. Például a rendszer regisztrálja a felhasználónevet és a jelszót a kezdeti regisztrációnál, majd minden bejelentkezéskor az ellenőrzéshez.

2. Amikor a felhasználó elküldi a lapot, a TypingDNA-könyvtár kiszámítja a felhasználó begépelési jellemzőjét. Ezt követően szúrja be az adatokat egy olyan rejtett szövegmezőbe, amely Azure AD B2C renderelt. Ez a mező a CSS-sel van elrejtve.  

    A minta a JavaScript-és CSS-módosításokat [tartalmazó HTML-fájlokat tartalmaz](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) , és a `api.selfasserted.tdnasignin` és a `api.selfasserted.tdnasignup` tartalmi definíciók hivatkoznak rá. A HTML-fájlok tárolásához tekintse meg [a lap tartalmát](./customize-ui-with-html.md#hosting-the-page-content) .

3. Azure AD B2C most már a kérelem táskájában a begépelési minta szerepel, amikor a felhasználó elküldi a hitelesítő adatait. Meg kell hívnia egy API-t (a tiéd), hogy átadja ezeket az adatfájlokat a TypingDNA REST API-végpontnak. Ezt az API-t a [minta tartalmazza (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. A középső rétegbeli API ezután átadja a gépelési minta TypingDNA REST API. A regisztráció során a rendszer meghívja a felhasználói [végpontot](https://api.typingdna.com/index.html#api-API_Services-GetUser) , hogy erősítse meg, hogy a felhasználó nem létezett, majd a [mentési minta](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) végpontját a rendszer a felhasználó első gépelési mintájának mentéséhez hívja meg.

> [!NOTE]
> A TypingDNA REST API végpont összes hívása egy felhasználóazonosító-t küld. Ennek a kivonatos értéknek kell lennie. Azure AD B2C a `HashObjectIdWithEmail` jogcím-átalakítás használatával kivonattal az e-mailt egy véletlenszerű sóval és titkos kulccsal.  

Az REST API-hívások modellezése a következőkön `validationTechnicalProfiles` belül történik `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Bejelentkezés

A következő bejelentkezéskor a felhasználó gépelési mintáját ugyanúgy számítjuk ki, mint az [egyéni HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml)használatával történő regisztráció során. Miután a begépelési profil a Azure AD B2C jogcím-Táskán belül van, Azure AD B2C meghívja az API-t a TypingDNA REST API végpontjának meghívásához. A rendszer meghívja a [felhasználói](https://api.typingdna.com/index.html#api-API_Services-GetUser) végpontot, hogy erősítse meg a felhasználó létezését. Ezután [ellenőrizze a minta](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) végpontot, hogy visszaállítsa a következőt: `net_score` . Ez annak a jele, hogy a `net_score` Gépelési minta hogyan zárult az eredeti regisztrációnál.

Ez a gépelési minta a következőn belül van modellezve `validationTechnicalProfiles` `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Ha a felhasználó egy magas szintű gépelési mintát kap `net_score` , ezt a TypingDNA [Save gépelési minta](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) végpontjának használatával mentheti el.  

Az API-nak egy jogcímet kell visszaadnia  `saveTypingPattern` , ha szeretné, hogy a TypingDNA Save begépelési minta végpontját Azure ad B2C (az API-n keresztül) hívja meg.

A tárházban szereplő példa egy olyan API-t (TypingDNA-API-Interface) tartalmaz, amely a következő tulajdonságokkal van konfigurálva.

- Betanítási mód – ha a felhasználónál kevesebb mint két minta van elmentve, a rendszer mindig az MFA-t kéri.

- Ha a felhasználóhoz 2-5 minta van mentve, és a `net_score` értéke alacsonyabb, mint 50, a rendszer az MFA-t kéri.

- Ha a felhasználó 5 + mintázattal rendelkezik, és a `net_score` értéke kisebb, mint 65, kérje az MFA-t.

Ezeket a küszöbértékeket a használati esethez kell igazítani.

- Miután az API kiértékelte a `net_score` -t, egy logikai jogcímet kell visszaadnia a B2C-nek `promptMFA` .

- A `promptMFA` jogcím az Azure ad MFA feltételes végrehajtásához szükséges előfeltételen belül használatos.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>TypingDNA

1. Regisztráljon a TypingDNA [itt](https://www.typingdna.com/)
2. Jelentkezzen be a TypingDNA irányítópultra, és szerezze be az **API-kulcsot** és az **API-titkot**. Erre később szükség lesz az API-felület telepítéséhez

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>A TypingDNA integrálása Azure AD B2C

1. A [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) üzemeltetése az Ön által választott üzemeltetési szolgáltatónál
2. Cserélje le `apiKey` a és a `apiSecret` [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) megoldás összes példányát a TypingDNA irányítópultjának hitelesítő adataival
3. A CORS [vonatkozó követelményeket](./customize-ui-with-html.md#3-configure-cors) követve üzemeltetheti a saját SZOLGÁLTATÓjának HTML-fájljait.
4. Cserélje le a fájlban lévő és a tartalom-definíciók Tartalomdefinícióban elemeit `api.selfasserted.tdnasignup` `api.selfasserted.tdnasignin` `TrustFrameworkExtensions.xml` a tárolt HTML-fájlok URI-ra.
5. Hozzon létre egy B2C-szabályzatot a **Azure Portal** Azure ad paneljének identitási élmény keretrendszere területén. Használja a `Generate` kapcsolót, és nevezze el a kulcsot `tdnaHashedId` .
6. Cserélje le a TenantId a szabályzat fájljaiban.
7. Cserélje le a ServiceURLs az összes TypingDNA REST API technikai profilban (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) a [TypingDNA-API-Interface API-](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)hoz tartozó végponttal.
8. Töltse fel a [házirend-fájlokat](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) a bérlőbe.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a B2C-bérlőt, és válassza az identitási élmény keretrendszert.
2. Válassza ki a korábban létrehozott **felhasználói folyamatot**.
3. Felhasználói folyamat **futtatásának** kiválasztása

    a. **Alkalmazás** – válassza ki a regisztrált alkalmazást (minta: JWT)

    b. **Válasz URL-címe** – válassza ki az átirányítási URL-címet

    c. Válassza a **felhasználói folyamat futtatása** lehetőséget.
  
4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot
5. Kijelentkezés
6. Bejelentkezési folyamat átugrása
7. Az eredményül kapott JWT eredmény a TypingDNA eredményeit jeleníti meg

## <a name="live-version"></a>Élő verzió

• Az MFA le lett tiltva ebben a tesztelési verzióban, de a hitelesítés után megtekintheti, hogy az MFA-t a jogcím kéri-e `promptMFA` .

• [Regisztráljon itt, és](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) jelentkezzen be [ide](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni házirendek a AAD B2Cban](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal AAD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)