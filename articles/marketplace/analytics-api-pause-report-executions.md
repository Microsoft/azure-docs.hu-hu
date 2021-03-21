---
title: Jelentések végrehajtásának szüneteltetése API
description: Ezzel az API-val szüneteltetheti egy kereskedelmi Piactéri elemzési jelentés ütemezett végrehajtását.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583836"
---
# <a name="pause-report-executions-api"></a>Jelentések végrehajtásának szüneteltetése API

Ez az API a végrehajtás során szünetelteti a jelentések ütemezett végrehajtását.

**Kérelem szintaxisa**

| Metódus | Kérés URI-ja |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Kérelem fejléce**

| Fejléc | Típus | Description |
| ------------ | ------------- | ------------- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési token az űrlapon `Bearer <token>` |
| Content-Type | sztring | `Application/JSON` |
||||

**Elérési út paraméter**

Nincsenek

**Lekérdezési paraméter**

| Paraméter neve | Kötelező | Típus | Leírás |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Igen | sztring | A módosítandó jelentés azonosítója |
|||||

**Szószedet**

Nincsenek

**Válasz**

A válasz tartalma a következőképpen van strukturálva JSON formátumban:

Válasz kódja: 200, 400, 401, 403, 404, 500 válasz hasznos adat:

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
| `ReportId` | A törölt jelentés univerzálisan egyedi azonosítója (UUID) |
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
