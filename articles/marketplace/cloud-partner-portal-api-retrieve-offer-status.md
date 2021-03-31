---
title: Ajánlat állapotának beolvasása – Azure Marketplace
description: API az ajánlat aktuális állapotának lekéréséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 336f23f83c33bcee1887d0e41710e686b794a663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272011"
---
# <a name="retrieve-offer-status"></a>Ajánlat állapotának lekérése

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Az ajánlat aktuális állapotának beolvasása.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek

|  **Név**       |   **Leírás**                            |  **Adattípus** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Közzétevő azonosítója, például: `Contoso`  |     Sztring     |
|  offerId        | Az ajánlatot egyedileg azonosító GUID      |     Sztring     |
|  api-verzió    | Az API legújabb verziója                        |     Date       |
|  |  |

## <a name="header"></a>Fejléc


|  Name           |  Érték               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Engedélyezés  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Példa szövegtörzsre

### <a name="response"></a>Reagálás

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```

### <a name="response-body-properties"></a>Válasz törzsének tulajdonságai

|  **Név**             |    **Leírás**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Az ajánlat állapota. A lehetséges értékek listájáért lásd az [ajánlat állapota](#offer-status) alább. |
|  üzenet             | Az ajánlattal társított üzenetek tömbje                                                    |
|  lépések                | Azon lépések tömbje, amelyek az ajánlat közzétételekor haladnak                      |
|  estimatedTimeFrame   | A lépés végrehajtásához szükséges idő becslése rövid formátumban                       |
|  id                   | A lépés azonosítója                                                                         |
|  stepName             | A lépés neve                                                                               |
|  leírás          | A lépés leírása                                                                        |
|  status               | A lépés állapota. A lehetséges értékek listájáért lásd az alábbi [lépések állapotát](#step-status) .    |
|  üzenet             | A lépéshez kapcsolódó üzenetek tömbje                                                          |
|  processPercentage    | A lépés százalékos készültsége                                                              |
|  previewLinks         | *Jelenleg nincs implementálva*                                                                    |
|  liveLinks            | *Jelenleg nincs implementálva*                                                                    |
|  notificationEmails   | A rendszer a partner központba migrált ajánlatok esetében elavult. Az áttelepített ajánlatokat értesítő e-maileket a rendszer az eladó kapcsolattartási adatok menüpontban megadott e-mailbe küldi el a fiók beállításai között.<br><br>Nem áttelepített ajánlatok esetén az e-mail-címek vesszővel tagolt listája, amelyekről értesíteni kell a művelet állapotát        |
|  |  |

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód** |   **Leírás**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` – A kérés feldolgozása sikeres volt, és az ajánlat aktuális állapota vissza lett állítva. |
|  400     | `Bad/Malformed request` – A hiba-válasz törzse több információt is tartalmazhat.                 |
|  404     | `Not found` – A megadott entitás nem létezik.                                                |
|  |  |

### <a name="offer-status"></a>Ajánlat állapota

|  **Név**                    |    **Leírás**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Az ajánlatot még soha nem tették közzé.                          |
|  NotStarted                  | Az ajánlat új, és nem indult el.                            |
|  WaitingForPublisherReview   | Az ajánlat a közzétevő jóváhagyására vár.                 |
|  Futó                     | Az ajánlatok beküldése folyamatban van.                     |
|  Sikeres                   | Az ajánlat küldése befejeződött a feldolgozás során.               |
|  Megszakítva                    | Az ajánlat beküldését megszakították.                           |
|  Sikertelen                      | Az ajánlat küldése nem sikerült.                                 |
|  |  |

### <a name="step-status"></a>Lépés állapota

|  **Név**                    |    **Leírás**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | A lépés nem indult el.                        |
|  Folyamatban                  | A lépés fut.                             |
|  WaitingForPublisherReview   | A lépés a közzétevő jóváhagyására vár.      |
|  WaitingForApproval          | A lépés a folyamat jóváhagyására vár.        |
|  Blokkolva                     | A lépés le van tiltva.                             |
|  Elutasítva                    | A lépés elutasításra kerül.                            |
|  Kész                    | A lépés befejeződött.                            |
|  Megszakítva                    | A lépést megszakították.                           |
|  |  |
