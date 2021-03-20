---
title: Azure Active Directory használata a Batch-felügyeleti megoldások hitelesítéséhez
description: Ismerkedjen meg a Azure Active Directory használatával a Batch Management .NET függvénytárat használó alkalmazásokból való hitelesítéshez.
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: ff49d5e88df7c56ed4dee0685f09e45eb372aa5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930213"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Batch-felügyeleti megoldások hitelesítése Active Directory

Azok az alkalmazások, amelyek meghívják a Azure Batch felügyeleti szolgáltatást [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) hitelesítéssel. Az Azure AD a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. Az Azure maga az Azure AD-t használja az ügyfelek, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítéséhez.

A Batch Management .NET könyvtár a Batch-fiókokkal, a fiók kulcsaival, az alkalmazásokkal és az alkalmazásokkal kapcsolatos műveletek típusait teszi elérhetővé. A Batch Management .NET-függvénytár egy Azure erőforrás-szolgáltatói ügyfél, és a [Azure Resource Manager](../azure-resource-manager/management/overview.md) együtt használható az erőforrások programozott kezeléséhez. Az Azure AD-nek szüksége van az Azure erőforrás-szolgáltatói ügyfélen keresztül küldött kérések hitelesítésére, beleértve a Batch Management .NET könyvtárat és a Azure Resource Manager.

Ebben a cikkben azt vizsgáljuk, hogy az Azure AD használatával kell hitelesíteni a Batch Management .NET könyvtárat használó alkalmazásokkal. Bemutatjuk, hogyan lehet az Azure AD-t az előfizetés-adminisztrátor vagy a közös rendszergazda hitelesítésére használni integrált hitelesítés használatával. A GitHubon elérhető [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) -minta projektet használjuk az Azure ad és a Batch Management .net könyvtár használatával történő átjárásra.

Ha többet szeretne megtudni a Batch Management .NET-könyvtár és a AccountManagement-minta használatáról, tekintse meg [a Batch-fiókok és kvóták kezelése a .net-hez készült batch Management ügyféloldali kódtár](batch-management-dotnet.md)használatával című témakört.

## <a name="register-your-application-with-azure-ad"></a>Az alkalmazás regisztrálása az Azure AD-ben

A [Azure Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) egy programozott felületet biztosít az Azure ad számára az alkalmazásokon belüli használatra. Az ADAL az alkalmazásból való meghívásához regisztrálnia kell az alkalmazást egy Azure AD-bérlőben. Az alkalmazás regisztrálásakor az Azure AD-t az alkalmazással kapcsolatos információkkal látja el, beleértve az Azure AD-bérlőn belüli nevet is. Az Azure AD ezt követően egy alkalmazás-azonosítót biztosít, amelyet az alkalmazás az Azure AD-vel való hozzárendeléséhez használ futásidőben. Az alkalmazás-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md).

A AccountManagement-minta alkalmazás regisztrálásához kövesse az alkalmazások [hozzáadása](../active-directory/develop/quickstart-register-app.md) című szakasz lépéseit az [alkalmazások integrálása a Azure Active Directorysal](../active-directory/develop/quickstart-register-app.md)című témakörben. **Natív ügyfélalkalmazás** megadása az alkalmazás típusához. Az iparági szabványnak megfelelő OAuth 2,0 URI az **átirányítási URI** -hoz `urn:ietf:wg:oauth:2.0:oob` . Megadhat azonban bármilyen érvényes URI-t (például `http://myaccountmanagementsample` ) az **átirányítási URI** számára, mivel nem kell valódi végpontnak lennie.

![Alkalmazás hozzáadása](./media/batch-aad-auth-management/app-registration-management-plane.png)

Miután elvégezte a regisztrációs folyamatot, megjelenik az alkalmazás azonosítója és az objektum (egyszerű szolgáltatásnév) azonosítója.

