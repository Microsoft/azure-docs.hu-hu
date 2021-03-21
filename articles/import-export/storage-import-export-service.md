---
title: Azure import/export használata az Azure Storage-ba irányuló és onnan érkező adatok átvitelére | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási és exportálási feladatokat a Azure Portal az Azure Storage-ba irányuló és onnan érkező adatok átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b1f1560fc1a00577e1e4b30d922fc7d4cae0ab92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181842"
---
# <a name="what-is-azure-importexport-service"></a>Mi az az Azure import/export szolgáltatás?

Az Azure import/export szolgáltatás használatával nagy mennyiségű adatmennyiséget importálhat az Azure Blob Storage-ba, és Azure Files a lemezmeghajtók Azure-adatközpontba való szállításával. Ez a szolgáltatás az adatoknak az Azure Blob-tárolóból lemezmeghajtókra való átvitelére, majd saját helyszíni helyeire való eljuttatására is használható. Egy vagy több lemezmeghajtóról származó adatok az Azure Blob Storage-ba vagy a Azure Filesba importálhatók.

Adja meg saját lemezmeghajtóit, és vigye át az adatait az Azure import/export szolgáltatással. Használhatja a Microsoft által biztosított lemezmeghajtókat is.

Ha a Microsoft által biztosított lemezmeghajtók használatával szeretne adatátvitelt alkalmazni, a [Azure Data Box Disk](../databox/data-box-disk-overview.md) használatával importálhatja az Azure-ba az adatok importálását. A Microsoft legfeljebb 5 titkosított SSD-meghajtót (SSD-t) üzemeltet, amely az adatközpontban egy regionális szolgáltatón keresztül 40 TB-os teljes kapacitással rendelkezik. Gyorsan konfigurálhatja a lemezmeghajtókat, átmásolhatja az adatlemezeket a lemezmeghajtóra USB 3,0-kapcsolaton keresztül, és visszahelyezheti a lemezmeghajtókat az Azure-ba. További információ: [Azure Data Box Disk Overview (áttekintés](../databox/data-box-disk-overview.md)).

## <a name="azure-importexport-use-cases"></a>Azure-beli importálási/exportálási használati esetek

Az Azure import/export szolgáltatást akkor érdemes használni, ha az adatfeltöltés vagy a hálózaton keresztüli Letöltés túl lassú, vagy további hálózati sávszélességet igényel. Használja ezt a szolgáltatást a következő esetekben:

* **Adatáttelepítés a felhőbe**: nagy mennyiségű, gyorsan és költséghatékonyan áthelyezhető adatforgalom az Azure-ba.
* **Tartalom terjesztése**: gyorsan küldhet adatokat az ügyfelek webhelyeire.
* **Biztonsági mentés**: készítsen biztonsági másolatot a helyszíni adatairól az Azure Storage-ban való tároláshoz.
* **Adatok helyreállítása**: a tárterületen tárolt nagy mennyiségű adatok helyreállítása, és a helyszíni helyre való szállítása.

## <a name="importexport-components"></a>Importálási/exportálási összetevők

Az import/export szolgáltatás a következő összetevőket használja:

* **Importálási/exportálási szolgáltatás**: a Azure Portalben elérhető szolgáltatás segít a felhasználónak adatimportálási (feltöltési) és exportálási (letöltési) feladatok létrehozásában és nyomon követésében.  

* **WAImportExport eszköz**: ez egy parancssori eszköz, amely a következő műveleteket végzi el:
  * Előkészíti az importálásra szállított lemezmeghajtókat.
  * Megkönnyíti az adatok másolását a meghajtóra.
  * Titkosítja a meghajtón lévő, AES 256 bites BitLocker-alapú titkosítást. A BitLocker-kulcs védelme érdekében külső kulcstartót használhat.
  * Létrehozza az importálás létrehozásakor használt meghajtó-napló fájljait.
  * Segít az exportálási feladatokhoz szükséges meghajtók számának azonosításában.

