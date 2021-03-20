---
title: Tábla tárolási kimenete Azure Stream Analytics
description: Ez a cikk az Azure Table Storage-t ismerteti Azure Stream Analytics kimenetként.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 558ddf065d33a552034c5b129ea70bc144c494ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013889"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Tábla tárolási kimenete Azure Stream Analytics

Az [Azure Table Storage](../storage/common/storage-introduction.md) kiválóan elérhető, nagy mértékben méretezhető tárhelyet kínál, így az alkalmazások automatikusan méretezhetők a felhasználói igények kielégítése érdekében. A Table Storage a Microsoft NoSQL kulcs-és attribútum-tárolója, amelyet a séma kevesebb korlátozásával rendelkező strukturált adatkészletekhez használhat. Az Azure Table Storage az adatok megőrzését és hatékony lekérését is képes tárolni.

A következő táblázat felsorolja a tulajdonságok nevét és a táblázat kimenetének létrehozásához szükséges leírásokat.

| Tulajdonság neve | Description |
| --- | --- |
| Kimeneti alias |Egy rövid név, amelyet a lekérdezések a lekérdezés kimenetének a táblázatos tárolóba való irányításához használnak. |
| A(z) |Annak a Storage-fióknak a neve, ahová a kimenetet küldi. |
| Storage-fiók kulcsa |A Storage-fiókhoz társított hozzáférési kulcs. |
| Table name (Táblázat neve) |A tábla neve. A tábla akkor jön létre, ha nem létezik. |
| Partíciókulcs |A partíciós kulcsot tartalmazó kimeneti oszlop neve. A partíciós kulcs a partíció egyedi azonosítója egy olyan táblán belül, amely az entitás elsődleges kulcsának első részét képezi. Ez egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. |
| Sor kulcsa |A sor kulcsot tartalmazó kimeneti oszlop neve. A sor kulcsa a partíción belüli entitás egyedi azonosítója. Az entitás elsődleges kulcsának második részét képezi. A sor kulcsa egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. |
| Köteg mérete |A Batch-művelet rekordjainak száma. Az alapértelmezett érték (100) elegendő a legtöbb feladathoz. A beállítás módosításával kapcsolatos további információkért tekintse meg a [tábla batch műveletének specifikációját](/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

## <a name="partitioning"></a>Particionálás

A partíciós kulcs bármely kimeneti oszlop. A kimeneti írók száma követi a bemeneti particionálást a [teljesen párhuzamos lekérdezéseknél](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Az üzenetek maximális méretével kapcsolatban lásd: az [Azure Storage korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Az alapértelmezett érték a 100 entitások száma egyetlen tranzakcióban, de igény szerint kisebb értékre is konfigurálható.

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)