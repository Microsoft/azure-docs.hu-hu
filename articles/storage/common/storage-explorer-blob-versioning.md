---
title: Azure Storage Explorer blob verziószámozási útmutatója | Microsoft Docs
description: BLOB verziószámozási útmutatója Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783827"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure Storage Explorer blob verziószámozási útmutatója

Microsoft Azure Storage Explorer a blob-verziók egyszerű elérését és kezelését teszi lehetővé. Ez az útmutató segít megérteni, hogyan működik a blob verziószámozása Storage Explorerban. A folytatás előtt javasoljuk, hogy olvassa el a [blob verziószámozásával](../blobs/versioning-overview.md)kapcsolatos további tudnivalókat.

## <a name="terminology"></a>Terminológia

Ez a szakasz néhány definíciót tartalmaz, amelyek segítenek megérteni a használatot ebben a cikkben.

- Soft DELETE: egy alternatív automatikus adatvédelmi funkció. További információ a [Soft delete](../blobs/soft-delete-blob-overview.md)szolgáltatásról.
- Aktív blob: A blob vagy A blob verziója aktív állapotban jön létre. Csak aktív állapotban lévő blobokon vagy blob-verziókon lehet működni.
- Soft-Deleted blob: A blob vagy A blob verziója nem törölhető. A Soft-Deleted Blobok csak a megőrzési időszakra érvényesek.
- BLOB verziója: engedélyezve lett A blob verziószámozásával létrehozott blob. Minden blob-verzió egy verzióazonosító-AZONOSÍTÓval van társítva.
- Aktuális verzió: a blob verziója aktuális verzióként van megjelölve.
- Előző verzió: a blob olyan verziója, amely nem az aktuális verzió.
- Nem verziójú blob: A blob verziószámozásával létrehozott blob le van tiltva. A nem verziójú Blobok nem rendelkeznek verzióazonosító-AZONOSÍTÓval.

## <a name="view-blob-versions"></a>BLOB-verziók megtekintése

A Storage Explorer négy különböző nézetet támogat a Blobok megjelenítéséhez.

| Nézet | Aktív nem verziójú Blobok | Soft-Deleted nem Version Blobok | BLOB-verziók |
| ---- | :----------: | :-----------: | :------------------: |
| Aktív Blobok | Igen | Nem | Csak a jelenlegi verzió |
| Aktív blobok és nem törölt Blobok | Igen | Yes | Csak a jelenlegi verzió |
| Aktív blobok és Blobok az aktuális verzió nélkül | Igen | Nem | Aktuális verzió vagy legújabb aktív verzió |
| Minden blob és blob a jelenlegi verzió nélkül | Igen | Yes | Aktuális verzió vagy legújabb verzió |

### <a name="active-blobs"></a>Aktív Blobok

Ebben a nézetben Storage Explorer a következőt jeleníti meg:

- Aktív nem verziójú Blobok
- Aktuális verziók

### <a name="active-blobs-and-soft-deleted-blobs"></a>Aktív blobok és nem törölt Blobok

Ebben a nézetben Storage Explorer a következőt jeleníti meg:

- Aktív nem verziójú Blobok
- Soft-Deleted nem Version Blobok
- Aktuális verziók.

### <a name="active-blobs-and-blobs-without-current-version"></a>Aktív blobok és Blobok az aktuális verzió nélkül

Ebben a nézetben Storage Explorer a következőt jeleníti meg:

- Aktív nem verziójú Blobok
- Aktuális verziók
- Legutóbbi aktív korábbi verziók. 

Olyan Blobok esetében, amelyeknek nincs aktuális verziója, de aktív korábbi verziójúak, Storage Explorer az adott blob ábrázolásával megjeleníti a legutóbbi aktív előző verziót.

### <a name="all-blobs-and-blobs-without-current-version"></a>Minden blob és blob a jelenlegi verzió nélkül

Ebben a nézetben Storage Explorer a következőt jeleníti meg:

