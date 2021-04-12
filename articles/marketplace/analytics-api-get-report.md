---
title: Jelentés API beolvasása
description: Ezzel az API-val beolvashatja a partner Centerben ütemezett elemzési jelentéseket.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583841"
---
# <a name="get-report-api"></a>Jelentés API beolvasása

Ez az API lekéri az összes ütemezett jelentést.

**Kérelem szintaxisa**

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Kérelem fejléce**

| **Fejléc** | **Típus** | **Leírás** |
| --- | --- | --- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési token az űrlapon `Bearer <token>` |
| Content-Type | sztring | `Application/JSON` |

**Elérési út paraméter**

Nincsenek

**Lekérdezési paraméter**

| **Paraméter neve** | **Kötelező** | **Típus** | **Leírás** |
| --- | --- | --- | --- |
| `reportId` | No | sztring | Szűrés a csak az argumentumban megadott jelentések részleteinek lekéréséhez `reportId` |
| `reportName` | No | sztring | Szűrés a csak az argumentumban megadott jelentések részleteinek lekéréséhez `reportName` |
| `queryId` | No | boolean | Előre definiált rendszerlekérdezések belefoglalása a válaszba |

**Szószedet**

Nincsenek

**Válasz**

A válasz tartalma JSON formátumban van strukturálva, a következőképpen:

Válasz kódja: 200, 400, 401, 403, 404, 500

Válasz hasznos adatai:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
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
| `ReportId` | A létrehozott jelentés egyedi UUID azonosítója |
| `ReportName` | A jelentésnek a kérelem hasznos adataiban megadott neve |
| `Description` | A jelentés létrehozásakor megadott leírás |
| `QueryId` | A jelentés létrehozásának időpontjában átadott lekérdezési azonosító |
| `Query` | A jelentéshez végrehajtandó szöveg lekérdezése |
| `User` | A jelentés létrehozásához használt felhasználói azonosító |
| `CreatedTime` | A jelentés létrehozásának ideje. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `ModifiedTime` | A jelentés utolsó módosításának időpontja. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `StartTime` | Az idő végrehajtása megkezdődik. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `ReportStatus` | A jelentés végrehajtásának állapota. A lehetséges értékek: szüneteltetve, aktív és inaktív. |
| `RecurrenceInterval` | A jelentés létrehozásakor megadott ismétlődési időköz |
| `RecurrenceCount` | A jelentés létrehozásakor megadott ismétlődések száma |
| `CallbackUrl` | A kérelemben megadott visszahívási URL-cím |
| `Format` | A jelentés fájljainak formátuma |
