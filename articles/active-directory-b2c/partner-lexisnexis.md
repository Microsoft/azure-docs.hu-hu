---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a LexisNexis használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható Azure AD B2C hitelesítés a LexisNexis-vel, amely egy profilkészítési és identitás-ellenőrzési szolgáltatás, és a felhasználók azonosítására szolgál, és átfogó kockázatértékelést biztosít a felhasználó eszköze alapján.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 362704368958d2600d16cd907421917b824a59cb
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256599"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Oktatóanyag a LexisNexis konfigurálásához a Azure Active Directory B2C

Ebben a példában a Azure AD B2C és a [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)integrálását ismertető útmutató nyújt útmutatást. A LexisNexis számos megoldást kínál, [itt](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)megtalálja őket. Ebben a minta oktatóanyagban a LexisNexis **ThreatMetrix** -megoldását mutatjuk be. A ThreatMetrix egy profilkészítési és identitás-ellenőrzési szolgáltatás. Ez a felhasználó azonosítására szolgál, és átfogó kockázatértékelést biztosít a felhasználó eszköze alapján.

Ez az integráció a felhasználói adatok néhány felhasználói adatán alapul, amelyet a felhasználó a regisztrációs folyamat során biztosít. A ThreatMetrix határozza meg, hogy a felhasználó számára engedélyezett-e a bejelentkezés vagy sem. A ThreatMetrix a következő attribútumokat veszi figyelembe:

- E-mail
- Telefonszám
- A felhasználó gépén gyűjtött információk profilkészítése

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Egy Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

## <a name="scenario-description"></a>Forgatókönyv leírása

A ThreatMetrix-integráció a következő összetevőket tartalmazza:

- Azure AD B2C – az engedélyezési kiszolgáló, amely a felhasználó hitelesítő adatainak (más néven identitás-szolgáltató) ellenőrzéséhez felelős.

- ThreatMetrix – a ThreatMetrix szolgáltatás a felhasználó által megadott bemeneteket veszi igénybe, és a felhasználó számítógépéről gyűjtött profilkészítési információkkal kombinálja a felhasználói beavatkozások biztonságának ellenőrzéséhez.

- Egyéni REST API – ez az API implementálja Azure AD B2C és a ThreatMetrix szolgáltatás közötti integrációt.

A következő architektúra-diagram a megvalósítást mutatja be.

