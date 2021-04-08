---
title: Jelentések végrehajtásának folytatása API
description: Ezzel az API-val folytathatja a szüneteltetett kereskedelmi Piactéri elemzési jelentés ütemezett végrehajtását.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583800"
---
# <a name="resume-report-executions-api"></a>Jelentések végrehajtásának folytatása API

Ez az API a végrehajtás során folytatja a szüneteltetett kereskedelmi Piactéri elemzési jelentés ütemezett végrehajtását.

**Kérelem szintaxisa**

| Metódus | Kérés URI-ja |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
|||

**Kérelem fejléce**

| Fejléc | Típus | Leírás |
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
| `ReportId` | A folytatott jelentés univerzálisan egyedi azonosítója (UUID) |
| `ReportName` | A jelentéshez megadott név a létrehozás során |
| `Description` | A jelentés létrehozása során megadott leírás |
| `QueryId` | A jelentés létrehozásának időpontjában átadott lekérdezési azonosító |
| `Query` | A jelentéshez végrehajtandó szöveg lekérdezése |
| `User` | A jelentés létrehozásához használt felhasználói azonosító |
| `CreatedTime` | A jelentés létrehozásának ideje. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `ModifiedTime` | A jelentés utolsó módosításának időpontja. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `StartTime` | A jelentés végrehajtásának időpontja. Az időformátum éééé-hh-NNTóó: PP: ssZ |
| `ReportStatus` | A jelentés végrehajtásának állapota. A lehetséges értékek: szüneteltetve, aktív és inaktív. |
| `RecurrenceInterval` | A jelentés létrehozásakor megadott ismétlődési időköz |
| `RecurrenceCount` | A jelentés létrehozásakor megadott ismétlődések száma |
| `CallbackUrl` | A kérelemben megadott visszahívási URL-cím |
| `Format` | A jelentés fájljainak formátuma |
|||
