---
title: Az Azure bejárati ajtó standard/prémium szabálykészlet egyeztetési feltételeinek beállítása
description: Ez a cikk az Azure bejárati ajtó standard/prémium szabálykészlet által elérhető különböző egyeztetési feltételek listáját tartalmazza.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: yuajia
ms.openlocfilehash: 039effb885463c1c53085535a6980601be890340
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561438"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure bejárati ajtó standard/prémium (előzetes verzió) szabályának beállítása egyeztetési feltételek

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure-beli bejárati ajtó standard/prémium [szabálykészlet beállításban](concept-rule-set.md)egy szabály nulla vagy több egyeztetési feltételből és egy műveletből áll. Ez a cikk részletesen ismerteti az Azure-beli előtérben szabványos/prémium szabálykészlet által használható egyezési feltételeket.

A szabály első része az egyeztetési feltétel vagy az egyeztetési feltételek halmaza. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez meghatározott műveleteket végeztek. Ha több egyeztetési feltételt használ, az egyeztetési feltételek a és a Logic használatával vannak csoportosítva. A több értéket támogató egyezési feltételek vagy a logika használata.

Az egyeztetési feltételt a következőre használhatja:

* Kérelmek szűrése adott IP-cím, ország vagy régió alapján.
* Kérelmek szűrése fejléc-információk alapján.
* A mobileszközök vagy asztali eszközök kéréseinek szűrése.
* A kérelem fájljának neve és a fájl kiterjesztése alapján szűri a kérelmeket.
* A kérelem URL-címe, protokoll, elérési út, lekérdezési karakterlánc, post ARG stb. alapján szűri a kérelmeket.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Eszköz típusa

A mobileszköz vagy asztali eszköz által készített kérelmek azonosításához használja az **eszköz típusának** egyeztetése feltételt.  

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-------|------------------|
| Operátor | <ul><li>A Azure Portal: `Equal` , `Not Equal`</li><li>Az ARM-sablonokban: `Equal` ; a `negateCondition` tulajdonsággal adhatja meg, hogy _ne legyen egyenlő_ |
| Érték | `Mobile`, `Desktop` |

### <a name="example"></a>Példa

Ebben a példában a mobileszköz alapján észlelt összes kérelem megfelel.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="A portál képernyőképe az eszköz típusának egyeztetése feltételt mutatja.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Argumentumok közzététele

A **post argumentumok** egyeztetési feltételével azonosíthatja a kérelmeket a post kérelem törzsében megadott argumentumok alapján. Egyetlen egyeztetési feltétel egyezik a POST kérelem törzsének egyetlen argumentumával. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

> [!NOTE]
> A **post argumentumok** egyeztetése feltétel a `application/x-www-form-urlencoded` tartalomtípussal működik.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Argumentumok közzététele | A POST argumentum nevét jelölő karakterlánc-érték. |
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Érték | Egy vagy több karakterlánc-vagy egész érték, amely a POST argumentum értékének megfelelő értéket jelöli. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában az összes olyan POST-kérést egyeztetjük, amelyben `customerName` argumentum van megadva a kérelem törzsében, és ahol az érték a `customerName` betűvel kezdődik `J` `K` . A bemeneti értékeket nagybetűvé alakítjuk, hogy az a, a, a és a értékkel kezdődő értékek `J` `j` `K` `k` mindegyike illeszkedjen a kis-és nagybetűkre.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="A portál képernyőképe az argumentumok egyeztetése feltételt mutatja.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Lekérdezési karakterlánc

A **lekérdezési karakterlánc** egyeztetési feltételével azonosíthatja azokat a kérelmeket, amelyek egy adott lekérdezési karakterláncot tartalmaznak. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

