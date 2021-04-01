---
title: Ajánlat közzététele – Azure Marketplace
description: API a megadott ajánlat közzétételéhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272045"
---
# <a name="publish-an-offer"></a>Ajánlat közzététele

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Elindítja a közzétételi folyamatot a megadott ajánlathoz. Ez a hívás hosszú ideig futó művelet.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**      |    **Leírás**                               |  **Adattípus** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Közzétevő azonosítója, például: `contoso`      |   Sztring       |
|  offerId       | Ajánlat azonosítója                                 |   Sztring       |
|  api-verzió   | Az API legújabb verziója                        |   Date         |
|  |  |

## <a name="header"></a>Fejléc
------

|  **Név**        |    **Érték**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Engedélyezés   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Példa szövegtörzsre
------------

### <a name="request"></a>Kérés

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Kérelem törzsének tulajdonságai

|  **Név**               |   **Leírás**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  értesítés – e-mailek    | Az e-mail-címek vesszővel tagolt listája, amelyekről értesíteni kell a közzétételi művelet állapotát. |
|  |  |

### <a name="response"></a>Reagálás

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Válaszfejléc

|  **Név**             |    **Érték**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Hely    | A művelet állapotának lekéréséhez szükséges relatív elérési út     |
|  |  |

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód** |  **Leírás**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – A kérés elfogadása sikeresen megtörtént. A válasz olyan helyet tartalmaz, amely az elindított művelet nyomon követésére használható. |
| 400   | `Bad/Malformed request` – A hiba-válasz törzse további információkat is biztosíthat.                                                               |
| 422   | `Un-processable entity` – Azt jelzi, hogy a közzétenni kívánt entitás ellenőrzése sikertelen volt.                                                        |
| 404   | `Not found` – Az ügyfél által megadott entitás nem létezik.                                                                              |
|  |  |
