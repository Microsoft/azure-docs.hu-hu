---
title: Azure Service Fabric CLI – sfctl tároló
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A tárolók parancsainak listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f82883b68ab911fb0b89fc117d9a9d77e05a781a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245891"
---
# <a name="sfctl-container"></a>sfctl-tároló
Tárolóval kapcsolatos parancsok futtatása fürtcsomóponton.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| meghívás – API | A Container API meghívása egy Service Fabric csomóponton telepített tárolón az adott programkódhoz. |
| naplók | Beolvassa a Service Fabric csomóponton üzembe helyezett tároló naplófájljait. |

## <a name="sfctl-container-invoke-api"></a>sfctl-tároló meghívása – API
A Container API meghívása egy Service Fabric csomóponton telepített tárolón az adott programkódhoz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Code-Package-instance-ID [kötelező] | Olyan azonosító, amely egyedileg azonosítja a Service Fabric-csomóponton üzembe helyezett Code Package-példányt. <br><br> A "Service Code-Package-List" lekérdezéssel kérhető le. |
| --Code-Package-Name [kötelező] | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Container-API-URI-elérési_út [kötelező] | Tároló REST API URI elérési útja, használja a (z) {ID} nevet a tároló neve/azonosítója helyett. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| --Container-API-Body | A tároló REST API HTTP-kérelem törzse. |
| --Container-API-Content-Type | A tároló REST API tartalomtípusa alapértelmezett értéke az "Application/JSON". |
| --Container-API-http-művelet | A tároló REST API HTTP-művelete alapértelmezett értéke a beolvasás. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-container-logs"></a>sfctl-tároló naplói
Beolvassa a Service Fabric csomóponton üzembe helyezett tároló naplófájljait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Code-Package-instance-ID [kötelező] | Kód csomag példányának azonosítója, amelyet a "Service Code-Package-List" paranccsal kérhet le. |
| --Code-Package-Name [kötelező] | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| – farok | A naplók végétől megjelenítendő sorok száma. Az alapértelmezett érték a 100. az "all" (összes) a teljes naplókat jeleníti meg. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