> [!NOTE]
> A teljes lekérdezési karakterlánc egyetlen karakterláncként van kialakítva, a sortávolság nélkül `?` .

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Lekérdezési sztring | Egy vagy több karakterlánc-vagy egész érték, amely a lekérdezési karakterláncnak megfelelő értéket jelöli. A `?` lekérdezési karakterlánc elején ne adja meg a következőt:. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában az összes olyan kérést egyeztetjük, amelyben a lekérdezési karakterlánc tartalmazza a karakterláncot `language=en-US` . Az egyeztetési feltételt a kis-és nagybetűk megkülönböztetésére szeretnénk használni, ezért nem alakítjuk át az esetet.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="A lekérdezési karakterlánc egyeztetési feltételét ábrázoló portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Távoli címek

A **távoli cím** egyeztetése feltétel azonosítja a kérelmeket a kérelmező helye vagy IP-címe alapján. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

* IP-CIDR megadásakor használja a jelölést. Ez azt jelenti, hogy az IP-címterület szintaxisa az alapszintű IP-cím, amelyet egy perjel és az előtag mérete követ. Például:
    * **IPv4-példa**: a `5.5.5.64/26` 5.5.5.127-en keresztül 5.5.5.64-címekről érkező kérelmekre illeszkedik.
    * **IPv6-példa**: a `1:2:3:/48` 1:2:3:0:0:0:0:0-tól 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF címről érkező kérelmekre illeszkedik.
* Ha több IP-címet és IP-címtartományt ad meg, a "vagy a" logikát alkalmazza a rendszer.
    * **IPv4-példa**: Ha két IP-címet ad hozzá `1.2.3.4` `10.20.30.40` , és a feltétel megfelel a 1.2.3.4 vagy 10.20.30.40 érkező kéréseknek.
    * **IPv6-példa**: Ha két IP-címet ad hozzá `1:2:3:4:5:6:7:8` `10:20:30:40:50:60:70:80` , és a feltételt a 1:2:3:4:5:6:7:8 vagy a 10:20:30:40:50:60:70:80 címről érkező kérések esetén egyezteti.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | <ul><li>A Azure Portal:, `Geo Match` `Geo Not Match` ,, `IP Match` vagy `IP Not Match`</li><li>Az ARM-sablonokban: `GeoMatch` , `IPMatch` ; a `negateCondition` tulajdonság használatával megadásával megadhatók a _földrajzi nem egyezés_ vagy az _IP-cím nem egyezik_ .</li></ul> |
| Érték | <ul><li>A `IP Match` vagy `IP Not Match` operátorok esetében: egy vagy több IP-címtartományt ad meg. Ha több IP-címtartományt is meg van adva, azok kiértékelése a vagy a Logic használatával történik.</li><li>A `Geo Match` vagy `Geo Not Match` operátorok esetében: válasszon egy vagy több helyet az országkód alapján.</li></ul> |

### <a name="example"></a>Példa

Ebben a példában minden olyan kérésnek megfelel, amelyben a kérelem nem a Egyesült Államok származik.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="A távoli címek egyeztetési feltételét ábrázoló portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Kérelem törzse

A **kérelem törzsének** egyeztetési feltétele a kérés törzsében megjelenő konkrét szöveg alapján azonosítja a kérelmeket. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

> [!NOTE]
> Ha egy kérelem törzse meghaladja a 64 kb méretét, csak az első 64 kb fogja figyelembe venni a **kérelem törzsének** egyeztetési feltételéhez.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Érték | Egy vagy több olyan karakterlánc-vagy egész érték, amely a kérelem szövegtörzsének megfelelő értéket jelöli. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában minden olyan kérés megfelel, amelyben a kérelem törzse tartalmazza a karakterláncot `ERROR` . Az egyezés kiértékelése előtt átalakítja a kérés törzsét nagybetűvé, így a `error` többi eset variáció is elindítja ezt a megfeleltetési feltételt.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="A kérelem törzsének egyeztetési feltételét bemutató portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Kérelem fájljának neve

