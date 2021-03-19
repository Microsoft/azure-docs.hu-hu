---
title: Az API-formátumok támogatásának korlátozásai és részletei
titleSuffix: Azure API Management
description: Az Azure API Management által támogatott nyílt API-, WSDL-és WADL-formátumokkal kapcsolatos ismert problémák és korlátozások részletei.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932634"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák

## <a name="about-this-list"></a>A lista ismertetése

Az API-k importálásakor előfordulhat, hogy bizonyos korlátozásokkal vagy az importálás sikeres végrehajtása előtt meg kell határozni azokat a problémákat, amelyeket orvosolni kell. Ez a cikk ezeket a korlátozásokat az API importálási formátuma alapján rendezi. Azt is leírja, hogyan működik a OpenAPI exportálása.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/hencegés importálási korlátai

Ha hibaüzenetet kap a OpenAPI-dokumentum importálásakor, győződjön meg róla, hogy előzetesen ellenőrizte. Ezt megteheti, ha a tervezőt használja a Azure Portalban (OpenAPI-specifikációs szerkesztő), vagy egy harmadik féltől származó eszközzel, például a <a href="https://editor.swagger.io">hencegés szerkesztőjével</a>.

### <a name="general"></a><a name="open-api-general"> </a>Általános kérdések

-   Az elérési út és a lekérdezés kötelező paramétereinek egyedi névvel kell rendelkezniük. (A OpenAPI csak egyedinek kell lennie egy helyen belül, például elérési út, lekérdezés, fejléc. API Management azonban lehetővé tesszük, hogy a műveletek a két útvonal és a lekérdezési paraméterek (amelyek nem támogatják a OpenAPI) megkülönböztetését. Ezért a paraméterek neveinek egyedinek kell lenniük a teljes URL-sablonon belül.)
-   `\$ref` a mutatók nem hivatkozhatnak külső fájlokra.
-   `x-ms-paths` és `x-servers` az egyetlen támogatott bővítmény.
-   Az egyéni bővítményeket a rendszer figyelmen kívül hagyja az importálás során, és nem menti vagy nem őrzi meg az exportálást.
-   `Recursion` -API Management nem támogatja a rekurzív módon definiált definíciókat (például a magukra hivatkozó sémákat).
-   A forrásfájl URL-címe (ha elérhető) a relatív kiszolgálói URL-címekre lesz alkalmazva.
-   A biztonsági definíciók figyelmen kívül lesznek hagyva.
-   Az API-műveletekhez tartozó beágyazott séma-definíciók nem támogatottak. A séma-definíciók az API-hatókörben vannak meghatározva, és az API-műveleti kérelemben vagy a válasz hatókörében is szerepelhetnek.
-   Egy meghatározott URL-paraméternek az URL-sablon részét kell képeznie.
-   `server` az objektum nem támogatott az API-műveleti szinten.
-   `Produces` a kulcsszó, amely leírja az API által visszaadott MIME-típusokat, nem támogatott. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 2. verzió

-   Csak a JSON formátum támogatott.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 3-as verzió

-   Ha sok `servers` van megadva, API Management megpróbálja kiválasztani az első HTTPS URL-címet. Ha nincsenek HTTPs URL-címek – az első HTTP URL-cím. Ha nincs HTTP URL-cím, akkor a kiszolgáló URL-címe üres lesz.
-   `Examples` nem támogatott, de `example` .

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI importálási, frissítési és exportálási mechanizmusai

### <a name="general"></a><a name="open-import-export-general"> </a>Általános kérdések

-   A API Management szolgáltatásból exportált API-definíciók elsődlegesen a API Management szolgáltatáson kívüli alkalmazások számára készültek, amelyeknek meg kell hívniuk a API Management szolgáltatásban üzemeltetett API-t. Az exportált API-definíciók nem importálhatók újra ugyanabba vagy a különböző API Management szolgáltatásba. Az API-defiitions különböző többek között webszolgáltatások/envionments keresztüli konfigurálásához tekintse meg a dokumentációt, amely a API Management szolgáltatás és a git használatával történő használatáról nyújt tájékoztatást. 

### <a name="add-new-api-via-openapi-import"></a>Új API hozzáadása az OpenAPI import használatával

Az OpenAPI dokumentumban található minden egyes művelethez létrejön egy új művelet, amely az Azure-erőforrás nevét és a megjelenítendő nevet fogja beállítani a és a értékre `operationId` `summary` . `operationId` az érték normalizált az alábbi szabályok szerint. `summary` az értéket a rendszer importálja, és a hossza 300 karakterre van korlátozva.

