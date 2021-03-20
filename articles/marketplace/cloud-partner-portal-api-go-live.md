---
title: Go Live API – Azure Marketplace
description: A Go Live API elindítja az ajánlat élő listázási folyamatát.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87292943"
---
# <a name="go-live"></a>Élő indítás

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Ez az API elindítja az alkalmazás éles környezetben való továbbításának folyamatát. Ez a művelet általában hosszú ideig fut. Ez a hívás az értesítési e-mailek listáját használja a [közzétételi](./cloud-partner-portal-api-publish-offer.md) API-műveletből.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**      |   **Leírás**                                                           | **Adattípus** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | A lekérdezni kívánt ajánlat közzétevő-azonosítója, például: `contoso`       |  Sztring       |
| offerId        | A lekérdezni kívánt ajánlat azonosítója                                   |  Sztring       |
| api-verzió    | Az API legújabb verziója                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Fejléc
------

|  **Név**       |     **Érték**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Engedélyezés   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Példa szövegtörzsre

### <a name="response"></a>Reagálás

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Válaszfejléc

|  **Név**             |      **Érték**                                                            |
|  --------             |      ----------                                                           |
| Hely    |  A művelet állapotának lekéréséhez szükséges relatív elérési út            |
|  |  |

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód** |  **Leírás**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – A kérés elfogadása sikeresen megtörtént. A válasz a művelet állapotának nyomon követésére szolgáló helyet tartalmaz. |
|  400     | `Bad/Malformed request` – További hibaüzenetek találhatók a válasz törzsében. |
|  404     |  `Not found` – A megadott entitás nem létezik.                                       |
|  |  |
