---
title: Frissítés a Azure Search .NET Management SDK-ra
titleSuffix: Azure Cognitive Search
description: Frissítsen a Azure Search .NET Management SDK-ra a korábbi verziókról. Ismerkedjen meg az új szolgáltatásokkal, és az áttelepítéshez szükséges kód módosításait.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936707"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>A Azure Search .NET Management SDK verzióinak frissítése

Ez a cikk azt ismerteti, hogyan lehet áttérni a Azure Search .NET Management SDK egymást követő verzióira, amelyek a keresési szolgáltatások kiépítésére vagy kiépítésére, a kapacitás módosítására és az API-kulcsok kezelésére szolgálnak.

A felügyeleti SDK-k a felügyeleti REST API egy adott verzióját célozzák meg. A fogalmakkal és a műveletekkel kapcsolatos további információkért lásd: [keresési felügyelet (REST)](/rest/api/searchmanagement/).

## <a name="versions"></a>Verziók

| SDK verziója | A megfelelő REST API verziója | Szolgáltatások hozzáadásának vagy viselkedésének változása |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | API-Version = 2020 – 30 – 20 | Végpontok biztonsága (IP-tűzfalak és az [Azure Private-kapcsolat](../private-link/private-endpoint-overview.md)integrációja) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | API-Version = 2019-10-01 | Használhatóság javítása. A [lekérdezési kulcsok](/rest/api/searchmanagement/querykeys/listbysearchservice) lekérésének megszakítása (a Get megszűnik). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | API-Version = 2015-08-19  | Első verzió |

## <a name="how-to-upgrade"></a>A frissítés lépései

1. Frissítse a NuGet-referenciát `Microsoft.Azure.Management.Search` a NuGet Package Manager konzoljának használatával, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

1. Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. A kód szerkezetének módjától függően előfordulhat, hogy az Újraépítés sikeresen megtörtént, és ebben az esetben készen áll.

1. Ha a létrehozás sikertelen, annak oka az lehet, hogy néhány SDK-felületet implementált (például az egység tesztelése céljából), amelyek megváltoztak. Ennek megoldásához újabb módszereket kell megvalósítani, például: `BeginCreateOrUpdateWithHttpMessagesAsync` .

1. A felépítési hibák kijavítása után módosíthatja az alkalmazást, hogy kihasználhassa az új funkciókat. 

## <a name="upgrade-to-30"></a>Frissítés 3,0-re

Az 3,0-es verzió a magánhálózati végpontok védelmét az IP-tartományokhoz való hozzáférés korlátozásával, a nyilvános interneten nem látható keresési szolgáltatások esetében pedig opcionálisan integrálja az Azure Private-hivatkozással.

### <a name="new-apis"></a>Új API-k

| API | Kategória| Részletek |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP-tűzfal | A szolgáltatás-végpontokhoz való hozzáférés korlátozása az engedélyezett IP-címek listájára. Lásd: az [IP-tűzfal konfigurálása](service-configure-firewall.md) fogalmak és portál utasításokhoz. |
| [Megosztott privát kapcsolati erőforrás](/rest/api/searchmanagement/sharedprivatelinkresources) | Private Link | Hozzon létre egy megosztott magánhálózati kapcsolati erőforrást, amelyet egy keresési szolgáltatás használni fog.  |
| [Magánhálózati végpontok kapcsolatai](/rest/api/searchmanagement/privateendpointconnections) | Private Link | A keresési szolgáltatás kapcsolatainak létrehozása és kezelése privát végponton keresztül. Lásd: [privát végpont létrehozása](service-create-private-endpoint.md) fogalmakhoz és portálhoz – útmutatás.|
| [Privát kapcsolat erőforrásai](/rest/api/searchmanagement/privatelinkresources/) | Private Link | Egy privát végponti kapcsolatban álló keresési szolgáltatás esetében az azonos virtuális hálózatban használt összes szolgáltatás listájának beolvasása. Ha a keresési megoldás olyan indexelő tartalmaz, amelyek Azure-adatforrásokból (Azure Storage, Cosmos db, Azure SQL) származnak, vagy Cognitive Services vagy Key Vault használ, akkor az összes erőforrásnak rendelkeznie kell végpontokkal a virtuális hálózatban, és az API-nak egy listát kell visszaadnia. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Private Link | Ez egy tulajdonság a szolgáltatási kérelmek létrehozásához vagy frissítéséhez. Ha le van tiltva, a privát hivatkozás az egyetlen hozzáférési mód. |

### <a name="breaking-changes"></a>Kompatibilitástörő változások

A GET [lekérdezési kulcsokra](/rest/api/searchmanagement/querykeys/listbysearchservice) vonatkozó kérést már nem használhatja. Az előző kiadásokban használhatja a GET vagy a POST verziót, ebben a kiadásban és minden olyan kiadásban, amely továbbra is elérhető, csak a POST támogatott. 

## <a name="upgrade-to-20"></a>Frissítés 2,0-re

A Azure Search .NET Management SDK 2. verziója egy kisebb frissítés, ezért a kód módosítása csak minimális erőfeszítést igényelhet. Az SDK módosításai szigorúan ügyféloldali változások az SDK használhatóságának javítása érdekében. A változások a következőkre terjednek ki:

* `Services.CreateOrUpdate` a és az aszinkron verziói mostantól automatikusan lekérdezik a kiépítési és a nem visszaadott állapotot, `SearchService` amíg a szolgáltatás kiépítés be nem fejeződik. Ezzel elmentheti, hogy az ilyen lekérdezési kódot saját kezűleg írja.

* Ha továbbra is manuálisan szeretné lekérdezni a szolgáltatás üzembe helyezését, használhatja az új `Services.BeginCreateOrUpdate` metódust vagy annak egy aszinkron verzióját.

* Új metódusok `Services.Update` és aszinkron verziók lettek hozzáadva az SDK-hoz. Ezek a módszerek a HTTP-javítás használatával támogatják a szolgáltatások növekményes frissítését. Mostantól például méretezheti a szolgáltatást úgy, hogy átadja egy `SearchService` példányát ezekhez a metódusokhoz, amelyek csak a kívánt `partitionCount` és a `replicaCount` tulajdonságokat tartalmazzák. A régi módon hívható `Services.Get` meg, módosítható a visszaadott érték, `SearchService` és a továbbítása `Services.CreateOrUpdate` továbbra is támogatott, de már nem szükséges. 

## <a name="next-steps"></a>Következő lépések

Ha problémákba ütközik, a kérdések feladására szolgáló legjobb fórum [stack overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Ha hibát talál, a probléma az [Azure .net SDK GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/issues)is megadható. Ügyeljen arra, hogy a "[Search]" címkével címkézze fel a probléma címét.