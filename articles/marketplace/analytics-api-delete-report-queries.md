---
title: Jelentés lekérdezési API törlése
description: Ezzel az API-val törölhet felhasználó által definiált lekérdezéseket a kereskedelmi piactér-elemzésekhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583910"
---
# <a name="delete-report-queries-api"></a>Jelentés lekérdezési API törlése

Ez az API törli a felhasználó által definiált lekérdezéseket.

**Kérelem szintaxisa**

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Kérelem fejléce**

| **Fejléc** | **Típus** | **Leírás** |
| --- | --- | --- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési token az űrlapon `Bearer <token>` |
| Content-Type | sztring | `Application/JSON` |

**Elérési út paraméter**

| **Paraméter neve** | **Típus** | **Leírás** |
| --- | --- | --- |
| `queryId` | sztring | Szűrés az argumentumban megadott AZONOSÍTÓval rendelkező lekérdezések részleteinek lekéréséhez |

**Lekérdezési paraméter**

Nincs

**Kérelem tartalma**

Nincs

**Szószedet**

Nincs

**Válasz**

A válasz tartalma JSON formátumban a következőképpen van strukturálva.

Válasz kódja: 200, 400, 401, 403, 404, 500

Válasz hasznos adatai:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Szószedet**

Ez a táblázat a válasz elemeinek legfontosabb definícióit sorolja fel.

| **Paraméter** | **Leírás** |
| --- | --- |
| `QueryId` | A törölt lekérdezés egyedi UUID azonosítója. |
| `Name` | A törölt lekérdezés neve |
| `Description` | A törölt lekérdezés leírása |
| `Query` | A törölt lekérdezés jelentés lekérdezési karakterlánca |
| `Type` | Felhasználó által meghatározott |
| `User` | A lekérdezést létrehozó felhasználói azonosító |
| `CreatedTime` | A lekérdezés létrehozásának ideje |
| `ModifiedTime` | Null |
| `TotalCount` | Adatkészletek száma az érték tömbben |
| `StatusCode` | Eredmény kódja A lehetséges értékek a következők 200, 400, 401, 403, 500 |
