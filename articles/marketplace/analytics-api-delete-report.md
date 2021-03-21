---
title: Jelentés API törlése
description: Ezzel az API-val törölheti az összes jelentést és a jelentés-végrehajtási rekordokat a kereskedelmi piactér Analytics jelentéseihez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583880"
---
# <a name="delete-report-api"></a>Jelentés API törlése

A végrehajtás során ez az API törli a jelentés és a jelentés végrehajtási rekordjait.

**Kérelem szintaxisa**

| Metódus | Kérés URI-ja |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Kérelem fejléce**

| Fejléc | Típus | Description |
| ------------ | ------------- | ------------- |
| Engedélyezés | sztring | Kötelező. Az Azure AD hozzáférési jogkivonata az űrlapon `Bearer <token>` |
| Tartalom típusa | sztring | `Application/JSON` |
||||

**Elérési út paraméter**

Nincsenek

**Lekérdezési paraméter**

| Paraméter neve | Kötelező | sztring | Leírás |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Igen | sztring | A módosítandó jelentés azonosítója |
|||||

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
      "RecurrenceCount": 0,
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

| Paraméter | Leírás |
| ------------ | ------------- |
| `ReportId` | A törölt jelentés egyedi UUID azonosítója |
| `ReportName` | A jelentéshez megadott név a létrehozás során |
| `Description` | A jelentés létrehozása során megadott leírás |
| `QueryId` | A jelentés létrehozásának időpontjában átadott lekérdezési azonosító |
| `Query` | A jelentéshez végrehajtandó szöveg lekérdezése |
| `User` | A jelentés létrehozásához használt felhasználói azonosító |
| `CreatedTime` | A jelentés létrehozásának ideje. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `ModifiedTime` | A jelentés utolsó módosításának időpontja. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `StartTime` | A jelentés végrehajtásának megkezdésének ideje. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `ReportStatus` | A jelentés végrehajtásának állapota. A lehetséges értékek: szüneteltetve, aktív és inaktív. |
| `RecurrenceInterval` | A jelentés létrehozásakor megadott ismétlődési időköz |
| `RecurrenceCount` | A jelentés létrehozásakor megadott ismétlődések száma |
| `CallbackUrl` | A kérelemben megadott visszahívási URL-cím |
| `Format` | A jelentés fájljainak formátuma |
|||