A **kérelem fájlnevének** egyeztetése feltétel azonosítja azokat a kérelmeket, amelyek tartalmazzák a megadott fájlnevet a kérelem URL-címében. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Érték | Egy vagy több karakterlánc-vagy egész érték, amely a kérelem fájlnevének megfelelő értéket jelöli. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában minden olyan kérés megfelel, amelyben a kérelem fájlneve szerepel `media.mp4` . Az egyezés kiértékelése előtt átalakítja a fájlnevet kisbetűs értékre, így a `MEDIA.MP4` többi eset variáció is elindítja ezt a megfeleltetési feltételt.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="A Portal képernyőképe, amely a kérelem fájlnevének egyeztetési feltételét mutatja.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Fájl kiterjesztésének kérése

A **kérelem fájlkiterjesztés** egyeztetése feltétel azonosítja azokat a kérelmeket, amelyek tartalmazzák a megadott fájlkiterjesztést a kérelem URL-címében a fájlnévben. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

> [!NOTE]
> Ne adja meg a kezdő időszakot. Használja például az `html` értéket az `.html` érték helyett.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Érték | Egy vagy több karakterlánc-vagy egész érték, amely a kérelem kiterjesztésének megfelelő értéket jelöli. Ne adja meg a kezdő időszakot. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában az összes olyan kérést egyeztetjük, amelyben a kérelem fájlkiterjesztés `pdf` vagy `docx` . A kérelem kiterjesztését kisbetűsre alakítjuk, mielőtt kiértékeljük a egyezést, így a `PDF` `DocX` , és más esetekben az egyeztetési feltétel is aktiválódik.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Portál képernyőképe, amely a kérelem fájlkiterjesztés egyeztetési feltételét mutatja.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Kérelem fejléce

A **kérelem fejlécének** egyeztetése feltétel azonosítja azokat a kérelmeket, amelyek egy adott fejlécet tartalmaznak a kérelemben. Ezzel a megfeleltetési feltétellel ellenőrizhető, hogy egy fejléc létezik-e az értékével, vagy annak ellenőrzéséhez, hogy a fejléc megfelel-e a megadott értéknek. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Fejléc neve | A POST argumentum nevét jelölő karakterlánc-érték. |
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Érték | Egy vagy több karakterlánc-vagy egész érték, amely a kérelem fejlécének megfelelő értéket jelöli. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában az összes olyan kérést egyeztetjük, amelyben a kérelem egy nevű fejlécet tartalmaz `MyCustomHeader` , az értéktől függetlenül.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="A kérelem fejlécének egyeztetési feltételét bemutató portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Kérelem metódusa

A **kérelem metódusának** egyeztetése feltétel azonosítja a megadott HTTP-kérelem módszert használó kérelmeket. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | <ul><li>A Azure Portal: `Equal` , `Not Equal`</li><li>Az ARM-sablonokban: `Equal` ; a `negateCondition` tulajdonsággal adhatja meg, hogy _ne legyen egyenlő_ |
| Kérelem metódusa | Egy vagy több HTTP-metódus a következők közül:,,,,, `GET` `POST` `PUT` `DELETE` `HEAD` `OPTIONS` , `TRACE` . Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |

### <a name="example"></a>Példa

Ebben a példában minden olyan kérés megfelel, amelyben a kérelem a `DELETE` metódust használja.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="A kérelem metódusának egyeztetési feltételét bemutató portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Kérelem elérési útja

A **kérelem útvonalának** egyeztetése feltétel azonosítja azokat a kérelmeket, amelyek tartalmazzák a megadott elérési utat a kérelem URL-címében. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

> [!NOTE]
> Az elérési út az URL-cím része az állomásnév és a perjel után. Az URL-címben például `https://www.contoso.com/files/secure/file1.pdf` az elérési út `files/secure/file1.pdf` .

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Érték | Egy vagy több olyan karakterlánc-vagy egész érték, amely a kérelem útvonalának megfelelő értéket jelöli. Ne adja meg a kezdő perjelet. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában minden olyan kérésnek megfelel, amelyben a kérelem fájljának elérési útja a következővel kezdődik: `files/secure/` . Az egyezés kiértékelése előtt átalakítja a fájl kiterjesztését a kisbetűsre, ezért a kérések `files/SECURE/` és más esetekben a változás is elindítja ezt a megfeleltetési feltételt.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="A kérelem elérési útjának egyeztetési feltételét bemutató portál képernyőképe":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Kérelem protokollja

