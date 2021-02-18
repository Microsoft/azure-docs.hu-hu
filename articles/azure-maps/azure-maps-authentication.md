---
title: Hitelesítés Microsoft Azure térképekkel
titleSuffix: Azure Maps
description: 'Ismerkedjen meg a kérések hitelesítésének két módszerével a Azure Maps: megosztott kulcsú hitelesítés és a Azure Active Directory (Azure AD) hitelesítésben.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9c973859c8b7a3d04693946f50377837c3538b85
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094103"
---
# <a name="authentication-with-azure-maps"></a>Hitelesítés az Azure Maps használatával

Azure Maps a kérelmek hitelesítésének két módját támogatja: megosztott kulcsos hitelesítés és [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) hitelesítés. Ez a cikk mindkét hitelesítési módszert ismerteti Azure Maps szolgáltatások megvalósításának elősegítése érdekében.

> [!NOTE]
> A Azure Mapsekkel való biztonságos kommunikáció javítása érdekében mostantól támogatjuk a Transport Layer Security (TLS) 1,2, és a TLS 1,0 és 1,1 támogatását kihasználjuk. Ha jelenleg a TLS 1. x protokollt használja, értékelje ki a TLS 1,2 készültséget, és fejlesszen egy áttelepítési tervet a [tls 1,0-probléma megoldása](/security/solving-tls1-problem)című cikkben ismertetett teszteléssel.

## <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

 Az elsődleges és a másodlagos kulcsok a Azure Maps fiók létrehozása után jönnek létre. Javasoljuk, hogy az elsődleges kulcsot használja előfizetési kulcsként, amikor a Azure Maps megosztott kulcsos hitelesítéssel hívja meg. A megosztott kulcsos hitelesítés egy Azure Maps fiók által generált kulcsot ad át egy Azure Maps szolgáltatáshoz. A szolgáltatásokra Azure Maps minden egyes kérelemhez adja hozzá az *előfizetési kulcsot* paraméterként az URL-címhez. A másodlagos kulcsot olyan helyzetekben lehet használni, mint például a kulcsok változásai.  

