---
title: BLOB Storage-teljesítmény szintjeinek letiltása – Azure Storage
description: A prémium és a standard szintű teljesítményszint közötti különbséget ismerteti az Azure Block blob Storage-hoz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: cf0b5d29e0dc375a07fe024ef0763c8200275055
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880708"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Blokkblobtárolás teljesítményszintjei

Mivel a vállalatok teljesítményre érzékeny felhőalapú alkalmazásokat helyeznek üzembe, fontos, hogy a költséghatékony adattárolási lehetőségek különböző teljesítményszint esetén is elérhetők legyenek.

Az Azure Block blob Storage két különböző teljesítményszint kínál:

- **Prémium**: nagy tranzakciós díjszabásra és egyszámjegyű, konzisztens tárolási késésre optimalizált
- **Standard**: nagy kapacitású és magas átviteli sebességre optimalizált

A következő szempontokat kell figyelembe venni a különböző teljesítményszint esetén:

| Terület |Standard teljesítmény  |Prémium szintű teljesítmény  |
|---------|---------|---------|
|Régiónkénti elérhetőség     |   Minden régió      | A [régiók kiválasztása](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Támogatott [Storage-fiókok típusai](../common/storage-account-overview.md#types-of-storage-accounts)     |     Általános célú v2, BlobStorage, általános célú v1    |    BlockBlobStorage     |
|[Nagy teljesítményű blokkos Blobok](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) támogatása     |    Igen, 4 MiB-PutBlock vagy PutBlob-méretnél nagyobb méretben     |    Igen, 256 KiB-nál nagyobb PutBlock-vagy PutBlob-méretek    |
|Redundancia     |     Lásd: [Storage-fiókok típusai](../common/storage-account-overview.md#types-of-storage-accounts)   |  A jelenleg csak a helyileg redundáns tárolást (LRS) és a redudant tárolót (ZRS) támogatja<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> A zóna-redundáns tárolás (ZRS) a prémium szintű teljesítmény-blokkoló blob Storage-fiókok régió kiválasztása területén érhető el.</div>

A költségekkel kapcsolatban a prémium szintű teljesítmény a nagy tranzakciós díjszabású alkalmazások optimalizált díjszabását nyújtja, így csökkentheti a számítási feladatokhoz szükséges [összes tárolási költséget](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) .

## <a name="premium-performance"></a>Prémium szintű teljesítmény

A prémium szintű teljesítmény-blokkoló blob Storage nagy teljesítményű hardveren keresztül teszi elérhetővé az adattárolást. Az adatok tárolása SSD-meghajtókon történik, amelyek alacsony késésre vannak optimalizálva. Az SSD-k nagyobb átviteli sebességet biztosítanak, mint a hagyományos merevlemezek.

A prémium szintű tárterület ideális olyan munkaterhelésekhez, amelyek gyors és konzisztens válaszidőt igényelnek. A legjobb a sok kis tranzakciót végző munkaterhelések esetében. A számítási feladatok például a következők:

- **Interaktív munkaterhelések**. Ezeknek a számítási feladatoknak azonnali frissítésre és felhasználói visszajelzésre van szükségük, például az e-kereskedelmi és a leképezési alkalmazásokhoz. Egy e-kereskedelmi alkalmazásban például a ritkábban megtekintett elemek valószínűleg nem lesznek gyorsítótárazva. Ezeket azonban azonnal meg kell jeleníteni az ügyfél számára igény szerint.

- **Elemzés**. Egy IoT-forgatókönyvben sok kisebb írási műveletet lehet leküldeni a felhőbe másodpercenként. A rendszer nagy mennyiségű adattal is rendelkezhet, összesítve elemzés céljára, majd szinte azonnal törölve. A prémium blokkos blob Storage nagy betöltési képességeinek köszönhetően az ilyen típusú számítási feladatok hatékonyan használhatók.

- **Mesterséges intelligencia/gépi tanulás (AI/ml)**. Az AI/ML különféle adattípusok, például vizualizációk, beszédek és szövegek felhasználását és feldolgozását tárgyalja. Ez a nagy teljesítményű számítási feladatok olyan nagy mennyiségű adatot biztosítanak, amely gyors reagálást és hatékony betöltési időt igényel az adatok elemzéséhez.

- **Adatátalakítás**. Az adatszerkesztést, módosítást és átalakítást igénylő folyamatoknak azonnali frissítésre van szükségük. A pontos adatábrázoláshoz ezeknek az információknak a felhasználóknak azonnal tükröznie kell a módosításokat.

## <a name="standard-performance"></a>Standard teljesítmény

A standard szintű teljesítmény a különböző [hozzáférési szinteket](storage-blob-storage-tiers.md) támogatja az adattárolásra a legköltséghatékonyabb módon. A nagyméretű adathalmazok nagy kapacitásának és nagy teljesítményének növelésére van optimalizálva.

- **Biztonsági mentési és vész-helyreállítási adatkészletek**. A standard szintű tárterület költséghatékony szintet kínál, így tökéletes használatot biztosít mind a rövid, mind a hosszú távú vész-helyreállítási adatkészletekhez, a másodlagos biztonsági mentésekhez és a megfelelőségi adatarchiváláshoz.

- **Médiatartalom**. A képek és a videók gyakran az első létrehozás és tárolás során gyakran érhetők el, de ezt a tartalomtípust ritkábban használják, mint a régebbiek. A standard szintű tárterület a média tartalmának megfelelő kapacitást biztosít. 

- **Tömeges adatfeldolgozás**. Ezek a számítási feladatok alkalmasak a standard szintű tároláshoz, mivel a magas átviteli sebességű tárolást igénylik a következetes kis késés helyett. A nagyméretű, nyers adatkészletek feldolgozásra készülnek, és végül a hűvösebb szintre lesznek migrálva.

## <a name="migrate-from-standard-to-premium"></a>Migrálás a standard és a prémium között

A meglévő standard teljesítményű Storage-fiókok nem konvertálhatók prémium szintű teljesítményű blokk blob Storage-fiókra. A prémium szintű teljesítményű Storage-fiókba való átlépéshez létre kell hoznia egy BlockBlobStorage-fiókot, és át kell telepítenie az új fiókba. További információt a BlockBlobStorage- [fiók létrehozása](storage-blob-create-account-block-blob.md)című témakörben talál.

A Blobok Storage-fiókok közötti másolásához használhatja a [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) parancssori eszköz legújabb verzióját. Más eszközök, például a Azure Data Factory az adatáthelyezéshez és-átalakításhoz is elérhetők.

## <a name="blob-lifecycle-management"></a>BLOB-életciklus kezelése

A blob Storage életciklus-kezelése sokoldalú, szabályon alapuló szabályzatot kínál:

- **Prémium**: az életciklus végén lejáró adatmennyiség.
- **Standard**: az adattovábbítás a legjobb hozzáférési szintig, az életciklus végén pedig lejár az adatai.

További információ: [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md).

A prémium szintű blokk blob Storage-fiókban tárolt adatok nem helyezhetők át a gyakori, a ritka elérésű és az archív rétegek között. Egy blokk blob Storage-fiókból is másolhat blobokat egy *másik* fiókban lévő gyors elérési szinthez. Az adatok egy másik fiókba való másolásához használja a [put blokkot az URL](/rest/api/storageservices/put-block-from-url) API vagy a [AzCopy v10](../common/storage-use-azcopy-v10.md)használatával. A **put blokk from URL** API szinkron módon másolja az adatok a kiszolgálón. A hívás csak azt követően fejeződik be, hogy az összes adatok át lettek helyezve az eredeti kiszolgáló helyéről a célhelyre.

## <a name="next-steps"></a>További lépések

Értékelje ki a GPv2-és blob Storage-fiókokban a gyakori, ritka elérésű és archív archívumokat.

- [Tudnivalók a blob-adatok archiválási szintről való kiszárításáról](storage-blob-rehydration.md)
- [Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](./monitor-blob-storage.md)
- [Gyors, ritka és archív díjszabás a blob Storage-ban és a GPv2-fiókok régiónként](https://azure.microsoft.com/pricing/details/storage/)
- [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