- Aktív nem verziójú Blobok
- Soft-Deleted nem Version Blobok
- Aktuális verziók
- Legutóbbi korábbi verziók. 

Az aktuális verzióval nem rendelkező Blobok esetében a Storage Explorer megjeleníti a legutóbbi korábbi verziót az adott blob ábrázolásával.

> [!Note]
> A szolgáltatás korlátozása miatt Storage Explorer további feldolgozásra van szükség ahhoz, hogy a virtuális könyvtárak hierarchikus képet kapjanak a blob-verziók listázásakor. A Blobok listázása hosszabb ideig tart a következő nézetekben:
> 
> - Aktív blobok és Blobok az aktuális verzió nélkül
> - Minden blob és blob a jelenlegi verzió nélkül

## <a name="manage-blob-versions"></a>BLOB-verziók kezelése

### <a name="view-versions-of-a-blob"></a>BLOB verzióinak megtekintése

A Storage Explorer a Blobok összes verziójának megtekintéséhez a **verziók kezelése** parancsot biztosítja. A blob verzióinak megtekintéséhez válassza ki azt a blobot, amelyen meg szeretné tekinteni a verzióit, és válassza a **kezelés előzmények &rarr; kezelése** elemet az eszköztárból vagy a helyi menüből.

### <a name="download-blob-versions"></a>BLOB-verziók letöltése

Egy vagy több blob-verzió letöltéséhez jelölje ki a letölteni kívánt blob-verziókat, és válassza a **Letöltés** lehetőséget az eszköztárból vagy a helyi menüből.

Ha egy blob több verzióját tölti le, a letöltött fájloknak a fájlnevük elején lesznek a verziószámuk.

### <a name="delete-blob-versions"></a>BLOB-verziók törlése

Egy vagy több blob-verzió törléséhez válassza ki a törölni kívánt blob-verziókat, és válassza a **Törlés** lehetőséget az eszköztárból vagy a helyi menüből.

A blob-verziókra a Soft-delete szabályzat vonatkozik. Ha a helyreállítható törlés engedélyezve van, a blob-verziók törlésre kerülnek. Egy speciális eset egy aktuális verzió törlése. Ha egy aktuális verziót töröl, az automatikusan aktív korábbi verzió lesz.

### <a name="promote-blob-version"></a>BLOB verziójának előléptetése

A blob tartalmát visszaállíthatja egy korábbi verziónak a jelenlegi verzióra való előléptetésével. Válassza ki az előléptetni kívánt blob-verziót, és válassza a **verzió előléptetése** lehetőséget az eszköztárból vagy a helyi menüből.

A nem verziójú blobokat az előléptetett blob verziója felülírja. Győződjön meg arról, hogy a művelet megerősítése előtt már nincs szüksége az adatvédelemre vagy az adatbiztonsági mentésre. Az aktuális verziók automatikusan korábbi verziók lesznek, ezért Storage Explorer nem kér megerősítést.

### <a name="undelete-blob-version"></a>BLOB verziójának törlésének visszavonása

A blob verzióit nem lehet külön törölni. A törlést egyszerre kell törölni. Egy blob összes blob-verziójának törléséhez válassza ki a blob egyik verziójának bármelyikét, és válassza a kijelölés **törlésének** visszavonása lehetőséget az eszköztárból vagy a helyi menüből.

### <a name="change-access-tier-of-blob-versions"></a>BLOB-verziók hozzáférési szintjeinek módosítása

Minden blob-verzió saját hozzáférési szintű. A blob-verziók hozzáférési szintjének módosításához válassza ki a blob-verziókat, amelyeken módosítani szeretné a hozzáférési szintet, majd válassza a **hozzáférési szintek módosítása** lehetőséget a helyi menüből.

## <a name="see-also"></a>Lásd még:

* [BLOB verziószámozása](../blobs/versioning-overview.md)
* [Blobok helyreállítható törlése](../blobs/soft-delete-blob-overview.md)