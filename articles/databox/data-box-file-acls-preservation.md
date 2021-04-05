---
title: Fájl ACL-ek, attribútumok és időbélyegek megőrzése Azure Data Box
description: Az ACL-eket, az időbélyegeket és az attribútumokat az Adatmásolás során az SMB-n keresztül Azure Data Box. Metaadatok másolása Windows-és Linux-alapú adatmásolási eszközökkel.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950312"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Fájl ACL-ek, attribútumok és időbélyegek megőrzése Azure Data Box

A Azure Data Box lehetővé teszi a hozzáférés-vezérlési listák (ACL-ek), időbélyegek és fájlattribútumok megőrzését az Azure-ba való adatküldés során. Ez a cikk azokat a metaadatokat ismerteti, amelyeket a rendszer az adatoknak a Server Message Block (SMB) használatával történő Data Box történő másolásakor továbbíthat a Azure Filesba való feltöltéshez. 

A metaadatok Windows-és Linux-alapú adatmásolási eszközökkel történő másolásához adott lépések vannak megadva. A metaadatok nem őrződnek meg az adatok blob Storage-ba való továbbításakor.

Ebben a cikkben a továbbított ACL-ek, időbélyegek és fájlattribútumok közösen *metaadatokként* vannak hivatkozva.

## <a name="transferred-metadata"></a>Átvitt metaadatok

A rendszer a következő metaadatokat továbbítja a Data Box Azure Filesba való feltöltésekor.

#### <a name="timestamps"></a>Időbélyegek

A következő időbélyegek átvitele történik:
- CreationTime (Létrehozás ideje)
- LastWriteTime

A következő időbélyeg nincs átadva:
- LastAccessTime
  
#### <a name="file-attributes"></a>Fájlattribútumok

Ha másként nincs megadva, a fájlok és könyvtárak fájljainak attribútumai is átkerülnek.

A következő fájlattribútumok átadása:
- FILE_ATTRIBUTE_READONLY (csak fájl)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (csak könyvtár)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (csak fájl)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

A következő fájlattribútumok nem lettek átadva:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
A címtárak írásvédett attribútumai nem kerülnek át.

#### <a name="acls"></a>ACL

A rendszer átmásolja és áthelyezi az összes olyan könyvtárat és fájlt, amelyet az SMB-re másol a Data Box. Az átvitelek közé tartozik a tulajdonosi hozzáférés-vezérlési listák (DACL) és a rendszerhozzáférés-vezérlési listák (ACL). A Linux esetében csak a Windows NT ACL-ek lesznek átadva.

Az ACL-eket nem továbbítja a rendszer az adatmásolatok során a hálózati fájlrendszer (ZEM) használatával, és ha az adatmásolási szolgáltatást használja az adatok átviteléhez. Az adatmásolási szolgáltatás közvetlenül a megosztásokból olvassa be az adatokat, és nem tudja olvasni az ACL-eket.

Még ha az adatmásolási eszköz nem másolja az ACL-eket, a rendszer a könyvtárak és fájlok alapértelmezett ACL-jeit Azure Files továbbítja. Az alapértelmezett ACL-ek rendelkeznek a beépített rendszergazdai fiókkal, a rendszerfiókkal és az SMB-megosztás felhasználói fiókjával, amely az adatData Boxban való csatlakoztatására és másolására szolgál.

Az ACL-ek a következő tulajdonságokkal rendelkező biztonsági leírókat tartalmazzák: ACL-ek, tulajdonos, csoport, hozzáférés-vezérlési listák.

Az ACL-ek átvitele alapértelmezés szerint engedélyezve van. Előfordulhat, hogy ezt a beállítást a Data Box helyi webes felhasználói felületén szeretné letiltani. További információ: [a helyi webes felhasználói felület használata a Data Box és Data Box Heavy felügyeletéhez](./data-box-local-web-ui-admin.md).

> [!NOTE]
> A feltételes hozzáférés-vezérlési (ACE) karakterláncokat tartalmazó ACL-eket tartalmazó fájlok nem másolhatók. Ez egy ismert probléma. Ennek megkerüléséhez másolja át ezeket a fájlokat a Azure Files megosztásba manuálisan a megosztás csatlakoztatásával, majd egy másolási eszköz használatával, amely támogatja az ACL-eket.

## <a name="copying-data-and-metadata"></a>Adatok és metaadatok másolása

Az ACL-ek, az időbélyegek és az adatokhoz tartozó attribútumok átviteléhez használja az alábbi eljárásokat az adatoknak a Data Boxba való másolásához. 

### <a name="windows-data-copy-tool"></a>Windows adatmásolási eszköz

Az adatData Box SMB-n keresztül történő másolásához használjon egy SMB-kompatibilis fájlmásolás eszközt, például: `robocopy` . Az alábbi parancs az összes fájlt és könyvtárat átmásolja, és az adatokkal együtt átadja a metaadatokat.

A vagy a kapcsoló használata esetén győződjön meg `/copyall` `/dcopy:DAT` arról, hogy a biztonsági mentési operátor szükséges jogosultságai nincsenek letiltva. További információ: [a helyi webes felhasználói felület használata a Data Box és Data Box Heavy felügyeletéhez](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

ahol

|Beállítás |Leírás |
|------------------- | ----- |
|`/copyall` |Az összes attribútum másolása.|
|`/e`      |Alkönyvtárak másolása, beleértve az üres könyvtárakat is.         |
|`/dcopy:DAT`  |Az Adatmásolás, az attribútumok és az időbélyegek másolása. Megjegyzés: a/DCOPY: DAT kapcsolót kell használni a címtárakban történő átvitelhez `CreationTime` . |
|`/r:3`    |A sikertelen másolatok esetében 3 újrapróbálkozást ad meg.         |
|`/w:60`   |Megadja az újrapróbálkozások közötti várakozási időt 60 másodpercben.         |
|`/is`     |A fájlok nevét tartalmazza.         |
|`/nfl`    |A nem naplózza a fájlneveket.         |
|`/ndl`    |A nem naplózza a címtár nevét.        |
|`/np`     |A nem jeleníti meg a másolási művelet állapotát.         |
|`/MT:32 or 64`  |Többszálú, 32 vagy 64 szálat használ.           |
|`/fft`    |Csökkenti az időbélyegző részletességét bármely fájlrendszernél.        |
|`/log+:<LogFile>`  |Hozzáfűzi a kimenetet a meglévő naplófájlhoz.|

További információ ezekről a `robocopy` paraméterekről [: oktatóanyag: adatok másolása Azure Data Box SMB-n keresztül](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Linux adatmásolási eszköz

A metaadatok Linuxon történő átvitele kétlépéses folyamat. Először másolja a forrásadatokat egy olyan eszköz használatával `rsync` , amely nem másolja a metaadatokat. Az adatok másolása után a metaadatokat egy eszköz, például a vagy a használatával is másolhatja `smbcacls` `cifsacl` . 

Az alábbi példa az első lépést hajtja végre az adatoknak a használatával történő másolásával `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Következő lépések

- [Adatok másolása az Azure Data Boxra SMB-n keresztül](./data-box-deploy-copy-data.md)