A **kérelem protokolljának** egyeztetése feltétel azonosítja a megadott PROTOKOLLT (http vagy https) használó kérelmeket.

> [!NOTE]
> A *protokollt* más néven *sémának* is nevezzük.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | <ul><li>A Azure Portal: `Equal` , `Not Equal`</li><li>Az ARM-sablonokban: `Equal` ; a `negateCondition` tulajdonsággal adhatja meg, hogy _ne legyen egyenlő_ |
| Kérelem metódusa | `HTTP`, `HTTPS` |

### <a name="example"></a>Példa

Ebben a példában minden olyan kérés megfelel, amelyben a kérelem a `HTTP` protokollt használja.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="A portál képernyőképe a kérelem protokolljának egyeztetési feltételét mutatja.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> Kérelem URL-címe

A megadott URL-címnek megfelelő kérelmeket azonosítja. A rendszer kiértékeli a teljes URL-címet. Több értéket is megadhat az egyeztetéshez, amely kombinálva lesz a vagy a Logic használatával.

> [!TIP]
> A szabály feltételének használatakor ügyeljen arra, hogy tartalmazza a protokollt. Például `https://www.contoso.com` a helyett használja a parancsot `www.contoso.com` .

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-|-|
| Operátor | A [normál operátorok listájáról](#operator-list)származó bármely operátor. |
| Érték | Egy vagy több karakterlánc-vagy egész érték, amely a kérelem URL-címének megfelelő értéket jelöli. Ha több érték van megadva, azok kiértékelése a vagy a Logic használatával történik. |
| Eset átalakítása | `Lowercase`, `Uppercase` |

### <a name="example"></a>Példa

Ebben a példában a kérelem URL-címének megkezdéséhez tartozó összes kérelem megfelel `https://api.contoso.com/customers/123` . Az egyezés kiértékelése előtt átalakítja a fájl kiterjesztését a kisbetűsre, ezért a kérések `https://api.contoso.com/Customers/123` és más esetekben a változás is elindítja ezt a megfeleltetési feltételt.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Portál képernyőképe – a kérelem URL-címének egyeztetése.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Operátorok listája

Azok a szabályok, amelyek a normál operátorok listájából fogadnak értékeket, a következő operátorok érvényesek:

| Operátor                   | Leírás                                                                                                                    | ARM-sablon támogatása                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Bármelyik                        | Egyezés, ha bármilyen érték van, függetlenül attól, hogy mi ez.                                                                     | `operator`: `Any`                                               |
| Egyenlő                      | Megfelel, ha az érték pontosan megfelel a megadott sztringnek.                                                                   | `operator`: `Equal`                                             |
| Contains                   | Megfelel, ha az érték a megadott karakterláncot tartalmazza.                                                                          | `operator`: `Contains`                                          |
| Kisebb, mint                  | Akkor egyezik, ha az érték hossza kisebb, mint a megadott egész szám.                                                       | `operator`: `LessThan`                                          |
| Nagyobb, mint               | Akkor egyezik, ha az érték hossza nagyobb, mint a megadott egész szám.                                                    | `operator`: `GreaterThan`                                       |
| Kisebb vagy egyenlő         | Akkor egyezik, ha az érték hossza kisebb vagy egyenlő, mint a megadott egész szám.                                           | `operator`: `LessThanOrEqual`                                   |
| Nagyobb vagy egyenlő      | Akkor egyezik, ha az érték hossza nagyobb vagy egyenlő, mint a megadott egész szám.                                        | `operator`: `GreaterThanOrEqual`                                |
| Ezzel kezdődik                | Egyezés, ha az érték a megadott karakterlánccal kezdődik.                                                                       | `operator`: `BeginsWith`                                        |
| Erre végződik                  | Egyezés, ha az érték a megadott karakterlánccal végződik.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | Megfelel, ha az érték megfelel a megadott reguláris kifejezésnek. [További részletekért lásd alább.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Nem                    | Egyezés, ha nincs érték.                                                                                                | `operator`: `Any` és `negateCondition` : `true`                |
| Nem egyenlő                  | Egyezés, ha az érték nem felel meg a megadott karakterláncnak.                                                                    | `operator`: `Equal` és `negateCondition` : `true`              |
| Nem tartalmazza               | Egyezés, ha az érték nem tartalmazza a megadott karakterláncot.                                                                  | `operator`: `Contains` és `negateCondition` : `true`           |
| Nem kisebb, mint              | Akkor egyezik, ha az érték hossza nem kisebb a megadott egész számnál.                                                   | `operator`: `LessThan` és `negateCondition` : `true`           |
| Nem nagyobb, mint           | Akkor egyezik, ha az érték hossza nem nagyobb a megadott egész számnál.                                                | `operator`: `GreaterThan` és `negateCondition` : `true`        |
| Nem kisebb vagy egyenlő     | Akkor felel meg, ha az érték hossza nem kisebb a megadott egész számnál vagy azzal egyenlő.                                       | `operator`: `LessThanOrEqual` és `negateCondition` : `true`    |
| Nem nagyobb vagy egyenlő | Akkor egyezik, ha az érték hossza nem nagyobb vagy egyenlő a megadott egész számmal.                                    | `operator`: `GreaterThanOrEqual` és `negateCondition` : `true` |
| Nem kezdődik            | Egyezés, ha az érték nem a megadott karakterlánccal kezdődik.                                                               | `operator`: `BeginsWith` és `negateCondition` : `true`         |
| Nem végződik              | Egyezés, ha az érték nem a megadott karakterlánccal végződik.                                                                 | `operator`: `EndsWith` és `negateCondition` : `true`           |
| Nem RegEx                  | Egyezés, ha az érték nem felel meg a megadott reguláris kifejezésnek. [További részletekért lásd alább.](#regular-expressions) | `operator`: `RegEx` és `negateCondition` : `true`              |

> [!TIP]
> A (z) *vagy* annál *kisebb* numerikus operátorok esetében a felhasznált összehasonlítás a hosszon alapul. Az egyeztetési feltételben szereplő értéknek egész számnak kell lennie, amely meghatározza az összehasonlítani kívánt hosszúságot.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Reguláris kifejezések

A reguláris kifejezések nem támogatják a következő műveleteket:

* Alkifejezések Backreferences és rögzítése.
* Tetszőleges nulla szélességű állítások.
* Alrutin-hivatkozások és rekurzív mintázatok.
* Feltételes minták.
* A vezérlési műveletek visszautasítása.
* Az `\C` egybájtos direktíva.
* A `\R` sortörési egyeztetési direktíva.
* A `\K` Match reset utasítás kezdete.
* Képfeliratok és beágyazott kód.
* Atomi csoportosítás és a birtokosi számszerűsítés.

## <a name="arm-template-support"></a>ARM-sablon támogatása

A szabálykészlet Azure Resource Manager sablonok használatával konfigurálhatók. [Példa sablonra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set). A fenti példákban szereplő JSON-vagy bicep-kódrészletek használatával is hozzáadhat egyezési feltételeket.

## <a name="next-steps"></a>Következő lépések

* További információ a [szabálykészlet beállításáról](concept-rule-set.md).
* Megtudhatja, hogyan [konfigurálhatja az első szabálykészlet](how-to-configure-rule-set.md)beállítását.
* További információ a [szabálykészlet műveleteiről](concept-rule-set-actions.md).
