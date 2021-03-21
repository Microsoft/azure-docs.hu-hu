---
title: Az Azure AD-hitelesítés használata az Azure Media Services API .NET-tel való eléréséhez | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható a Azure Active Directory (Azure AD) hitelesítés a Azure Media Services (AMS) API .NET-tel való eléréséhez.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013992"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Az Azure AD-hitelesítés használata Azure Media Services API .NET-tel való eléréséhez

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A windowsazure. Mediaservices 4.0.0.4 kezdve a Azure Media Services támogatja a Azure Active Directory (Azure AD) alapján történő hitelesítést. Ebből a témakörből megtudhatja, hogyan használhatja az Azure AD-hitelesítést a Azure Media Services API Microsoft .NET-hoz való eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Részletekért lásd: az [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/).
- Egy Media Services-fiók. További információ: [Azure Media Services fiók létrehozása a Azure Portal használatával](media-services-portal-create-account.md).
- A legújabb [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) -csomag.
- A [Azure Media Services API Azure ad-hitelesítéssel történő elérésének áttekintése](media-services-use-aad-auth-to-access-ams-api.md)című témakör ismerete.

Az Azure AD-hitelesítés Azure Media Services használatával történő használatakor kétféleképpen végezheti el a hitelesítést:

- A **felhasználói hitelesítés** az alkalmazást használó személyt hitelesíti Azure Media Services erőforrásokkal való kommunikációhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a hitelesítő adatok megadására. Ilyen például az a felügyeleti konzol alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő adatfolyamok figyelésére használnak.
- A **szolgáltatás egyszerű hitelesítése** hitelesíti a szolgáltatást. Azok az alkalmazások, amelyek gyakran használják ezt a hitelesítési módszert a Daemon Services, a közepes szintű szolgáltatások vagy az ütemezett feladatok, például a Web Apps, a Function apps, a Logic apps, az API-k vagy a Service-alkalmazások futtatásához.

>[!IMPORTANT]
>Az Azure Media Service jelenleg egy Azure Access Control Service hitelesítési modellt támogat. A Access Control engedélyezése azonban a 2018-es június 22-én lesz elavult. Javasoljuk, hogy a lehető leghamarabb telepítse át Azure Active Directory hitelesítési modellre.

## <a name="get-an-azure-ad-access-token"></a>Azure AD hozzáférési token beszerzése

Ha az Azure AD-hitelesítéssel szeretne csatlakozni a Azure Media Services API-hoz, az ügyfélalkalmazás Azure AD hozzáférési jogkivonatot kell kérnie. Ha a Media Services .NET Client SDK-t használja, az Azure AD hozzáférési jogkivonatok beszerzésének számos részlete a [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) és a [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) osztályban van becsomagolva és egyszerűsítve.

Például nem kell megadnia az Azure AD-szolgáltatót, Media Services erőforrás URI-JÁT vagy a natív Azure AD-alkalmazás részleteit. Ezek olyan jól ismert értékek, amelyeket az Azure AD hozzáférési jogkivonat-szolgáltatói osztálya már konfigurált.

Ha nem az Azure Media Service .NET SDK-t használja, javasoljuk, hogy használja az [Azure ad hitelesítési függvénytárat](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). Az Azure AD hitelesítési függvénytárhoz használni kívánt paraméterek értékeinek lekéréséhez lásd: [a Azure Portal használata az Azure ad-hitelesítési beállítások eléréséhez](media-services-portal-get-started-with-aad.md).

Lehetősége van arra is, hogy lecserélje a **AzureAdTokenProvider** alapértelmezett implementációját a saját megvalósítására.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Azure Media Services .NET SDK telepítése és konfigurálása

>[!NOTE]
>Ha az Azure AD-hitelesítést a Media Services .NET SDK-val szeretné használni, rendelkeznie kell a legújabb [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) -csomaggal. Továbbá adjon hozzá egy hivatkozást a **Microsoft. IdentityModel. clients. ActiveDirectory** szerelvényhez. Ha meglévő alkalmazást használ, foglalja bele a **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** szerelvényt.