> [!NOTE]
> Az WAImportExport eszköz két, 1. és 2. verzióban érhető el. Javasoljuk, hogy a következőket használja:
>
> * 1. verzió az Azure Blob Storage-ba való importáláshoz/exportáláshoz.
> * 2. verzió az adatimportáláshoz az Azure Files szolgáltatásba.
>
> A WAImportExport eszköz csak 64 bites Windows operációs rendszerrel kompatibilis. A támogatott operációsrendszer-verziók esetében lépjen az [Azure import/export követelményekre](storage-import-export-requirements.md#supported-operating-systems).

* **Lemezmeghajtók**: SSD-vagy merevlemez-meghajtók (HDD-k) szállítása az Azure-adatközpontba. Importálási feladatok létrehozásakor az adatait tartalmazó lemezmeghajtók szállítása. Exportálási feladatok létrehozásakor a rendszer üres meghajtókat szállít az Azure-adatközpontba. Adott típusú lemezek esetében válassza a [támogatott lemez típusok](storage-import-export-requirements.md#supported-hardware)lehetőséget.

## <a name="how-does-importexport-work"></a>Hogyan működik az Import/Export funkció?

Az Azure import/export szolgáltatás lehetővé teszi az adatátvitelt az Azure-Blobokra és Azure Files a feladatok létrehozásával. Feladatok létrehozásához használjon Azure Portal vagy Azure Resource Manager REST API. Mindegyik feladattípus egyetlen Storage-fiókhoz van társítva.

A feladatok importálhatók és exportálhatók. Az importálási feladatok lehetővé teszik az adatok importálását az Azure-Blobokra vagy az Azure-fájlokra, míg az exportálási feladatok lehetővé teszik az adatok Azure-Blobokból való exportálását. Importálási feladatokhoz az adatait tartalmazó meghajtókat szállíthatja. Exportálási feladatok létrehozásakor a rendszer üres meghajtókat szállít egy Azure-adatközpontba. Minden esetben akár 10 lemezmeghajtót is elhelyezhet a feladatok végrehajtásához.

### <a name="inside-an-import-job"></a>Importálási feladatokon belül

Magas szinten az importálási feladatok az alábbi lépéseket foglalják magukban:

1. Határozza meg az importálandó és az Azure Storage-beli adattárolóban tárolt adatmennyiségeket, a szükséges meghajtók számát.
2. A WAImportExport eszköz használatával másolhat adatlemez-meghajtókat. Titkosítsa a lemezmeghajtókat a BitLockerrel.
3. Hozzon létre egy importálási feladatot a célként szolgáló Storage-fiókban Azure Portal. Töltse fel a meghajtó-napló fájljait.
4. Adja meg a visszaküldési címet és a szállítmányozó számlaszámát a meghajtók visszaszállításához.
5. A lemezmeghajtók szállítása a feladatok létrehozása során megadott szállítási címnek.
6. Frissítse a kézbesítési nyomon követési számot az importálási feladatok részletei között, és küldje el az importálási feladatot.
7. A rendszer az Azure-adatközpontban fogadja és dolgozza fel a meghajtókat.
8. A meghajtókat az importálási feladatokban megadott visszaküldési címnek a Carrier-fiókjával kell továbbítani.

> [!NOTE]
> A helyi (az adatközpont országa/régiója) szállításai esetében ossza meg a belföldi fuvarozó fiókját.
>
> Külföldön (az adatközpont országa/régióján kívül) beszállítások esetén ossza meg a nemzetközi szolgáltatói fiókokat.

 ![1. ábra: a feladathoz tartozó folyamat importálása](./media/storage-import-export-service/import-job.png)

Az adatimportálás részletes utasításait a következő témakörben találhatja meg:

* [Az Azure-Blobok importálása](storage-import-export-data-to-blobs.md)
* [Adatimportálás az Azure Filesba](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Exportálási feladatokon belül

> [!IMPORTANT]
> A szolgáltatás csak az Azure-Blobok exportálását támogatja. Az Azure Files exportálása nem támogatott.

Az exportálási feladatok magas szinten az alábbi lépéseket foglalják magukban:

1. Határozza meg az exportálni kívánt adatait, a szükséges meghajtók számát, a forrás blobokat vagy a tároló elérési útját a blob Storage-ban.
2. Hozzon létre egy exportálási feladatot a forrásként szolgáló Storage-fiókban Azure Portal.
3. A forrás Blobok vagy a tároló elérési útjának megadása az exportálandó adatforrásokhoz.
4. Adja meg a visszaküldési címet és a szállítmányozó számlaszámát a meghajtók visszaszállításához.
5. A lemezmeghajtók szállítása a feladatok létrehozása során megadott szállítási címnek.
6. Frissítse a kézbesítési nyomon követési számot az exportálási feladatok részletei között, és küldje el az exportálási feladatot.
7. A rendszer az Azure-adatközpontban fogadja és dolgozza fel a meghajtókat.
8. A meghajtók titkosítva vannak a BitLocker szolgáltatással, és a kulcsok a Azure Portalon keresztül érhetők el.  
9. A meghajtókat az importálási feladatokban megadott visszaküldési címnek a Carrier-fiókjával kell továbbítani.

> [!NOTE]
> A helyi (az adatközpont országa/régiója) szállításai esetében ossza meg a belföldi fuvarozó fiókját.
>
> Külföldön (az adatközpont országa/régióján kívül) beszállítások esetén ossza meg a nemzetközi szolgáltatói fiókokat.
  
 ![2. ábra: a feladatok folyamatának exportálása](./media/storage-import-export-service/export-job.png)

Az adatok exportálásának lépésenkénti utasításait az [adatok exportálása Azure-blobokból](storage-import-export-data-from-blobs.md)című témakörben találhatja meg.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure import/export szolgáltatás lehetővé teszi az adatok másolását az összes Azure Storage-fiókba. Lemezmeghajtókat a felsorolt helyekre is kiszállíthat. Ha a Storage-fiókja olyan Azure-helyen található, amely itt nincs meghatározva, akkor a rendszer egy másik kézbesítési helyet biztosít a feladatok létrehozásakor.

### <a name="supported-shipping-locations"></a>Támogatott szállítási helyszínek

|Ország/régió  |Ország/régió  |Ország/régió  |Ország/régió  |
|---------|---------|---------|---------|
|USA keleti régiója    | Észak-Európa        | Közép-India        |US Gov Iowa         |
|USA nyugati régiója     |Nyugat-Európa         | Dél-India        | US DoD – Kelet        |
|USA 2. keleti régiója    | Kelet-Ázsia        |  Nyugat-India        | US DoD – Középső régió        |
|USA 2. nyugati régiója     | Délkelet-Ázsia        | Közép-Kanada        | Kelet-Kína         |
|Az USA középső régiója     | Kelet-Ausztrália        | Kelet-Kanada        | Észak-Kína        |
|USA északi középső régiója     |  Délkelet-Ausztrália       | Dél-Brazília        | Az Egyesült Királyság déli régiója        |
|USA déli középső régiója     | Nyugat-Japán        |Dél-Korea középső régiója         | Közép-Németország        |
|USA nyugati középső régiója     |  Kelet-Japán       | USA-beli államigazgatás – Virginia        | Északkelet-Németország        |
|Dél-Afrika nyugati régiója   |  Dél-Afrika északi régiója |

## <a name="security-considerations"></a>Biztonsági szempontok

A meghajtón lévő adatai titkosítva vannak az AES 256 bites BitLocker meghajtótitkosítás használatával. Ez a titkosítás védi az adatait, amíg az átvitel közben van.

Az importálási feladatok esetében a meghajtók két módon lesznek titkosítva.  

* A WAImportExport eszköz a meghajtó előkészítése során való futtatásakor *dataset.csv* fájl használatakor válassza a beállítást.

* Engedélyezze a BitLocker-titkosítást manuálisan a meghajtón. A meghajtó előkészítése során a WAImportExport eszköz parancssorának futtatásakor a *driveset.csv* titkosítási kulcsát kell megadnia. A BitLocker titkosítási kulcsa a külső kulcstartó (más néven a Microsoft által felügyelt kulcs) vagy egy ügyfél által felügyelt kulcs használatával tovább védhető. További információkért lásd: Hogyan lehet [ügyfél által felügyelt kulcsot használni a BitLocker-kulcs védeleméhez](storage-import-export-encryption-key-portal.md).

Az exportálási feladatok esetében az adatoknak a meghajtókra való másolása után a szolgáltatás a BitLocker használatával titkosítja a meghajtót, mielőtt a rendszer visszaküldi azt. A titkosítási kulcsot a Azure Portalon keresztül kapja meg. A meghajtót fel kell oldani a WAImporExport eszközzel a kulcs használatával.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Díjszabás

**A meghajtó kezelési díja**

Az importálási vagy exportálási feladatok részeként feldolgozható egyes meghajtók esetében a meghajtó kezelése díjköteles. Tekintse meg az [Azure importálási/exportálási díjszabásának](https://azure.microsoft.com/pricing/details/storage-import-export/)részleteit.

**Szállítási költségek**

Ha meghajtókat szállít az Azure-ba, a szállítási költséget a szállítási szolgáltatónak kell megfizetnie. Ha a Microsoft visszaadja a meghajtókat, a szállítási költség arra a hordozófrekvencia-fiókra kerül, amelyet a feladatok létrehozásakor adott meg.

**Tranzakciós költségek**

A [standard szintű tárolási tranzakciós díj](https://azure.microsoft.com/pricing/details/storage/) az importálás során és az adatexportálás során is érvényes. Az Azure Storage-ból származó adatok exportálásakor a standard szintű kimenő költségek is érvényesek a tárolási tranzakciós költségekkel együtt. A kimenő költségekkel kapcsolatos további információkért lásd az [adatátviteli díjszabást.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja az import/export szolgáltatást a következőhöz:

* [Az Azure-Blobok importálása](storage-import-export-data-to-blobs.md)
* [Adatok exportálása az Azure-Blobokból](storage-import-export-data-from-blobs.md)
* [Az Azure Filesba való adatimportálás](storage-import-export-data-to-files.md)
