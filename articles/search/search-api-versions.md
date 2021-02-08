---
title: API-verziók
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search REST API-khoz és a .NET SDK-hoz készült ügyféloldali kódtár verziójának szabályzata.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 489c6be7bf977d01207fee4d1bd1fda13dc12025
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820517"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-verziók az Azure Cognitive Search

Az Azure Cognitive Search rendszeresen kibővíti a szolgáltatás frissítéseit. Néha, de nem mindig, ezeknek a frissítéseknek az API új verziójára van szükségük a visszamenőleges kompatibilitás megőrzése érdekében. Az új verzió közzétételével szabályozhatja, hogy mikor és hogyan integrálja a keresési szolgáltatás frissítéseit a kódban.

Szabályként az Azure Cognitive Search csapata csak szükség esetén tesz közzé új verziókat, mert némi erőfeszítést okozhat a kód új API-verzió használatára való frissítéséhez. Az új verzióra csak akkor van szükség, ha az API valamilyen aspektusa úgy módosult, hogy a visszafelé való kompatibilitást megszakítja. Ezek a változások a meglévő szolgáltatások javításai, vagy a meglévő API-felületet módosító új szolgáltatások miatt fordulnak elő.

Ugyanez a szabály vonatkozik az SDK frissítéseire is. Az Azure Cognitive Search SDK a [szemantikai verziószámozási](https://semver.org/) szabályokat követi, ami azt jelenti, hogy a verziója három részből áll: a fő, a másodlagos és a kiépítési számokból (például 1.1.0). Az SDK új főverziójának kiadása csak a visszamenőleges kompatibilitást megszakító változások esetében engedélyezett. A nem feltörhető szolgáltatások frissítései megnövelik a másodlagos verziót, és a hibajavítások csak a Build verzióját fogják növelni.

> [!Important]
> A .NET, a Java, a Python és a JavaScript rendszerhez készült Azure SDK-k új ügyféloldali kódtárakat vezetnek be az Azure Cognitive Searchhoz. Jelenleg az Azure SDK-kódtárak egyike sem támogatja teljes mértékben a legújabb keresési REST API-kat (2020-06-30) vagy a felügyeleti REST API-kat (2020-03-13), de ez idővel változni fog. Rendszeresen megtekintheti ezt az oldalt vagy a funkciók [újdonságait](whats-new.md) a funkcionális fejlesztésekben.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Nem támogatott verziók

Frissítse a meglévő keresési megoldásokat a REST API legújabb verziójára a 2020. október 15-én. Ekkor az Azure Cognitive Search REST API következő verziói megszűnnek, és már nem támogatottak:

+ **2015-02-28**
+ **2015-02-28 – előzetes verzió**
+ **2014-07-31 – előzetes verzió**
+ **2014-10-20 – előzetes verzió**

Emellett az Azure Cognitive Search .NET SDK-nál régebbi, az [**3.0.0-RC**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) verziónál régebbi verziók is megszűnnek, mivel ezek a REST API-verziók egyikét célozzák meg. 

Ezen időpont után az elavult REST API vagy SDK-verziót használó alkalmazások már nem fognak működni, és frissíteni kell őket. Ahogy az ilyen típusú változások esetében is, 12 hónapos felmondást adunk, így Önnek megfelelő idő van a módosításra.

Ha továbbra is szeretné használni [REST API](search-api-migration.md) az Azure Cognitive Search-t, telepítse át a meglévő kódot, amely a 2020-06-30-es vagy újabb SDK-ra vonatkozik, 2020. október 15-én [REST API](/rest/api/searchservice/) .  Ha bármilyen kérdése van a legújabb verzióra való frissítéssel kapcsolatban, küldjön e-mailt azuresearch_contact@microsoft.com május 15-én, 2020-ig, hogy elegendő idő legyen a kód frissítésére.

## <a name="rest-apis"></a>REST API-k

Az Azure Cognitive Search Service-példányok számos REST API verziót támogatnak, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára [telepítse át a kódot](search-api-migration.md) . A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

A következő táblázat a Search Service REST API aktuális és korábban kiadott verzióinak korábbi verzióit tartalmazza. A dokumentációt csak a legfrissebb stabil és előzetes verziókra teszi közzé.

### <a name="search-service-apis"></a>Search Service API-k

Tartalom létrehozása és kezelése keresési szolgáltatásban.

| Verziója&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Állapot | Leírás |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stable | A Search REST API-k legújabb stabil kiadása, a relevancia pontozásával és általánosan elérhető a Knowledge Store szolgáltatással.|
| [Search 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Előnézet | A stabil verzióhoz társított előzetes verzió. Több [előzetes verziójú funkciót](search-api-preview.md)is tartalmaz. |
| Search 2019-05-06 | Stable  | [Összetett típusok](search-howto-complex-data-types.md)hozzáadására szolgál. |
| Search 2019-05-06 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2017-11-11 | Stable | Szakértelmével és [AI-gazdagítás](cognitive-search-concept-intro.md)hozzáadására szolgál. |
| Keresés 2017-11-11 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2016-09-01 |Stable | [Indexelő](search-indexer-overview.md)hozzáadására szolgál. |
| Keresés 2016-09-01 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió.|
| Keresés 2015-02-28 | 10-10-2020 után nem támogatott   | Az első általánosan elérhető kiadás.  |
| Keresés 2015-02-28 – előzetes verzió | 10-10-2020 után nem támogatott  | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2014-10-20 – előzetes verzió | 10-10-2020 után nem támogatott | Második nyilvános előzetes verzió. |
| Keresés 2014-07-31 – előzetes verzió | 10-10-2020 után nem támogatott  | Első nyilvános előzetes verzió. |

### <a name="management-rest-apis"></a>Felügyeleti REST API-k

Keresési szolgáltatás létrehozása és konfigurálása, valamint az API-kulcsok kezelése.

| Verziója&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Állapot | Leírás |
|-------------------------|--------|------------------------------|
| [Felügyelet 2020-08-01](/rest/api/searchmanagement/) | Stable | A felügyeleti REST API-k legújabb stabil kiadása. Általánosan elérhető megosztott privát kapcsolati erőforrás-támogatást biztosít minden kimenő hozzáférésű erőforráshoz, kivéve az előzetes verzióban feljegyzett |
| [Felügyelet 2020-08-01 – előzetes verzió](/rest/api/searchmanagement/index-preview) | Előnézet  | Jelenleg előzetes verzióban érhető el: a megosztott magánhálózati erőforrás-támogatás Azure Functions és Azure Database for MySQL. |
| Management 2020-03-13  | Stable | Privát [végpontot](service-create-private-endpoint.md) biztosít a privát kapcsolaton keresztül, valamint az új szolgáltatásokra vonatkozó [hálózati IP-szabályokat](service-configure-firewall.md) . További információkért tekintse meg ezt a [hencegő specifikációt](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01). |
| Management 2019-10-01-Preview | Előnézet  | A listában nem szerepelnek előzetes verziójú funkciók. Ez az előzetes verzió a 2020-03-13-es működéssel egyenértékű. További információkért tekintse meg ezt a [hencegő specifikációt](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview). |
| Felügyelet 2015-08-19  | Stable | A felügyeleti REST API-k első általánosan elérhető verziója. Biztosítja a szolgáltatás üzembe helyezését, a vertikális felskálázást és az API-kulcsok kezelését. További információkért tekintse meg ezt a [hencegő specifikációt](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |
| Felügyelet 2015-08-19 – előzetes verzió  | Előnézet | A felügyeleti REST API-k első előzetes verziója. További információkért tekintse meg ezt a [hencegő specifikációt](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

Az alábbi táblázat a újabb SDK-verziókra mutató hivatkozásokat tartalmaz. 

| SDK verziója | Állapot | Leírás |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2020-06-30 REST API célozza meg, de még nem biztosít natív támogatást a földrajzi szűrőkhöz. A földrajzi műveletekhez a [Microsoft. térbeli](https://www.nuget.org/packages/Microsoft.Spatial/) csomag használatát javasoljuk. Ilyenek például a [System.Text.Js](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) és a [Newtonsoft.Js](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md). |
| [Microsoft. Azure. Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Kiadás dátuma: 2019. A keresési REST API-Version = 2019-05-06 célokat célozza meg.|
| [Microsoft. Azure. Management. Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Célozza meg a felügyeleti REST API-verziót = 2020-08-01.  |
| Microsoft. Azure. Management. Search 3.0.0 | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg.  |

## <a name="azure-sdk-for-java"></a>Javához készült Azure SDK

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [Java Azure – keresés – 11. dokumentum](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2019-05-06 célokat célozza meg. |
| [Java Management-ügyfél 1.35.0](/java/api/overview/azure/search/management) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK for JavaScript

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [JavaScript Azure – keresés 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2016-09-01-et célozza meg. |
| [JavaScript Azure-ARM-Search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |

## <a name="azure-sdk-for-python"></a>Azure SDK for Python

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [Python Azure – keresés – dokumentumok 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2019-05-06 célokat célozza meg. |
| [Python Azure-mgmt-Search 1,0](/python/api/overview/azure/search) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |