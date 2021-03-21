---
title: A Table Storage bemutatása – Objektumtárolás az Azure-ban | Microsoft Docs
description: Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 01/07/2021
ms.subservice: tables
ms.openlocfilehash: 292104651c6bb9b63e9d8cbe26fea2bf8c6acbf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027740"
---
# <a name="what-is-azure-table-storage-"></a>Mi az Azure Table Storage? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Az Azure Table Storage egy olyan szolgáltatás, amely a felhőben nem összehasonlítható strukturált (más néven strukturált NoSQL-) adattárakat tárol, és egy kulcs-és attribútum-tárolót biztosít a séma nélküli kialakításhoz. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Következő lépések

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [Az Azure Table Storage használatának első lépései a .NET-ben](../../cosmos-db/tutorial-develop-table-dotnet.md)

* A Table Service elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:

    * [A Storage ügyféloldali kódtára a .NET-hez – referencia](/dotnet/api/overview/azure/storage)

    * [REST API-referencia](/rest/api/storageservices/)