1. Hozzon létre egy új C# konzolos alkalmazást a Visual Studióban.
2. **Azure Media Services .net SDK** telepítéséhez használja a [windowsazure. Mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet-csomagot.

    Ha a NuGet használatával szeretne hivatkozásokat felvenni, hajtsa végre a következő lépéseket: **megoldáskezelő** kattintson a jobb gombbal a projekt nevére, majd válassza a **NuGet-csomagok kezelése** lehetőséget. Ezután keressen rá a **windowsazure. Mediaservices** kifejezésre, és válassza a **telepítés** lehetőséget.

    -vagy-

    Futtassa a következő parancsot a **Package Manager konzolon** a Visual Studióban.

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. Adja hozzá a **használatát** a forráskódhoz.

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>Felhasználói hitelesítés használata

Ha az Azure Media Service API-hoz szeretne csatlakozni a felhasználói hitelesítés beállítással, az ügyfélalkalmazás a következő paraméterekkel kell igényelnie az Azure AD-jogkivonatot:

- Azure AD-bérlői végpont. A bérlő adatai a Azure Portalból kérhetők le. Vigye a kurzort a bejelentkezett felhasználó fölé a jobb felső sarokban.
- Media Services erőforrás URI-ja.
- Media Services (natív) alkalmazás ügyfél-azonosítója.
- Media Services (natív) alkalmazás átirányítási URI-ja.

A paraméterek értékei a következő címen találhatók: **AzureEnvironments. AzureCloudEnvironment**. A **AzureEnvironments. AzureCloudEnvironment** állandó a .net SDK egyik segítője, amely egy nyilvános Azure-adatközpont megfelelő környezeti változójának beállítására szolgál.

Előre definiált környezeti beállításokat tartalmaz, amelyek csak a nyilvános adatközpontokban lévő Media Services elérésére használhatók. Szuverén vagy kormányzati Felhőbeli régiókban a **AzureChinaCloudEnvironment**, a **AzureUsGovernmentEnvironment** és a **AzureGermanCloudEnvironment** is használható.

A következő kódrészlet létrehoz egy jogkivonatot:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

A Media Services programozásának megkezdéséhez létre kell hoznia egy **csatlakozáshoz szükséges cloudmediacontext** -példányt, amely a kiszolgáló környezetét jelöli. A **csatlakozáshoz szükséges cloudmediacontext** olyan fontos gyűjteményekre mutató hivatkozásokat tartalmaz, mint a feladatok, az eszközök, a fájlok, a hozzáférési házirendek és a lokátorok.

A **Media Rest-szolgáltatások erőforrás-URI-ját** is át kell adnia a **csatlakozáshoz szükséges cloudmediacontext** konstruktornak. A Media REST-szolgáltatások erőforrás-URI-ja beszerzéséhez jelentkezzen be a Azure Portalba, válassza ki a Azure Media Services fiókját, válassza az **API-hozzáférés** lehetőséget, majd válassza a **Kapcsolódás a Azure Media Serviceshoz felhasználói hitelesítéssel** lehetőséget.

A következő mintakód egy **csatlakozáshoz szükséges cloudmediacontext** -példányt hoz létre:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Az alábbi példa bemutatja, hogyan hozhatja létre az Azure AD-tokent és a környezetet:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }
        }
    }
}
```

>[!NOTE]
>Ha olyan kivételt kap, amely azt jelzi, hogy "a távoli kiszolgáló a következő hibát adta vissza: (401) nem engedélyezett", tekintse meg a [hozzáférés-vezérlési](media-services-use-aad-auth-to-access-ams-api.md#access-control) szakaszt a Azure Media Services API Azure ad-hitelesítéssel történő elérésének áttekintésével

## <a name="use-service-principal-authentication"></a>Egyszerű szolgáltatás hitelesítésének használata

Ha csatlakozni szeretne a Azure Media Services API-hoz az egyszerű szolgáltatásnév beállítással, a középső rétegbeli alkalmazásnak (webes API-nak vagy webalkalmazásnak) egy Azure AD-jogkivonatot kell igényelni a következő paraméterekkel:

- Azure AD-bérlői végpont. A bérlő adatai a Azure Portalból kérhetők le. Vigye a kurzort a bejelentkezett felhasználó fölé a jobb felső sarokban.
- Media Services erőforrás URI-ja.
- Azure AD-alkalmazás értékei: az **ügyfél-azonosító** és az **ügyfél titka**.

Az **ügyfél-azonosító** és az **ügyfél titkos** paramétereinek értékei a Azure Portal találhatók. További információ: [Az Azure ad-hitelesítés használatának első lépései a Azure Portal használatával](media-services-portal-get-started-with-aad.md).

A következő mintakód létrehoz egy jogkivonatot a **AzureAdTokenCredentials** konstruktor használatával, amely paraméterként a **AzureAdClientSymmetricKey** fogadja:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Megadhatja azt a **AzureAdTokenCredentials** konstruktort is, amely paraméterként fogadja a **AzureAdClientCertificate** .

A tanúsítványoknak az Azure AD által használható űrlapon való létrehozásával és konfigurálásával kapcsolatos utasításokért lásd: [hitelesítés az Azure ad-ben az Active Directory-alkalmazásokban, tanúsítványokkal – manuális konfigurációs lépések](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2).

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

A Media Services programozásának megkezdéséhez létre kell hoznia egy **csatlakozáshoz szükséges cloudmediacontext** -példányt, amely a kiszolgáló környezetét jelöli. A **Media Rest-szolgáltatások erőforrás-URI-ját** is át kell adnia a **csatlakozáshoz szükséges cloudmediacontext** konstruktornak. A **Media Rest Services** értékének erőforrás-URI-ja a Azure Portal is beszerezhető.

A következő mintakód egy **csatlakozáshoz szükséges cloudmediacontext** -példányt hoz létre:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Az alábbi példa bemutatja, hogyan hozhatja létre az Azure AD-tokent és a környezetet:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                        AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

Ismerkedés a [fájlok feltöltésével a fiókjába](media-services-dotnet-upload-files.md).
