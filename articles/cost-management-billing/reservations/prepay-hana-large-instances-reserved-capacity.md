---
title: Megtakarítás a SAP HANA nagyméretű példányaival Azure-foglalás segítségével
description: Megismerheti azokat az információkat, amelyeket tudni érdemes a HANA nagyméretű példányaira vonatkozó foglalás vásárlása előtt, illetve megismerheti a vásárlás módját.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: 579c7b5f66f52551e2504101406e1f7b2bb69e92
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775527"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Megtakarítás a SAP HANA nagyméretű példányaival Azure-foglalás segítségével

Pénzt takaríthat meg a SAP HANA nagyméretű példányok (HLI) költségein, ha Azure-foglalást vásárol előzetesen egy vagy három évre. A foglalási kedvezmény a megvásárolt lefoglalt példánynak megfelelő, kiépített HLI SKU-ra érvényes. Ez a cikk segít megismerni azokat az információkat, amelyeket tudni érdemes a foglalás vásárlása előtt, valamint bemutatja a vásárlás menetét.

A foglalás megvásárlásával egy vagy három évre elkötelezi magát a HLI használata mellett. A HLI fenntartott kapacitásának megvásárlása fedezi az SKU-val egy csomagba tartozó számítások és az NFS-tárolás költségeit. A foglalás nem tartalmazza a szoftverlicencelési költségeket, például az operációs rendszert, az SAP-t vagy a további tárolási költségeket. A rendszer automatikusan a kiépített SAP HLI-re alkalmazza a foglalási kedvezményt. A foglalási időszak lejárta után használatalapú fizetés szerinti díjak vonatkoznak a kiépített erőforrásra.

## <a name="purchase-considerations"></a>A vásárláskor megfontolandó szempontok

A fenntartott kapacitás megvásárlása előtt a HLI SKU-t ki kell építeni. A foglalásért fizethet előre vagy havi részletekben. Az alábbi korlátozások érvényesek a HLI fenntartott kapacitására:

- A foglalási kedvezmények csak a Nagyvállalati Szerződésre és a Microsoft-ügyfélszerződéses előfizetésekre vonatkoznak. Más előfizetés nem támogatott.
- A HLI fenntartott kapacitás esetében a rugalmas példányméret nem támogatott. A foglalás csak arra az SKU-ra és régióra vonatkozik, amelyhez megvásárolta.
- Az önkiszolgáló lemondás és csere nem támogatott.
- A fenntartott kapacitás hatóköre egyetlen hatókör, ezért egyetlen előfizetésre és erőforráscsoportra érvényes. A megvásárolt kapacitás nem frissíthető egy másik előfizetés általi használatra.
- A HANA fenntartott kapacitásához nem tartozhat megosztott foglalási hatókör. A foglalási hatókört nem lehet felosztani, egyesíteni vagy frissíteni.
- Egyszerre csak egy HLI-t vásárolhat meg a fenntartott kapacitás API-hívásaival. Továbbiakat másik API-hívások kezdeményezésével vásárolhat.

Fenntartott kapacitást az Azure Portalon, a [REST API](/rest/api/reserved-vm-instances/reservationorder/purchase) használatával vásárolhat.

## <a name="buy-a-hana-large-instance-reservation"></a>A HANA nagy méretű példányaira vonatkozó foglalás vásárlása

Az alábbi információk segítségével HLI-foglalást vásárolhat a [Reservation Order REST API-k](/rest/api/reserved-vm-instances/reservationorder/purchase) használatával.

### <a name="get-the-reservation-order-and-price"></a>Foglalási rendelés és ár lekérése

Első lépésként kérje le a foglalási rendelést és az árat a kiépített nagyméretű HANA-példány SKU-jához a [Calculate Price](/rest/api/reserved-vm-instances/reservationorder/calculate) API-val.

Az alábbi példa az [armclient](https://github.com/projectkudu/ARMClient) használatával végez REST API-hívást a PowerShell-lel. A foglalási rendelésnek, a Calculate Price API-kérelemnek és a kérelem törzsének a következőhöz hasonlóan kell kinéznie:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'billingplan': 'Monthly'
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

További információ az adatmezőkről és a hozzájuk tartozó leírásról: [HLI-foglalás mezői](#hli-reservation-fields).

Az alábbi példaválasz a kapott válaszhoz hasonlít. Jegyezze fel a következőhöz visszaadott értéket: `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Vásárlás

Az előző, [Foglalási rendelés és ár lekérése](#get-the-reservation-order-and-price) című szakaszban visszakapott `quoteId` és `reservationOrderId` használatával vásárolja meg a foglalást.

Példa a kérelemre:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
       'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
               'billingplan': 'Monthly'

        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Példa a válaszra. Ha a rendelés feladása sikeres, a `provisioningState` a következő lesz: `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>A vásárlás sikerességének ellenőrzése

Futtassa a Foglalási rendelés GET-kérést a megrendelés állapotának megtekintéséhez. A `provisioningState` értékének a következőnek kell lennie: `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

A válasznak a következő példához hasonlónak kell lennie.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>HLI-foglalás mezői

Az alábbi információk ismertetik a különböző foglalási mezők jelentését.

  **SKU** A HLI SKU neve. A következőhöz hasonlít: `SAP_HANA_On_Azure_<SKUname>`.

  **Hely** Az elérhető HLI-régiók. Tekintse meg az [SAP HANA on Azure (Large Instances) SKU-i](../../virtual-machines/workloads/sap/hana-available-skus.md) című részt az elérhető régiók megismeréséért. A hely sztringformátumának lekéréséhez használja a [get locations API-hívást](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Fenntartott erőforrás típusa** `SapHana`

  **Előfizetés** A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés kétféle típusú lehet: nagyvállalati szerződés (ajánlatszám: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft-ügyfélszerződés. A díjak az Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva (ha van ilyen), vagy kerettúllépésként lesznek számlázva.

  **Hatókör** A foglalás hatókörének egy hatókörnek kell lennie.

  **Időtartam** Egy év vagy három év. A következőhöz hasonlít: `P1Y` vagy `P3Y`.

  **Mennyiség** A foglaláshoz vásárolt példányok száma. A vásárolt mennyiség egyszerre egy HLI. További foglalásokhoz ismételje meg az API-hívást a megfelelő mezőkkel.

## <a name="troubleshoot-errors"></a>Hibák elhárítása

Foglalás vásárlásakor az alábbi példához hasonló hibaüzenetet kaphat. Ennek lehetséges oka az, hogy a HLI nem lett kiépítve vásárláshoz. Ha ez a helyzet, vegye fel a kapcsolatot a Microsoft-fiókok ügyfélszolgálatával, és kérje egy HLI kiépítését, mielőtt foglalást vásárolna.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>További lépések

- Tudnivalók az [Azure REST API-k Postman és cURL használatával történő meghívásáról](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Tekintse meg az [SAP HANA on Azure (Large Instances) SKU-i](../../virtual-machines/workloads/sap/hana-available-skus.md) című részt az elérhető SKU-k listájának és az elérhető régiók megismeréséért.