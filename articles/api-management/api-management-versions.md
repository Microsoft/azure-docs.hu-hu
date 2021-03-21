---
title: Verziók az Azure API Managementban | Microsoft Docs
description: Ismerje meg az Azure API Management verzióinak koncepcióját.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 55951f288314d92cf5057e7d5c1e988f65cb3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040379"
---
# <a name="versions-in-azure-api-management"></a>Az Azure API Management verziói

A verziók lehetővé teszik a kapcsolódó API-k csoportjának bemutatását a fejlesztőknek. A verziók használatával biztonságosan kezelheti az API-ban feltörhető változtatásokat. Az ügyfelek választhatják az új API-verzió használatát, amikor készen állnak, míg a meglévő ügyfelek továbbra is régebbi verziót használnak. A verziók megkülönböztetni a verziószámot (amely bármely kiválasztott karakterlánc-érték), és a verziószámozási séma lehetővé teszi az ügyfeleknek, hogy azonosítsák a használni kívánt API-k melyik verzióját.

A legtöbb esetben az API-verziók a saját független API-ját is figyelembe vehetik. Két különböző API-verzió különböző műveletekkel és különböző házirendekkel rendelkezhet.

A verziók segítségével:

- Az API több verzióját is közzéteheti egyszerre.
- A verziók közötti különbségtételhez használjon elérési utat, lekérdezési karakterláncot vagy fejlécet.
- Használjon olyan karakterlánc-értéket, amelynek a verzióját azonosítani szeretné, ami lehet egy szám, egy dátum vagy egy név.
- Jelenítse meg az API-verziókat a fejlesztői portálon együtt csoportosítva.
- Hozzon létre egy meglévő (nem verziószámmal rendelkező) API-t, és hozzon létre egy új verziót a meglévő ügyfelek megszakítása nélkül.

[Ismerkedés a verziókkal a bemutató lépéseinek követésével.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Verziószámozási sémák

A különböző API-fejlesztők eltérő követelményekkel rendelkeznek a verziószámozáshoz. Az Azure API Management nem ír elő egyetlen módszert a verziószámozásra, hanem több lehetőséget biztosít.

### <a name="path-based-versioning"></a>Elérésiút-alapú verziószámozás

Az elérési út verziószámozási sémájának használatakor a verzió azonosítóját fel kell venni az API-kérelmek URL-címébe.

Például, `https://apis.contoso.com/products/v1` és `https://apis.contoso.com/products/v2` hivatkozhat ugyanarra az `products` API-ra, de a verziókra és a verzióra `v1` `v2` .

Az API-kérelmek URL-címének formátuma, amikor elérésiút-alapú verziószámozást használ: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Fejléc-alapú verziószámozás

A fejléc verziószámozási sémájának használatakor a verziószámnak szerepelnie kell egy HTTP-kérelem fejlécében bármely API-kéréshez. A HTTP-kérelem fejlécének nevét is megadhatja.

Előfordulhat például, hogy létrehoz egy nevű egyéni fejlécet `Api-Version` , és az ügyfelek megadhatják `v1` vagy a `v2` fejléc értékét.

### <a name="query-string-based-versioning"></a>Lekérdezési karakterlánc-alapú verziószámozás

A lekérdezési karakterlánc verziószámozási sémájának használatakor a verziószámnak szerepelnie kell egy lekérdezési karakterlánc paraméterben bármely API-kéréshez. Megadhatja a lekérdezési karakterlánc paraméterének nevét.

Az API-kérelmek URL-címének formátuma, ha lekérdezési karakterlánc-alapú verziószámozást használ: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

Például, `https://apis.contoso.com/products?api-version=v1` és `https://apis.contoso.com/products/api-version=v2` hivatkozhat ugyanarra az `products` API-ra, de a verziókra és a verzióra `v1` `v2` .

## <a name="original-versions"></a>Eredeti verziók

Ha egy verziót ad hozzá egy nem verziójú API-hoz, a rendszer `Original` automatikusan létrehozza a verziót, és az alapértelmezett URL-címen válaszol, a megadott verzióazonosító nélkül. A `Original` verzió biztosítja, hogy a meglévő hívók ne legyenek megszakítva a verziók hozzáadásának folyamata. Ha olyan új API-t hoz létre, amelyen engedélyezve van a verzió, a `Original` verzió nem jön létre.

## <a name="how-versions-are-represented"></a>A verziók ábrázolása

Az Azure API Management egy olyan nevű erőforrást tart *fenn, amely egy adott logikai* API-hoz tartozó verziókat jelöl. A verziókövetés tartalmazza a verziószámmal ellátott API megjelenített nevét, valamint a kérelmeknek a megadott verzióra való irányításához [használt verziószámozási sémát](#versioning-schemes) .

Az API-k minden verziója a saját API-erőforrásként van fenntartva, amelyet aztán egy adott verzióhoz társít. A verziókövetés különböző műveletekkel vagy házirendekkel rendelkező API-kat tartalmazhat. Előfordulhat, hogy jelentős módosításokat végez egy készletben lévő verziók között.

A Azure Portal beállítja a verziószámokat. A Azure Portal lévő verziók nevét és leírását módosíthatja.

A verziókövetés közvetlenül az [Azure CLI](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), [Resource Manager-sablonok](/azure/templates/microsoft.apimanagement/service/apiversionsets)vagy a [Azure Resource Manager API](/rest/api/apimanagement/2020-06-01-preview/apiversionset)használatával tekinthetők meg és kezelhetők.

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Nem verziószámú API áttelepítése egy verzióval ellátott API-ra

Ha a Azure Portal segítségével engedélyezi a verziószámozást egy meglévő API-ban, a rendszer az alábbi módosításokat hajtja végre a API Management-erőforrásokon:

 * A rendszer létrehoz egy új verziót.
 * A meglévő verziót a rendszer karbantartja és [konfigurálja `Original` API-verzióként](#original-versions). Az API a verzióhoz van csatolva, de nem kötelező megadni a verziószámot.
 * Az új verzió új API-ként jön létre, és a verzióhoz van csatolva. Ezt az új API-t a verziószámozási séma és az azonosító használatával kell elérni.

## <a name="versions-and-revisions"></a>Verziók és változatok

A verziók és a változatok különböző funkciók. Az egyes verziók több változattal is rendelkezhetnek, akárcsak a nem verziószámú API-k. Változatok használata nélkül is használhatja a változatokat, vagy fordítva. A verziók általában az API-verziók elválasztására szolgálnak, a módosítások azonban az API-k kisebb és nem törhető módosításaihoz is használhatók.

Ha azt tapasztalja, hogy a változat módosult, vagy ha a változatot egy béta-vagy tesztelési verzióban szeretné kialakítani, egy verziót is létrehozhat egy verzióból. A Azure Portal használatával kattintson a "verzió létrehozása a változatból" elemre a változatok lap változat helyi menüjében.

## <a name="developer-portal"></a>Fejlesztői portál

A [fejlesztői portál](./api-management-howto-developer-portal.md) az API-k egyes verzióit külön listázza.

![API Management fejlesztői portál a verzióval ellátott API-k listájának megjelenítéséhez](media/api-management-versions/portal-list.png)

Az API részletei az API összes verziójának listáját is megjelenítik. Egy `Original` verzió verziószám nélkül jelenik meg.

![API Management fejlesztői portál API-k részleteit és az API verzióinak listáját jeleníti meg](media/api-management-versions/portal-details.png)

> [!TIP]
> Az API-verziókat fel kell venni egy termékbe, mielőtt láthatóvá válnak a fejlesztői portálon.