Ha `operationId` nincs megadva (azaz nincs jelen, `null` vagy üres), az Azure-Erőforrás neve értéket a http-metódus és az elérésiút-sablon kombinálásával hozza létre, például: `get-foo` .

Ha `summary` nincs megadva (azaz nincs jelen, `null` vagy üres), `display name` az érték a következőre lesz beállítva: `operationId` . Ha `operationId` nincs megadva, a megjelenített név értéke a http-metódus és az elérésiút-sablon kombinálásával jön létre, például: `Get - /foo` .

### <a name="update-an-existing-api-via-openapi-import"></a>Meglévő API frissítése a OpenAPI-importálás használatával

A meglévő API importálása során a rendszer az OpenAPI dokumentumban leírt API-ra módosítja. A OpenAPI-dokumentum minden művelete a meglévő művelettel egyezik meg, ha az értékét összehasonlítja a `operationId` meglévő művelet Azure-erőforrásának nevével.

Ha a rendszer egyezést talál, a meglévő művelet tulajdonságai "helyben" lesznek frissítve.

Ha egy egyezés nem található, a fenti részben ismertetett szabályok alapján új művelet jön létre. Az importálás során a rendszer minden egyes új művelet esetében megkísérli egy meglévő műveletből származó házirendek másolását ugyanazzal a HTTP-metódussal és elérésiút-sablonnal.

A rendszer törli az összes meglévő nem egyező műveletet.

A következő irányelveket követve hatékonyabbá teheti az importálást:

- Győződjön meg arról, hogy minden művelethez meg kell adni a `operationId` tulajdonságot.
- A kezdeti importálás után ne módosítsa a módosítást `operationId` .
- Soha ne módosítsa `operationId` és http metódust vagy elérésiút-sablont egyszerre.

### <a name="export-api-as-openapi"></a>API exportálása OpenAPI

Az egyes műveletekhez az Azure-Erőforrás neve lesz exportálva `operationId` , és a megjelenített név lesz exportálva `summary` .
A operationId vonatkozó normalizálás szabályai

- Kis-és nagybetűkre konvertálható.
- Cserélje le a nem alfanumerikus karakterek egyes sorozatait egyetlen kötőjeltel, például a következőre: `GET-/foo/{bar}?buzz={quix}` `get-foo-bar-buzz-quix-` .
- A kötőjelek mindkét oldalon láthatók, például a következő `get-foo-bar-buzz-quix-` lesz: `get-foo-bar-buzz-quix`
- Csonkítás 76 karakterre, az erőforrás neveként legfeljebb négy karakterből állhat.
- Ha szükséges, használja a további négy karaktert a deduplikáló utótaghoz, a következő formátumban: `-1, -2, ..., -999` .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

A WSDL-fájlok SOAP-alapú áteresztő és SOAP-REST API-k létrehozásához használhatók.

-   **SOAP-kötések** – csak a "document" és a "literális" kódolású SOAP-kötések támogatottak. Az "RPC" stílus vagy a SOAP-kódolás nem támogatott.
-   **Wsdl: import** – ez az attribútum nem támogatott. Az ügyfeleknek egyetlen dokumentumba kell egyesíteni az importálást.
-   **Több részből álló üzenetek** – az ilyen típusú üzenetek nem támogatottak.
-   **WCF wsHttpBinding** – a Windows Communication Foundation által létrehozott SOAP-szolgáltatásoknak az BasicHttpBinding-wsHttpBinding használata nem támogatott.
-   **MTOM** – a MTOM-t használó <em>szolgáltatások működhetnek</em> . A hivatalos támogatás jelenleg nem érhető el.
-   **Rekurzív** – a APIM nem támogatja a rekurzív módon definiált (például a saját tömbre hivatkozó) típusokat.
-   **Több névtér** – több névtér is használható egy sémában, de csak a cél névtér használható az üzenetek részeinek definiálásához. A más bemeneti vagy kimeneti elemek meghatározására szolgáló nem a célként megadott névterek nem őrződnek meg. Habár egy ilyen WSDL-dokumentum importálható, az összes üzenet részeinek exportálásakor a WSDL cél névterét fogja használni.
-   **Tömbök** – a SOAP – Rest átalakítás csak az alábbi példában látható burkolt tömböket támogatja:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Jelenleg nincsenek ismert WADL-importálási problémák.