A kulcsok a Azure Portalban való megtekintésével kapcsolatos információkért lásd: a [hitelesítés kezelése](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Biztonsági okokból ajánlott elforgatni az elsődleges és a másodlagos kulcsok között. A kulcsok elforgatásához frissítse az alkalmazást a másodlagos kulcs használatára, telepítse, majd nyomja le az elsődleges kulcs melletti ciklus/frissítés gombot egy új elsődleges kulcs létrehozásához. A régi elsődleges kulcs le lesz tiltva. A kulcsok elforgatásával kapcsolatos további információkért lásd: [Azure Key Vault beállítása kulcsfontosságú rotációs és naplózási](../key-vault/secrets/tutorial-rotation-dual.md) szolgáltatással

## <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

Az Azure-előfizetések egy Azure AD-Bérlővel érhetők el, és lehetővé teszik a részletes hozzáférés-vezérlést. A Azure Maps az Azure AD-vel Azure Maps-szolgáltatások hitelesítését nyújtja. Az Azure AD identitás-alapú hitelesítést biztosít az Azure AD-bérlőben regisztrált felhasználók és alkalmazások számára.

Azure Maps elfogadja a **OAuth 2,0** hozzáférési jogkivonatokat egy Azure Maps-fiókot tartalmazó Azure-előfizetéshez társított Azure ad-bérlők számára. A Azure Maps a következő jogkivonatokat is elfogadja:

* Azure AD-felhasználók
* A felhasználók által delegált engedélyeket használó partneri alkalmazások
* Azure-erőforrások felügyelt identitásai

A Azure Maps minden Azure Maps-fiókhoz létrehoz egy *egyedi azonosítót (ügyfél-azonosítót)* . Ha az ügyfél-azonosítót további paraméterekkel kombinálja, kérhet tokeneket az Azure AD-től.

További információ az Azure AD konfigurálásáról és a Azure Maps jogkivonatok igényléséről: [a Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md).

Az Azure AD-vel történő hitelesítéssel kapcsolatos általános információkért lásd: [Mi az a hitelesítés?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Felügyelt identitások az Azure-erőforrásokhoz és a Azure Maps

Az Azure- [erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) biztosítják az Azure-szolgáltatásokat egy automatikusan felügyelt alkalmazás-alapú rendszerbiztonsági tag számára, amely képes hitelesíteni az Azure ad-t. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával a felügyelt identitás rendszerbiztonsági tag jogosult a Azure Maps-szolgáltatások elérésére. Néhány példa a felügyelt identitásokra: Azure App Service, Azure Functions és Azure Virtual Machines. A felügyelt identitások listáját lásd: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Az alkalmazás Azure AD-hitelesítésének konfigurálása

Az alkalmazások az Azure ad-ben egy vagy több támogatott forgatókönyv használatával hitelesítve lesznek az Azure AD-Bérlővel. Az egyes Azure AD-alkalmazási forgatókönyvek az üzleti igények alapján eltérő követelményeket jelentenek. Egyes alkalmazások felhasználói bejelentkezést igényelhetnek, és más alkalmazásokhoz is szükség lehet az alkalmazás bejelentkezési élményére. További információ: [hitelesítési folyamatok és alkalmazási forgatókönyvek](../active-directory/develop/authentication-flows-app-scenarios.md).

Miután az alkalmazás hozzáférési jogkivonatot kap, az SDK és/vagy az alkalmazás egy HTTPS-kérést küld a következő, a szükséges HTTP-fejléceket tartalmazó, a többi REST API HTTP-fejléceken kívül:

| Fejléc neve    | Érték               |
| :------------- | :------------------ |
| x-MS-Client-ID | 30d7cc....9f55        |
| Engedélyezés  | Tulajdonos eyJ0e.... HNIVN |

> [!NOTE]
> `x-ms-client-id` a Azure Maps fiók-alapú GUID, amely megjelenik a Azure Maps hitelesítés lapon.

Íme egy példa egy Azure Maps Route-kérelemre, amely egy Azure AD OAuth-tulajdonosi jogkivonatot használ:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Az ügyfél-azonosító megtekintésével kapcsolatos információkért lásd a [hitelesítési adatok megtekintése](./how-to-manage-authentication.md#view-authentication-details)című témakört.

## <a name="authorization-with-role-based-access-control"></a>Engedélyezés szerepköralapú hozzáférés-vezérléssel

Azure Maps támogatja az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md) összes elsődleges típusának elérését, beleértve az egyes Azure ad-felhasználók, csoportok, alkalmazások, Azure-erőforrások és az Azure által felügyelt identitások használatát. A rendszerbiztonsági tag-típusok engedélyeket, más néven szerepkör-definíciókat kapnak. A szerepkör-definíciók engedélyeket biztosítanak a REST API műveletekhez. Egy vagy több Azure Maps-fiókhoz való hozzáférés alkalmazása hatókörként ismert. A rendszerbiztonsági tag, a szerepkör-definíció és a hatókör alkalmazásakor a rendszer létrehoz egy szerepkör-hozzárendelést. 

A következő részekben az Azure RBAC Azure Maps integrációjának fogalmait és összetevőit tárgyaljuk. A Azure Maps-fiók beállításának részeként egy Azure AD-címtár van társítva ahhoz az Azure-előfizetéshez, amelyhez a Azure Maps-fiók található. 

Az Azure RBAC konfigurálásakor ki kell választania egy rendszerbiztonsági tag szerepkör-hozzárendelését. Ha szeretné megtudni, hogyan adhat hozzá szerepkör-hozzárendeléseket a Azure Portal, tekintse meg az [Azure-szerepkörök hozzárendelése](../role-based-access-control/role-assignments-portal.md)című témakört.

### <a name="picking-a-role-definition"></a>Szerepkör-definíció kiválasztása

A következő szerepkör-definíciós típusok léteznek az alkalmazási forgatókönyvek támogatásához.

| Azure-szerepkör definíciója       | Description                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Adatolvasó Azure Maps      | Hozzáférést biztosít a nem változtatható Azure Maps REST API-khoz.                                                       |
| Azure Maps adatközreműködő | Hozzáférést biztosít a megváltoztathatatlan Azure Maps REST API-khoz. A változékonyság a műveletek: írás és törlés művelet határozza meg. |
| Egyéni szerepkör-definíció      | Hozzon létre egy kialakított szerepkört, amely lehetővé teszi a rugalmasan korlátozott hozzáférést Azure Maps REST API-khoz.                      |

Egyes Azure Maps szolgáltatások emelt szintű jogosultságokat igényelhetnek az írási és törlési műveletek elvégzéséhez Azure Maps REST API-kon. Írási vagy törlési műveleteket biztosító szolgáltatásokhoz Azure Maps adatközreműködői szerepkör szükséges. Az alábbi táblázat ismerteti, hogy mely szolgáltatások Azure Maps adatközreműködő, ha írási vagy törlési műveleteket használ a megadott szolgáltatásban. Ha a szolgáltatásban csak olvasási műveletek vannak használatban, Azure Maps adatolvasót Azure Maps adatközreműködő helyett lehet használni.

| Azure Maps szolgáltatás | Azure Maps szerepkör-definíció  |
| :----------------- | :-------------------------- |
| Adatok               | Azure Maps adatközreműködő |
| Létrehozó            | Azure Maps adatközreműködő |
| Térbeli            | Azure Maps adatközreműködő |

Az Azure RBAC beállításainak megtekintésével kapcsolatos információkért lásd: az [Azure RBAC konfigurálása Azure Mapshoz](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Egyéni szerepkör-definíciók

Az alkalmazások biztonságának egyik aspektusa, hogy alkalmazza a legalacsonyabb jogosultsági szint elvét. Ez az alapelv azt feltételezi, hogy a rendszerbiztonsági tag csak a szükséges hozzáférésre jogosult, és nincs további hozzáférése. Az egyéni szerepkör-definíciók létrehozása olyan használati eseteket támogat, amelyek további részletességet igényelnek a hozzáférés-vezérléshez. Egyéni szerepkör-definíció létrehozásához kiválaszthatja azokat a konkrét adatműveleteket, amelyek belefoglalják vagy kizárják a definíciót.

Ezt követően az egyéni szerepkör-definíció bármely rendszerbiztonsági tag szerepkör-hozzárendelésében használható. Az Azure egyéni szerepkör-definíciókkal kapcsolatos további tudnivalókért tekintse meg az [Azure egyéni szerepkörei](../role-based-access-control/custom-roles.md)című témakört.

Íme néhány példa arra, hogy az egyéni szerepkörök Hogyan javíthatják az alkalmazások biztonságát.

| Eset                                                                                                                                                                                                                 | Egyéni szerepkörre vonatkozó adatművelet (ek)                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Nyilvános vagy interaktív bejelentkezési weblap, amely alapszintű Térkép csempével és más REST API-kkal nem rendelkezik.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Olyan alkalmazás, amely csak fordított helymeghatározáshoz és más REST API-k használatát igényli.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Egy rendszerbiztonsági tag szerepköre, amely Azure Maps Creator-alapú térképi adatok és az alaptérkép csempe REST API-k olvasását kéri.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Olyan rendszerbiztonsági tag szerepköre, amelynek a létrehozási alapú térképi adatait kell olvasni, írni és törölni. Ez egy Térkép adatszerkesztői szerepkörként definiálható, de nem teszi lehetővé más REST API-k, például az alapszintű Térkép csempék elérését. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>A hatókör ismertetése

Szerepkör-hozzárendelés létrehozásakor az az Azure-erőforrás-hierarchiában van definiálva. A hierarchia tetején egy [felügyeleti csoport](../governance/management-groups/overview.md) , a legalacsonyabb pedig egy Azure-erőforrás, például egy Azure Maps-fiók.
Ha hozzárendel egy szerepkör-hozzárendelést egy erőforráscsoporthoz, a hozzáférés több Azure Maps fiókhoz vagy erőforráshoz is engedélyezhető a csoportban.

> [!TIP]
> A Microsoft általános javaslata, hogy hozzáférést kell rendelnie a Azure Maps fiók hatóköréhez, mert megakadályozza, hogy az azonos Azure-előfizetésben létező **más Azure Maps fiókokhoz való nem kívánt hozzáférést biztosít** .

## <a name="next-steps"></a>Következő lépések

További információ az Azure RBAC:
> [!div class="nextstepaction"]
> [Azure szerepkör-alapú hozzáférés](../role-based-access-control/overview.md)

Ha többet szeretne megtudni az alkalmazások Azure AD-vel és Azure Maps-vel való hitelesítéséről, tekintse meg a következőt:
> [!div class="nextstepaction"]
> [Hitelesítés kezelése Azure Maps](./how-to-manage-authentication.md)

Ha többet szeretne megtudni a Azure Maps térképkezelés Azure AD-vel való hitelesítéséről, olvassa el a következőt:
> [!div class="nextstepaction"]
> [Használja a Azure Maps térképkezelés](./how-to-use-map-control.md)