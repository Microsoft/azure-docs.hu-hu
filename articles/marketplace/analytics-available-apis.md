---
title: A kereskedelmi piactér elemzési adatokhoz való hozzáféréshez használható API-k
description: Ezen API-k használatával programozott módon férhet hozzá az elemzési szolgáltatásokhoz a partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583904"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>A kereskedelmi piactér elemzési adatokhoz való hozzáféréshez használható API-k

A következő lista az API-k listáját tartalmazza a kereskedelmi Piactéri elemzési adatok és a hozzájuk kapcsolódó funkciók eléréséhez.

- [Adatkészlet lekéréses API-jai](#dataset-pull-apis)
- [Lekérdezés-kezelési API-k](#query-management-apis)
- [Jelentéskészítési API-k](#report-management-apis)
- [Jelentés-végrehajtási lekéréses API-k](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>Adatkészlet lekéréses API-jai

***1. táblázat: adatkészlet lekéréses API-jai***

| **API** | **Funkció** |
| --- | --- |
| [Az összes adatkészlet lekérése](analytics-api-get-all-datasets.md) | Lekéri az összes rendelkezésre álló adatkészletet. Az adatkészletek felsorolják a táblákat, oszlopokat, metrikákat és időtartományokat. |
|||

## <a name="query-management-apis"></a>Lekérdezés-kezelési API-k

***2. táblázat: lekérdezés-kezelési API-k***

| **API** | **Funkció** |
| --- | --- |
| [Jelentés lekérdezésének létrehozása](analytics-programmatic-access.md#create-report-query-api) | Egyéni lekérdezéseket hoz létre, amelyek meghatározzák azt az adatkészletet, amelyből az oszlopokat és a metrikákat exportálni kell. |
| [Jelentéskészítési lekérdezések beolvasása](analytics-api-get-report-queries.md) | Lekérdezi a jelentésekben használható összes lekérdezést. Alapértelmezés szerint lekéri az összes rendszer-és felhasználó által definiált lekérdezést. |
| [Jelentéskészítési lekérdezések törlése](analytics-api-delete-report-queries.md) | Felhasználó által definiált lekérdezések törlése. |
|||

## <a name="report-management-apis"></a>Jelentéskészítési API-k

***3. táblázat: jelentéskészítési API-k***

| **API** | **Funkció** |
| --- | --- |
| [Jelentés létrehozása](analytics-programmatic-access.md#create-report-api) | A lekérdezés rendszeres időközönként történő végrehajtásának ütemezett időpontja. |
| [Jelentéskészítési lekérdezések KIPRÓBÁLÁSa](analytics-api-try-report-queries.md) | Egy jelentés lekérdezési utasítását hajtja végre. Csak 10 olyan rekordot ad vissza, amelyet a partnerek a várt módon ellenőrizhetik. |
| [Jelentés lekérése](analytics-api-get-report.md) | Az összes ütemezett jelentés beolvasása. |
| [Jelentés frissítése](analytics-api-update-report.md) | Jelentésparaméter módosítása |
| [Jelentés törlése](analytics-api-delete-report.md) | A jelentés és a jelentés-végrehajtási rekordok törlése. |
| [Jelentések végrehajtásának szüneteltetése](analytics-api-pause-report-executions.md) | Szünetelteti a jelentések ütemezett végrehajtását. |
| [Jelentések végrehajtásának folytatása](analytics-api-resume-report-executions.md) | Folytatja egy szüneteltetett jelentés ütemezett végrehajtását. |
|||

## <a name="report-execution-pull-apis"></a>Jelentés-végrehajtási lekéréses API-k

***4. táblázat: jelentés-végrehajtási lekéréses API-k***

| **API** | **Funkció** |
| --- | --- |
| [Jelentések végrehajtásának beolvasása](analytics-programmatic-access.md#get-report-executions-api) | Egy adott jelentés összes végrehajtásának beolvasása. |
|||

## <a name="next-steps"></a>Következő lépések

- Az API-kat kipróbálhatja a [hencegő API URL-címén](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)keresztül.
