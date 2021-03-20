---
title: Az Azure Table Storage tervezése adatmódosításra | Microsoft Docs
description: Tervezze meg az Azure Table Storage adatmódosítási táblázatait. A beszúrási, frissítési és törlési műveletek optimalizálása. Gondoskodjon a tárolt entitások konzisztenciájáról.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88236760"
---
# <a name="design-for-data-modification"></a>Tervezés adatmódosításhoz
Ez a cikk a lapkák, frissítések és törlések optimalizálására vonatkozó tervezési szempontokat ismerteti. Bizonyos esetekben ki kell értékelnie a kiértékelést olyan formatervezési minták között, amelyek optimalizálják az adatmódosításra optimalizált kialakításokat, ugyanúgy, mint a viszonyítási adatbázisok tervezésekor (bár a tervezési kompromisszumok kezelésének módszerei eltérőek a kapcsolati adatbázisokban). A tábla kialakítási mintái a Table service részletes tervezési mintáit ismertetik, és kiemelik ezeket a kompromisszumokat. A gyakorlatban azt tapasztalja, hogy számos, az entitások lekérdezésére optimalizált terv is jól működik az entitások módosításához.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>A beszúrási, frissítési és törlési műveletek teljesítményének optimalizálása
Entitások frissítéséhez vagy törléséhez a **PartitionKey** és a **RowKey** értékeket kell tudnia azonosítani. Ebben a tekintetben az entitások módosítására választott **PartitionKey** és **RowKey** a hasonló feltételeket kell követnie, hogy támogassa a pontok lekérdezéseit, mert az entitásokat a lehető leghatékonyabb módon szeretné azonosítani. Nem érdemes nem hatékony partíciót vagy táblázatot keresni, hogy megkeresse az entitást, hogy felderítse a frissíteni vagy törölni kívánt **PartitionKey** és **RowKey** értékeket.  

A következő minták a táblázat tervezési mintái című szakasza a teljesítmény vagy az INSERT, Update és DELETE műveletek optimalizálása érdekében:  

* [Nagy mennyiségű törlési minta](table-storage-design-patterns.md#high-volume-delete-pattern) – engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblában. az entitásokat a tábla törlésével törölheti.  
* [Adatsorozat-minta](table-storage-design-patterns.md#data-series-pattern) – a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  
* [Széles entitások mintája](table-storage-design-patterns.md#wide-entities-pattern) – több fizikai entitást használhat a több mint 252 tulajdonságú logikai entitások tárolására.  
* [Nagyméretű entitások mintája](table-storage-design-patterns.md#large-entities-pattern) – a blob Storage használata nagy tulajdonságértékek tárolására.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Konzisztencia biztosítása a tárolt entitásokban
A másik kulcsfontosságú tényező, amely hatással van az adatmódosítások optimalizálására kiválasztott kulcsokra, az Atomic Transactions használatával biztosítható a konzisztencia. A EGT csak akkor használhatók, ha ugyanazon a partíción tárolt entitásokon működnek.  

A cikk [tábla kialakítási mintái](table-storage-design-patterns.md) a konzisztencia kezelése:  

* [Partíción belüli másodlagos index minta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző **RowKey** -értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek eltérő **RowKey** -értékek használatával történő engedélyezéséhez.  
* [Partíciók közötti másodlagos index minta](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző RowKey-értékekkel külön partíciókban vagy külön táblákban, hogy a gyors és hatékony keresési és alternatív rendezési sorrendet a különböző **RowKey** értékek használatával engedélyezze.  
* [Végül konzisztens tranzakciós minta](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) – az Azure Queues használatával lehetővé válik a partíciós határok vagy a tárolási rendszerek határainak végül konzisztens viselkedésének engedélyezése.
* [Entitások indexelése](table-storage-design-patterns.md#index-entities-pattern) – az indexelési entitások megtartása az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  
* [Denormalizálás mintája](table-storage-design-patterns.md#denormalization-pattern) – a kapcsolódó adategységeket egyetlen entitásban kombinálva lehetővé teszi az összes szükséges adat lekérését egyetlen pont lekérdezéssel.  
* [Adatsorozat-minta](table-storage-design-patterns.md#data-series-pattern) – a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  

További információ az Entity Transactions szolgáltatással kapcsolatban: [Entity Transactions (entitások csoportosítása](table-storage-design.md#entity-group-transactions)) szakasz.  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Győződjön meg arról, hogy a hatékony módosítások lehetővé teszik a tervezést a hatékony lekérdezésekhez
Sok esetben a hatékony lekérdezések kialakítása a hatékony módosítások eredményét eredményezi, azonban mindig ki kell értékelnie, hogy ez a helyzet az adott forgatókönyv esetén. A cikk [tábla kialakítási mintáinak](table-storage-design-patterns.md) néhány mintája explicit módon értékeli ki az entitások lekérdezése és módosítása közötti kompromisszumokat, és mindig vegye figyelembe az egyes típusú műveletek számát.  

A cikk [tábla kialakítási mintái](table-storage-design-patterns.md) a következő mintákat ismertetik a hatékony lekérdezések tervezése és a hatékony adatmódosítás kialakítása között:  

* [Összetett kulcs mintázata](table-storage-design-patterns.md#compound-key-pattern) – az összetett **RowKey** értékek használatával lehetővé teheti, hogy az ügyfél egyetlen pont lekérdezéssel keresse a kapcsolódó adatait.  
* [Naplóbeli farok minta](table-storage-design-patterns.md#log-tail-pattern) – a partícióhoz legutóbb hozzáadott *n* entitások beolvasása egy **RowKey** értékkel, amely fordított dátum és idő sorrendbe rendezi.  
