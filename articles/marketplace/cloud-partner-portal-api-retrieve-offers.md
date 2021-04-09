---
title: Ajánlatok beolvasása API – Azure Marketplace
description: API az ajánlatok összefoglaló listájának lekéréséhez a közzétevői névtérben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271960"
---
# <a name="retrieve-offers"></a>Ajánlatok lekérése

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Az ajánlatok összefoglaló listájának beolvasása a közzétevői névtérben.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek

| **Név**         |  **Leírás**                         |  **Adattípus** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Közzétevő azonosítója, például: `contoso` |   Sztring    |
|  api-verzió     | Az API legújabb verziója                    |    Date        |
|  |  |

## <a name="header"></a>Fejléc

|  **Név**        |         **Érték**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Engedélyezés   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>Példa szövegtörzsre

### <a name="response"></a>Reagálás

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Válasz törzsének tulajdonságai

|  **Név**       |       **Leírás**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Az ajánlat típusát azonosítja.                                                                                           |
|  publisherId    | A közzétevőt egyedileg azonosító azonosító                                                                      |
|  status         | Az ajánlat állapota. A lehetséges értékek listájáért lásd az [ajánlat állapota](#offer-status) alább.                         |
|  id             | GUID, amely egyedileg azonosítja az ajánlatot a közzétevő névterében.                                                    |
|  version        | Az ajánlat aktuális verziója. Az ügyfél nem tudja módosítani a Version tulajdonságot. Minden közzététel után növekszik. |
|  definíció     | A munkaterhelés tényleges definíciójának összefoglaló nézetét tartalmazza. A részletes definíciók beszerzéséhez használja az [adott ajánlat beolvasása](./cloud-partner-portal-api-retrieve-specific-offer.md) API-t. |
|  changedTime    | Az ajánlat utolsó módosításának UTC-ideje                                                                              |
|  |  |

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód**  |  **Leírás**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` – A kérés feldolgozása sikeres volt, és a közzétevő alatt lévő összes ajánlat vissza lett küldve az ügyfélnek.  |
|  400      | `Bad/Malformed request` – A hiba-válasz törzse több információt is tartalmazhat.                                    |
|  403      | `Forbidden` – Az ügyfél nem fér hozzá a megadott névtérhez.                                          |
|  404      | `Not found` – A megadott entitás nem létezik.                                                                 |
|  |  |

### <a name="offer-status"></a>Ajánlat állapota

|  **Név**                    | **Leírás**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Az ajánlatot még soha nem tették közzé.                  |
|  NotStarted                  | Az ajánlat új, de nem indult el.                 |
|  WaitingForPublisherReview   | Az ajánlat a közzétevő jóváhagyására vár.         |
|  Futó                     | Az ajánlatok beküldése folyamatban van.             |
|  Sikeres                   | Az ajánlat küldése befejeződött a feldolgozás során.       |
|  Megszakítva                    | Az ajánlat beküldését megszakították.                   |
|  Sikertelen                      | Az ajánlat küldése nem sikerült.                         |
|  |  |
