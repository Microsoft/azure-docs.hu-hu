---
title: Media Services v2 – v3 áttelepítési ellenőrzőlista
description: Ez a cikk egy ellenőrzőlista, amely segítséget nyújt az Azure Media Services v2-ről a v3-re való áttelepítéshez.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, áttelepítés, stream, Broadcast, Live, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: bd1488a1e89bb7d5c8a3a2dedda60bd5a226e02e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898256"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>3. lépés – áttelepítés beállítása a v3 REST API vagy az ügyféloldali SDK-ra

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-3.svg)

A következő lépésekben ismertetjük a környezet beállításához szükséges lépéseket a Media Services V3 API használatára.

## <a name="sdk-model"></a>SDK-modell

A v2 API-ban két különböző ügyféloldali SDK található, egyet a felügyeleti API számára, amely lehetővé tette a fiókok programozott létrehozását, valamint egy erőforrás-kezelést.

Korábban a fejlesztők egy Azure-beli egyszerű ügyfél-azonosítót és egy ügyfél-titkos kulcsot is feldolgozhatnak, valamint az AMS-fiókhoz tartozó v2 REST API végpontot.

A V3 API az Azure Resource Management (ARM) alapú. Az API-hoz való kapcsolódáshoz a Azure Active Directory (Azure AD) egyszerű szolgáltatás-azonosítóit és kulcsait használja. A fejlesztőknek az API-hoz való kapcsolódáshoz egyszerű szolgáltatásnevet vagy felügyelt identitásokat kell létrehozniuk. A V3 API-ban az API szabványos ARM-végpontokat használ, és egy hasonló és konzisztens modellt használ az összes többi Azure-szolgáltatáshoz.

Azok az ügyfelek, akik korábban az ARM Management API 2015-10-01-es verzióját használták a v2-fiókok felügyeletéhez, a V3 API-hozzáféréshez támogatott ARM felügyeleti API 2020-05-01-es verzióját kell használniuk.

## <a name="create-a-new-media-services-account-for-testing"></a>Új Media Services-fiók létrehozása teszteléshez

Kövesse a rövid útmutató lépéseit a [környezet beállításához](how-to-set-azure-subscription.md?tabs=portal) a Azure Portal használatával. Válassza az API-hozzáférés és az egyszerű szolgáltatás hitelesítése lehetőséget egy új Azure AD-alkalmazás AZONOSÍTÓjának és titkos kulcsainak létrehozásához a teszt fiókkal való használatra.

[Hozzon létre egy Media Services-fiókot](create-account-howto.md?tabs=portal).
[Hitelesítő adatok Beszerzése Media Services API-hoz való hozzáféréshez](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Az Ön által választott ügyféloldali SDK letöltése és a környezet beállítása

- A [.net](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true), a .net Core, a [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true), a [Python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true), a [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true), a [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)és a [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)esetében elérhető SDK-k.
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true)   integráció az egyszerű parancsfájlok támogatásához.

> [!NOTE]
> A közösségi PHP SDK már nem érhető el a (z) v3 Azure Media Serviceshoz. Ha a PHP-t használja a v2-ben, akkor közvetlenül a kódban kell áttérnie a REST APIra.

## <a name="open-api-specifications"></a>API-specifikációk megnyitása

- A v3 egy egységes API-felületen alapul, amely a Azure Resource Manager-ra épülő felügyeleti és üzemeltetési funkciókat egyaránt elérhetővé teszi. Azure Resource Manager sablonok használatával átalakításokat, folyamatos átviteli végpontokat, élő eseményeket és egyéb műveleteket hozhat létre és helyezhet üzembe.

- A [OpenAPI-specifikáció](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (korábbi nevén henceg) dokumentum ismerteti az összes szolgáltatás-összetevő sémáját.

- Az összes ügyfél SDK-t a GitHubon közzétett nyílt API-specifikáció alapján származtatjuk és generáljuk. A cikk közzétételének időpontjában a legújabb nyílt API-specifikációk nyilvánosan megmaradnak a GitHubban. Az 2020-05-01-es verzió a [legújabb stabil kiadás](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

A [poster](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) Media Services v3 REST API-hívásokhoz használható.
Olvassa el a [REST API-hivatkozási lapokat](https://docs.microsoft.com/rest/api/media/).

A Poster-gyűjteményben az 2020-05-01-es verzió sztringjét kell használnia.

## <a name="net"></a>[.NET](#tab/net)

Olvassa el a következő cikket: [Kapcsolódás a Media Services V3 API](configure-connect-dotnet-howto.md) -hoz a .net-tel a környezet beállításához.

Ha egyszerűen szeretné telepíteni a legújabb SDK-t a PackageManager használatával, használja a következő parancsot:

```Install-Package Microsoft.Azure.Management.Media```

Vagy telepítse a legújabb SDK-t a .NET CLI használatával a következő parancs használatával:

```dotnet add package Microsoft.Azure.Management.Media```

Emellett a teljes .NET-minták az [Azure-Samples/Media-Services-v3-DotNet](https://github.com/Azure-Samples/media-services-v3-dotnet) nyelven érhetők el különböző forgatókönyvek esetén. Az ebben a tárházban található projektek azt mutatják be, hogyan valósíthatók meg különböző Azure Media Services forgatókönyvek a v3 verzió használatával.

### <a name="get-started-adjusting-your-code"></a>A kód módosításának első lépései

A használati példányok esetében a kód alapján megkeresheti a `CloudMediaContext` frissítési folyamatot a V3 API-ra.

A következő kód azt mutatja be, hogy a v2 API-t korábban a v2 .NET SDK-val érik el. A fejlesztők az a létrehozásával kezdődnek, `CloudMediaContext` és létrehoznak egy példányt egy `AzureAdTokenCredentials` objektummal.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Olvassa el a következő cikket: [Kapcsolódás a Media Services V3 API](configure-connect-java-howto.md) -hoz Java használatával a környezet beállításához.

## <a name="python"></a>[Python](#tab/python)

Olvassa el a következő cikket: [kapcsolódjon a Azure Media Services V3 API-Pythonhoz](configure-connect-python-howto.md) a környezet beállításához.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

A környezet beállításához olvassa el a [Kapcsolódás a Azure Media Services V3 API-Node.jshoz ](configure-connect-nodejs-howto.md) című cikket.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Szerezze be a [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK-t a githubon.

## <a name="go"></a>[Ugrás](#tab/go)

Töltse le a [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK-t.

---

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]