![képernyőkép a LexisNexis-Architecture-diagramról](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Lépés | Description |
|:--------------|:-------------|
|1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználó a regisztráció gombra kattintva új fiókot hozhat létre, és beírhatja az adatokat az oldalra. A Azure AD B2C a felhasználói attribútumokat gyűjti.
| 2. | Azure AD B2C meghívja a középső rétegbeli API-t, és átadja a felhasználói attribútumokat.
| 3. | A középső rétegbeli API gyűjti a felhasználói attribútumokat, és átalakítja a LexisNexis API-t használó formátumba. Ezt követően küldje el a LexisNexis.  
| 4. | A LexisNexis felhasználja az adatokat, és feldolgozza azokat a kockázatelemzés alapján a felhasználók azonosításának ellenőrzéséhez. Ezután visszaadja az eredményt a középső rétegbeli API-nak.
| 5. | A középső rétegbeli API feldolgozza az adatokat, és visszaküldi a kapcsolódó információkat a Azure AD B2Cba.
| 6. | Azure AD B2C adatokat fogad a középső rétegbeli API-ból. Ha hibát jelez, hibaüzenet jelenik meg a felhasználó számára. Ha sikeres választ mutat, a felhasználó hitelesítve van, és hozzáférést kap.

## <a name="onboard-with-lexisnexis"></a>LexisNexis

1. LexisNexis-fiók létrehozásához forduljon a [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. Hozzon létre egy LexisNexis szabályzatot, amely megfelel a követelményeinek. Használja az [itt](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)elérhető dokumentációt.

>[!NOTE]
> A szabályzat nevét később fogjuk használni.

A fiók létrehozása után megkapja az API-konfigurációhoz szükséges információkat. A következő szakaszok ismertetik a folyamatot.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Azure AD B2C konfigurálása a LexisNexis

### <a name="part-1---deploy-the-api"></a>1. rész – az API üzembe helyezése

A megadott [API-kód](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) üzembe helyezése egy Azure-szolgáltatásban. A kód a Visual studióból is közzétehető, ezeket az [utasításokat](/visualstudio/deployment/quickstart-deploy-to-azure)követve.

>[!NOTE]
>Szüksége lesz az üzembe helyezett szolgáltatás URL-címére az Azure AD konfigurálásához a szükséges beállításokkal.

### <a name="part-2---configure-the-api"></a>2. rész – az API konfigurálása

[Az Alkalmazásbeállítások konfigurálhatók az Azure app Service-ben](../app-service/configure-common.md#configure-app-settings).  Ezzel a módszerrel a beállítások biztonságosan konfigurálhatók egy adattárba való ellenőrzés nélkül. A REST API-hoz a következő beállításokat kell megadnia:

| Alkalmazásbeállítások | Forrás | Jegyzetek |
| :-------- | :------------| :-----------|
|ThreatMetrix: URL | ThreatMetrix-fiók konfigurálása |     |
|ThreatMetrix: OrgId | ThreatMetrix-fiók konfigurálása |     |
|ThreatMetrix:ApiKey |ThreatMetrix-fiók konfigurálása|  |
|ThreatMetrix: szabályzat | A ThreatMetrix-ben létrehozott házirend neve | |
| Alapfokú hitelesítés: ApiUsername |Adja meg az API-hoz tartozó felhasználónevet| A felhasználónevet a rendszer a Azure AD B2C konfigurációjában fogja használni
| Alapfokú hitelesítés: ApiPassword | Adja meg az API-hoz tartozó jelszót | A jelszót a rendszer a Azure AD B2C konfigurációjában fogja használni

### <a name="part-3---deploy-the-ui"></a>3. rész – a felhasználói felület üzembe helyezése

Ez a megoldás a Azure AD B2C által betöltött egyéni FELHASZNÁLÓIFELÜLET-sablonokat használja. Ezek a felhasználói felületi sablonok a profilkészítést közvetlenül a ThreatMetrix szolgáltatásnak küldik el.

Tekintse át ezeket az [utasításokat](./customize-ui-with-html.md#custom-page-content-walkthrough) a mellékelt [felhasználói felületi fájlok](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) blob Storage-fiókba történő telepítéséhez. Az utasítások közé tartozik a blob Storage-fiók beállítása, a CORS konfigurálása és a nyilvános hozzáférés engedélyezése.

A felhasználói felület az [Ocean Blue sablonon](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)alapul. A felhasználói felületen lévő összes hivatkozást frissíteni kell, hogy az üzembe helyezett helyre hivatkozzon. A felhasználói felület mappában keresse meg és cserélje le https://yourblobstorage/blobcontainer az üzembe helyezett helyet.

### <a name="part-4---create-api-policy-keys"></a>4. rész – API-szabályzatok kulcsainak létrehozása

Tekintse meg ezt a [dokumentumot](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) , és hozzon létre két házirend-kulcsot – egyet az API-felhasználónévhez, egyet pedig a fent megadott API-jelszóhoz.

A minta szabályzat ezeket a neveket használja:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>5. rész – az API URL-címének frissítése

A megadott [TrustFrameworkExtensions-szabályzatban](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)keresse meg a nevű technikai profilt `Rest-LexisNexus-SessionQuery` , és frissítse a `ServiceUrl` metaadatokat a fent üzembe helyezett API helyére.

### <a name="part-6---update-ui-url"></a>6. rész – felhasználói felület URL-címének frissítése

A megadott [TrustFrameworkExtensions-szabályzatban](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)keresse meg és cserélje le, hogy megkeresse azt https://yourblobstorage/blobcontainer/ a helyet, ahol a felhasználói felület fájljai telepítve vannak.

>[!NOTE]
> Ajánlott eljárásként Azt javasoljuk, hogy az ügyfelek az attribútumok gyűjteménye lapon vegyenek fel beleegyező értesítéseket. Értesítse a felhasználókat arról, hogy az adatok a harmadik féltől származó szolgáltatásoknak személyazonosság-ellenőrzés céljából lesznek elküldve.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>7. rész – a Azure AD B2C házirend konfigurálása

Tekintse meg ezt a [dokumentumot](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) a [helyi fiókok kezdő csomagjának](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) letöltéséhez, és konfigurálja a Azure ad B2C bérlő [házirendjét](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) .

>[!NOTE]
>Frissítse a megadott szabályzatokat az adott bérlőhöz kapcsolódóan.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza a **felhasználói folyamatok** lehetőséget.

2. Válassza ki a korábban létrehozott **felhasználói folyamatot**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás**: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. **Válasz URL-címe**: válassza ki az **átirányítási URL-címet**

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. Kijelentkezés

6. Bejelentkezési folyamat átugrása  

7. A **folytatáshoz** a ThreatMetrix-puzzle jelenik meg.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
