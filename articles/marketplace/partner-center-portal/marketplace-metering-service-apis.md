---
title: Mérési szolgáltatás API-k – Microsoft kereskedelmi piactér
description: A használati esemény API lehetővé teszi az SaaS-ajánlatok használati eseményeinek kibocsátását Microsoft AppSource és az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436062"
---
# <a name="marketplace-metered-billing-apis"></a>Marketplace mért számlázási API-k

A mért számlázási API-kat akkor kell használni, ha a közzétevő egyéni mérési dimenziókat hoz létre egy ajánlat közzétételéhez a partner Centerben. A mért számlázási API-kkal való integráció minden olyan megvásárolt ajánlat esetében szükséges, amely egy vagy több, egyéni dimenzióval rendelkező csomaggal rendelkezik a használati események kibocsátásához.

Az egyéni mérési méretek SaaS-hoz történő létrehozásával kapcsolatos további információkért lásd: [SaaS-mért számlázás](saas-metered-billing.md).

A felügyelt alkalmazási csomaggal rendelkező Azure-alkalmazásokra vonatkozó egyéni mérési dimenziók létrehozásával kapcsolatos további információkért lásd: [Az Azure-alkalmazás ajánlatának](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details)beállítása.

## <a name="enforcing-tls-12-note"></a>TLS 1,2-Megjegyzés kényszerítése

A TLS-verzió 1,2-es verziója a HTTPS-kommunikációhoz szükséges minimális verzióként van kikényszerítve. Ügyeljen rá, hogy ezt a TLS-verziót használja a kódban. A TLS 1,0-es és 1,1-es verziója elavult, és a rendszer elutasítja a csatlakozási kísérleteket.

## <a name="metered-billing-single-usage-event"></a>Mért számlázás egyszeri használati eseménye

A használati esemény API-ját a közzétevőnek kell meghívnia, hogy használati eseményeket bocsátson ki egy aktív erőforráson (előfizetett) az adott ügyfél által vásárolt csomaghoz. A használati eseményt külön bocsátjuk ki a kiadó által az ajánlat közzétételekor megadott csomag minden egyéni dimenziójához.

Egy naptári nap minden órájában csak egy használati eseményt lehet kiállítani. Például: 05:15 ma, egy használati eseményt is kibocsáthat. Ha ez az esemény el van fogadva, a következő használati eseményt fogadjuk el a 9:00 ma. Ha a 8:15 és a 8:59:59 közötti további eseményt küld ma, a rendszer duplikált állapotba utasítja. Az összes felhasznált egységet egy órán belül fel kell halmozni, majd egyetlen esemény keretében kell kibocsátani.

Erőforráson belül egy naptári nap minden órájában csak egy használati esemény lehet kibocsátva. Ha egy órában több egység is használatban van, akkor az óránként felhasznált összes egységet összesíti, majd egyetlen esemény keretében bocsátja ki. A használati események csak az elmúlt 24 órában állíthatók elő. Ha 8:00 és 8:59:59 közötti időszakban bármikor bocsát ki használati eseményt, és a 8:00 és az 8:59:59 közötti időszakra vonatkozó további eseményt küld, akkor azt a rendszer duplikálja.

**Post**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

| Paraméterének | Ajánlás          |
| ---------- | ---------------------- |
| `ApiVersion` | 2018-08-31 használata. |
| | |

*Kérelem fejlécei:*

| Content-Type       | A `application/json` használata  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez, lehetőleg egy GUID-azonosító. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`   | Egyedi hozzáférési jogkivonat, amely az API-hívást igénylő ISV-ket azonosítja. A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét a következő módon: <br> <ul> <li> SaaS a [token lekérése http post](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Felügyelt alkalmazás a [hitelesítési stratégiákban](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |

*Kérelem törzse – példa:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` az SaaS-alkalmazás és az egyéni fogyasztásmérőt kibocsátó felügyelt alkalmazások esetében eltérő jelentéssel rendelkezik. 

Az Azure Application Managed apps-csomagok esetében a `resourceId` felügyelt alkalmazás `resource group Id` . Az [Azure által felügyelt identitások jogkivonat használatával](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)a beolvasáshoz példaként használható parancsfájl található. 

