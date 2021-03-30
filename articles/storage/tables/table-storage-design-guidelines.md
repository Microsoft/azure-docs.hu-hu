---
title: Útmutató az Azure Storage Table designhoz | Microsoft Docs
description: Az olvasási és írási műveletek hatékony támogatásához tekintse át az Azure Storage Table szolgáltatás megtervezésével kapcsolatos irányelveket.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236318"
---
# <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez

Az Azure Storage Table szolgáltatással való használatra szolgáló táblák tervezése nagyon különbözik a kapcsolódó adatbázisok kialakítási szempontjaitól. Ez a cikk útmutatást nyújt a Table service megoldás hatékony és írási eredményének megtervezéséhez.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Tervezze meg Table service-megoldását olvasásra és hatékonyságra

* ***Az olvasási és a nagy teljesítményű alkalmazásokban való lekérdezés tervezése.*** A táblázatok tervezésekor gondolja át a lekérdezéseket (különösen a késési különbségeket), amelyeket a rendszer az entitások frissítésének megkezdése előtt végrehajt. Ez általában egy hatékony és nagy teljesítményű megoldás eredményét eredményezi.  
* ***Adja meg a PartitionKey és a RowKey is a lekérdezésekben.** _ _Point lekérdezések * például ezek a leghatékonyabb Table Service-lekérdezések.  
* ***Érdemes lehet az entitások duplikált példányait tárolni.*** A Table Storage olcsó, ezért a hatékonyabb lekérdezések lehetővé tételéhez ugyanazt az entitást többször kell tárolni (különböző kulcsokkal).  
* ***Érdemes lehet az adatai denormalizálása.*** A Table Storage olcsó, ezért érdemes lehet az adatai denormalizálása. Tárolhatja például az összegző entitásokat, hogy az összesített adatlekérdezések csak egyetlen entitáshoz férhessenek hozzá.  
* ***Használjon összetett kulcsos értékeket.** _ Az egyetlen kulcs, amelyet a _ *PartitionKey** és a **RowKey**. Használjon például összetett kulcsos értékeket az entitásokhoz való helyettesítő hozzáférési útvonalak engedélyezéséhez.  
* ***Lekérdezési leképezés használata.*** Csökkentheti a hálózaton keresztül átvitt adatok mennyiségét olyan lekérdezések használatával, amelyek csak a szükséges mezőket választják ki.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Tervezze meg Table service-megoldását, hogy az írható legyen  

* ***Ne hozzon létre forró partíciót.*** Válassza ki azokat a kulcsokat, amelyek lehetővé teszik a kérések több partíción keresztüli terjesztését egy adott időpontban.  
* ***Kerülje a forgalomban lévő tüskéket.*** A forgalmat egy ésszerű időn belül zökkenőmentesen, és elkerülje a forgalomban fellépő tüskéket.
* ***Ne feltétlenül hozzon létre külön táblát az egyes entitások típusaihoz.*** Ha az entitások típusai között atomi tranzakciókat igényel, akkor a több entitás típusát ugyanabban a partícióban tárolhatja ugyanabban a táblában.
* ***Gondolja át, hogy mekkora átviteli sebességet kell elérnie.*** Tisztában kell lennie a Table service skálázhatósági céljaival, és gondoskodnia kell arról, hogy a kialakítás ne lépje túl őket.  

Az útmutató elolvasása során olyan példákat talál, amelyek az összes alapelvet bemutatják a gyakorlatban. 

## <a name="next-steps"></a>Következő lépések

- [Táblatervezési minták](table-storage-design-patterns.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Tábla adatai titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
