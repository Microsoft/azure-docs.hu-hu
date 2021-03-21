---
title: Jelentési lekérdezési API beolvasása
description: Ezzel az API-val lekérheti a kereskedelmi piactér Analytics-jelentéseiben használható összes lekérdezést.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583868"
---
# <a name="get-report-queries-api"></a>Jelentési lekérdezési API beolvasása

A jelentések lekérése API beolvassa a jelentésekben használható összes lekérdezést. Alapértelmezés szerint az összes rendszer-és felhasználó által definiált lekérdezést lekéri.

**Kérelem szintaxisa**

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Kérelem fejléce**

| **Fejléc** | **Típus** | **Leírás** |
| --- | --- | --- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési token az űrlapon `Bearer <token>` |
| Content-Type | sztring | `Application/JSON` |
||||

**Elérési út paraméter**

Nincsenek

**Lekérdezési paraméter**

| **Paraméter neve** | **Típus** | **Kötelező** | **Leírás** |
| --- | --- | --- | --- |
| `queryId` | sztring | No | Szűrés az argumentumban megadott AZONOSÍTÓval rendelkező lekérdezések részleteinek lekéréséhez |
| `queryName` | sztring | No | Szűrés az argumentumban megadott névvel rendelkező lekérdezések részleteinek lekéréséhez |
| `IncludeSystemQueries` | boolean | No | Előre definiált rendszerlekérdezések belefoglalása a válaszba |
| `IncludeOnlySystemQueries` | boolean | No | Csak a rendszerlekérdezések belefoglalása a válaszba |
|||||

**Kérelem tartalma**

Nincsenek

**Szószedet**

Nincsenek

**Válasz**

A válasz tartalma a következőképpen van strukturálva:

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

Ez a táblázat a válasz elemeinek legfontosabb definícióit ismerteti.

| **Paraméter** | **Leírás** |
| --- | --- |
| `QueryId` | A lekérdezés egyedi UUID azonosítója |
| `Name` | A lekérdezés által a lekérdezés létrehozásakor megadott név |
| `Description` | A lekérdezés létrehozásakor megadott leírás |
| `Query` | Jelentés lekérdezési karakterlánca |
| `Type` | Beállítás a `userDefined` felhasználó által létrehozott lekérdezések és `system` az előre definiált rendszerlekérdezések esetében |
| `User` | A lekérdezést létrehozó felhasználói azonosító |
| `CreatedTime` | A lekérdezés létrehozásának ideje |
| `TotalCount` | Adatkészletek száma az érték tömbben |
| `StatusCode` | Eredmény kódja A lehetséges értékek a következők 200, 400, 401, 403, 500 |
|||