SaaS-ajánlatok esetén a a `resourceId` SaaS-előfizetés azonosítója. Az SaaS-előfizetésekkel kapcsolatos további információkért lásd: [előfizetések listázása](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Válaszok

Kód: 200<br>
OK gombra. A használat kibocsátását a Microsoft oldalán fogadták el és rögzítették a további feldolgozás és számlázás érdekében.

Válasz hasznos adat például: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Kód: 400 <br>
Hibás kérelem.

* Hiányzó vagy érvénytelen kérelem-információ.
* `effectiveStartTime` több mint 24 órával régebben. Az esemény lejárt.
* Az SaaS-előfizetés nem előfizetett állapotban van.

Válasz hasznos adat például: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kód: 403<br>

Tilos. Az engedélyezési jogkivonat nincs megadva, érvénytelen vagy lejárt.  Vagy a kérelem egy olyan ajánlathoz próbál hozzáférni, amely az engedélyezési jogkivonat létrehozásához használt másik Azure AD alkalmazás AZONOSÍTÓval lett közzétéve.

Kód: 409<br>
Ütközés. Már sikeresen jelentettek egy használati eseményt a megadott erőforrás-AZONOSÍTÓhoz, a tényleges használati dátumhoz és az órára.

Válasz hasznos adat például: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Mért számlázási batch-használati esemény

A Batch-használati esemény API lehetővé teszi, hogy egyszerre több megvásárolt erőforrás használati eseményeit is kibocsátsa. Azt is lehetővé teszi, hogy több használati eseményt is kibocsátson ugyanarra az erőforrásra, amíg azok a különböző naptári órákra vonatkoznak. Az egyetlen kötegben lévő események maximális száma 25.

**Post:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

| Paraméter  | Ajánlás     |
| ---------- | -------------------- |
| `ApiVersion` | 2018-08-31 használata. |

*Kérelem fejlécei:*

| Content-Type       | A `application/json` használata       |
| ------------------ | ------ |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez, lehetőleg egy GUID-azonosító. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`      | Egyedi hozzáférési jogkivonat, amely az API-hívást igénylő ISV-ket azonosítja. A formátum az, `Bearer <access_token>` Amikor a közzétevő lekéri a jogkivonat értékét a következő módon: <br> <ul> <li> SaaS a [token lekérése http post](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Felügyelt alkalmazás a [hitelesítési stratégiákban](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |


*Kérelem törzse – példa:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` az SaaS-alkalmazás és az egyéni fogyasztásmérőt kibocsátó felügyelt alkalmazások esetében eltérő jelentéssel rendelkezik. 

Az Azure Application Managed apps-csomagok esetében a `resourceId` felügyelt alkalmazás `resource group Id` . Az [Azure által felügyelt identitások jogkivonat használatával](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)a beolvasáshoz példaként használható parancsfájl található. 

SaaS-ajánlatok esetén a a `resourceId` SaaS-előfizetés azonosítója. Az SaaS-előfizetésekkel kapcsolatos további információkért lásd: [előfizetések listázása](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Válaszok

Kód: 200<br>
OK gombra. A Batch-használat kibocsátását a Microsoft oldalán fogadták el és rögzítették további feldolgozásra és számlázásra. A rendszer visszaadja a válasz listáját a kötegben lévő egyes események állapotával. Érdemes megismételni a válasz tartalmát, hogy megértse a Batch esemény részeként küldött egyes használati események válaszait.

Válasz hasznos adat például: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Az API-válaszban hivatkozott állapotkód leírása `BatchUsageEvent` :

| Állapotkód  | Description |
| ---------- | -------------------- |
| `Accepted` | Elfogadott. |
| `Expired` | Lejárt használat. |
| `Duplicate` | Ismétlődő használat van megadva. |
| `Error` | Hibakód. |
| `ResourceNotFound` | A megadott használati erőforrás érvénytelen. |
| `ResourceNotAuthorized` | Ön nem jogosult az erőforrás használatának megadására. |
| `InvalidDimension` | Ebben az ajánlatban vagy tervben érvénytelen a dimenzió, amelynek a kihasználtsága átadásra kerül. |
| `InvalidQuantity` | Az átadott mennyiség kisebb vagy egyenlő, mint 0. |
| `BadArgument` | A bemenet hiányzik vagy helytelen formátumú. |

Kód: 400<br>
Hibás kérelem. A köteg több mint 25 használati eseményt foglalt magukban.

Kód: 403<br>
Tilos. Az engedélyezési jogkivonat nincs megadva, érvénytelen vagy lejárt.  Vagy a kérelem egy olyan ajánlathoz próbál hozzáférni, amely az engedélyezési jogkivonat létrehozásához használt másik Azure AD alkalmazás AZONOSÍTÓval lett közzétéve.

## <a name="development-and-testing-best-practices"></a>Fejlesztés és tesztelés – ajánlott eljárások

Az egyéni fogyasztásmérők kibocsátásának teszteléséhez implementálja a mérési API-val való integrációt, hozzon létre egy csomagot a közzétett SaaS-ajánlathoz egyéni dimenziókkal, az egységenkénti nulla árral. Ezt az ajánlatot előzetes verzióként teheti közzé, így csak a korlátozott számú felhasználó férhet hozzá és tesztelheti az integrációt.

Egy meglévő élő ajánlathoz is használhat privát csomagot, hogy a tesztelés során korlátozza a hozzáférést a csomaghoz korlátozott közönség számára.

## <a name="get-support"></a>Támogatás kérése

Kövesse a [partner Center kereskedelmi piactér programjának támogatásával](../support.md) kapcsolatos útmutatást a közzétevők támogatási lehetőségeinek megismeréséhez, és nyisson meg egy támogatási jegyet a Microsofttal.

## <a name="next-steps"></a>Következő lépések

A Service API-k mérésével kapcsolatos további információkért lásd: a [piactér-mérési szolgáltatás API-jai – gyakori kérdések](./marketplace-metering-service-apis-faq.md).
