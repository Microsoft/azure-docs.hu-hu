---
title: A jelentés API frissítése
description: Használja ezt az API-t a kereskedelmi Piactéri elemzési jelentések paraméterének jelentéséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583794"
---
# <a name="update-report-api"></a>A jelentés API frissítése

Ez az API segítséget nyújt a jelentési paraméterek módosításához.

**Kérelem szintaxisa**

| Metódus | Kérés URI-ja |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Kérelem tartalma**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Szószedet**

Ez a táblázat a kérelem hasznos adatainak elemeinek legfontosabb definícióit sorolja fel.

| Paraméter | Kötelező | Leírás | Megengedett értékek |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | A jelentéshez hozzárendelendő név | sztring |
| `Description` | No | A létrehozott jelentés leírása | sztring |
| `StartTime` | Yes | Az időbélyeg, amely után a jelentés létrehozása megkezdődik | sztring |
| `RecurrenceInterval` | No | A jelentés órában való generálásának gyakorisága. A minimális érték 4 | egész szám |
| `RecurrenceCount` | No | A létrehozandó jelentések száma. Az alapértelmezett érték határozatlan | egész szám |
| `Format` | Yes | Az exportált fájl fájlformátuma. Az alapértelmezett érték a CSV. | CSV/TSV |
| `CallbackUrl` | Yes | a jelentéskészítési felhíváshoz HTTPS-visszahívás URL-címe | sztring |
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
| `ReportId` | A frissített jelentés univerzálisan egyedi azonosítója (UUID) |
| `ReportName` | A jelentésnek a kérelem hasznos adataiban megadott neve |
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