![Befejezett regisztrációs folyamat](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>A Azure Resource Manager API-hozzáférés biztosítása az alkalmazáshoz

Ezután delegálnia kell az alkalmazáshoz való hozzáférést a Azure Resource Manager API-nak. A Resource Manager API-hoz készült Azure AD-azonosító a **Windows Azure Service Management API**.

Hajtsa végre a következő lépéseket az Azure Portalon:

1. A Azure Portal bal oldali navigációs paneljén válassza a **minden szolgáltatás** elemet, kattintson az **alkalmazás-regisztrációk** elemre, majd a **Hozzáadás** gombra.
2. Keresse meg az alkalmazás nevét az alkalmazások regisztrációinak listájában:

    ![Az alkalmazás nevének megkeresése](./media/batch-aad-auth-management/search-app-registration.png)

3. A **Beállítások** panel megjelenítése Az **API-hozzáférés** szakaszban válassza a **szükséges engedélyek** lehetőséget.
4. Új szükséges engedély hozzáadásához kattintson a **Hozzáadás** gombra.
5. Az 1. lépésben írja be a **Windows Azure Service Management APIt**, válassza ki az API-t az eredmények listájából, majd kattintson a **kiválasztás** gombra.
6. A 2. lépésben jelölje be a **klasszikus Azure-beli üzembe helyezési modell a szervezeti felhasználókként való elérésének** jelölőnégyzetét, majd kattintson a **kiválasztás** gombra.
7. Kattintson a **kész** gombra.

A **szükséges engedélyek** panelen látható, hogy az alkalmazáshoz tartozó engedélyek a ADAL és a Resource Manager API-k számára is érvényesek. Alapértelmezés szerint az Azure AD-ben való regisztráláskor az engedélyek a ADAL számára lesznek elérhetők.

![Engedélyek delegálása a Azure Resource Manager API-nak](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-végpontok

A Batch-felügyeleti megoldások Azure AD-vel történő hitelesítéséhez két jól ismert végpontra lesz szüksége.

- Az **Azure ad Common Endpoint** egy általános hitelesítőadat-összegyűjtési felületet biztosít, ha nincs megadva egy adott bérlő, mint az integrált hitelesítés esetében:

    `https://login.microsoftonline.com/common`

- A **Azure Resource Manager végpont** a kérelmeknek a Batch Management szolgáltatásba való hitelesítéséhez használt jogkivonat beszerzésére szolgál:

    `https://management.core.windows.net/`

A AccountManagement-minta alkalmazás állandókat határoz meg ezekhez a végpontokhoz. Hagyja változatlanul a következő állandókat:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Az alkalmazás AZONOSÍTÓjának hivatkozása

Az ügyfélalkalmazás az alkalmazás AZONOSÍTÓját (más néven ügyfél-azonosítót) használja az Azure AD eléréséhez futásidőben. Miután regisztrálta az alkalmazást a Azure Portalban, frissítse a kódját, hogy az Azure AD által megadott alkalmazás-azonosítót használja a regisztrált alkalmazáshoz. A AccountManagement minta alkalmazásban másolja az alkalmazás AZONOSÍTÓját a Azure Portal a megfelelő konstansba:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Másolja a regisztrációs folyamat során megadott átirányítási URI-t is. A kódban megadott átirányítási URI-nek meg kell egyeznie az alkalmazás regisztrálásakor megadott átirányítási URI-val.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Miután regisztrálta az AccountManagement mintát az Azure AD-bérlőben, és frissíti a minta forráskódját az értékekkel, a minta készen áll a hitelesítésre az Azure AD használatával. A minta futtatásakor a ADAL hitelesítési jogkivonat beszerzését kísérli meg. Ebben a lépésben a Microsoft hitelesítő adatait kéri:

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

A hitelesítő adatok megadása után a minta alkalmazás folytathatja a hitelesített kérések kiküldését a Batch Management szolgáltatásba.

## <a name="next-steps"></a>Következő lépések

- A [AccountManagement-minta alkalmazás](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement)futtatásával kapcsolatos további információkért lásd: [Batch-fiókok és kvóták kezelése a .net-hez készült batch Management ügyféloldali kódtár használatával](batch-management-dotnet.md).
- Az Azure AD-vel kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory dokumentációját](../active-directory/index.yml).
- A ADAL használatát bemutató részletes példák az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=active-directory) Library-ben érhetők el.
- A Batch szolgáltatásbeli alkalmazások Azure AD-vel történő hitelesítéséhez tekintse meg [a Batch szolgáltatással kapcsolatos megoldások hitelesítése Active Directory](batch-aad-auth.md)használatával című témakört.
