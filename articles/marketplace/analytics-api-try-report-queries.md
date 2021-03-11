---
title: A Report lekérdezési API kipróbálása
description: Ezzel az API-val jelentéskészítő lekérdezést futtathat a kereskedelmi piactér-elemzési jelentésekhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583797"
---
# <a name="try-report-queries-api"></a>A Report lekérdezési API kipróbálása

Ez az API egy jelentés lekérdezési utasítását hajtja végre. Az API csak 10 olyan rekordot ad vissza, amelyet partnerként használhat annak ellenőrzéséhez, hogy az adatok a várt módon működnek-e.

> [!IMPORTANT]
> Az API-val a lekérdezés végrehajtási időtúllépése 100 másodperc. Ha azt tapasztalja, hogy az API 100 másodpercnél hosszabb időt vesz igénybe, nagyon valószínű, hogy a lekérdezés szintaktikai helyes vagy más, mint a 200-es hibakód. Ha a lekérdezés szintaxisa helyes, a tényleges jelentés generálása sikeres lesz.

**Kérelem szintaxisa**

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Kérelem fejléce**

| **Fejléc** | **Típus** | **Leírás** |
| --- | --- | --- |
| Engedélyezés | sztring | Kötelező. A Azure Active Directory (Azure AD) hozzáférési token az űrlapon `Bearer <token>` |
| Content-Type | sztring | `Application/JSON` |
|||

**QueryParameter**

| **Paraméter neve** | **Típus** | **Leírás** |
| --- | --- | --- |
| `exportQuery` | sztring | A jelentés lekérdezési karakterláncát kell végrehajtani |
| `queryId` | sztring | Érvényes létező lekérdezés-azonosító |
|||

**Elérési út**  **paraméter**

Nincs

**Kérelem tartalma**

Nincs

**Szószedet**

Nincs

**Válasz**

A válasz tartalma a következőképpen van strukturálva:

Válasz kódja: 200, 400, 401, 403, 404, 500

Válasz hasznos adat: a lekérdezés végrehajtásának első 10 sora
