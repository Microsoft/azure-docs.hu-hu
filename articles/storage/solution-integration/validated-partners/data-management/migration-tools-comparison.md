---
title: Az Azure Storage áttelepítési eszközeinek összehasonlítása – strukturálatlan adat
description: Alapvető funkciók és összehasonlítás a strukturálatlan adatok áttelepítéséhez használt eszközök között
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231589"
---
# <a name="comparison-matrix"></a>Összehasonlító mátrix

A következő összehasonlító mátrix a strukturálatlan adatok áttelepítéséhez használható különböző eszközök alapvető funkcióit mutatja be. 

## <a name="supported-azure-services"></a>Támogatott Azure-szolgáltatások

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Megoldás neve**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Az adatmobilitás és a Migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligens adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files támogatás (minden szinten)** | Igen                          | Igen                      | Igen            | Igen                            |
| **Azure NetApp Files támogatás**      | Nem                           | Igen                      | Igen            | Igen                            |
| **Azure Blob – gyors/lassú támogatás**   | Nem                           | Igen (az NFS előzetes verzióján keresztül)    | Igen            | Igen                            |
| **Az Azure Blob archiválási szintjeinek támogatása** | Nem                           | Nem                       | Nem             | Igen (áttelepítés célhelyként) |
| **Azure Data Lake Storage támogatás** | Nem                           | Nem                       | Nem             | Nem                             |
| **Támogatott források**      | Windows Server 2012 R2 és akár | NAS & felhőalapú fájlrendszerek | Bármely NAS és S3 | NAS, blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Támogatott protokollok (forrás/cél)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Megoldás neve**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Az adatmobilitás és a Migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligens adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Igen | Igen | Igen | Igen |
| **SMB 3.0**       | Igen | Igen | Igen | Igen |
| **SMB 3,1**       | Igen | Igen | Igen | Igen |
| **NFS v3**        | Nem  | Igen | Igen | Igen |
| **NFS v 4.1**      | Nem  | Igen | Nem  | Igen |
| **BLOB REST API** | Nem  | Nem  | Igen | Igen |
| **S3**            | Nem  | Igen | Igen | Igen |

## <a name="extended-features"></a>Bővített funkciók

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Megoldás neve**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Az adatmobilitás és a Migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligens adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID/SID-újramegfeleltetés**                   | Nem  | Igen                        | Igen | Nem                             |
| **Protokoll ACL-újramegfeleltetése**                | Nem  | Nem                         | Nem  | Nem                             |
| **DFS-támogatás**                           | Igen | Igen                        | Igen | Igen                            |
| **Szabályozás támogatása**                    | Igen | Igen                        | Igen | Igen                            |
| **A fájl mintájának kizárása**               | Nem  | Igen                        | Igen | Igen (másolási funkció használata) |
| **Szelektív fájlattribútumok támogatása** | Igen | Igen                        | Igen | Igen (a kiterjesztett attribútumok esetében)  |
| **Propagálások törlése**                   | Igen | Igen                        | Igen | Igen                            |
| **NTFS-csomópontok követése**                 | Nem  | Igen                        | Nem  | Igen                            |
| **SMB-tulajdonos és csoport tulajdonosának felülbírálása**    | Igen | Igen                        | Igen | Nem                             |
| **Felügyeleti lánc – jelentéskészítés**            | Nem  | Igen                        | Nem  | Igen                            |
| **Alternatív adatfolyamok támogatása**    | Nem  | Igen                        | Igen | Nem                             |
| **Áttelepítés ütemezése**              | Nem  | Igen                        | Igen | Igen                            |
| **ACL megőrzése**                        | Nem  | Igen                        | Igen | Igen                            |
| **DACL-támogatás**                          | Igen | Igen                        | Igen | Igen                            |
| **Rendszerszintű hozzáférés-támogatás**                          | Igen | Igen                        | Igen | Nem                             |
| **Hozzáférési idő megőrzése**                | Igen | Igen                        | Igen | Igen                            |
| **Módosítási idő megőrzése**              | Igen | Igen                        | Igen | Igen                            |
| **Létrehozási idő megőrzése**              | Nem  | Igen                        | Igen | Igen                            |
| **Azure Data Box támogatás**       | Igen | Igen                        | Nem  | Nem                             |
| **Pillanatképek áttelepítése**                | Nem  | Kézi                     | Igen | Nem                             |
| **Szimbolikus hivatkozás támogatása**                 | Nem  | Igen                        | Nem  | Igen                            |
| **Rögzített hivatkozás támogatása**                     | Nem  | Áttelepítve különálló fájlként | Igen | Igen                            |
| **Nyitott/zárolt fájlok támogatása**       | Igen | Igen                        | Igen | Igen                            |
| **Növekményes migrálás**                 | Igen | Igen                        | Igen | Igen                            |
| **Átállás támogatása**                    | Nem  | Igen                        | Igen | Nem (csak manuális)               |
| **[Egyéb jellemzők](#other-features)**         | [Hivatkozás](#azure-file-sync)| [Hivatkozás](#dobimigrate) | [Hivatkozás](#data-mobility-and-migration) | [Hivatkozás](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Értékelés és jelentéskészítés

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Megoldás neve**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Az adatmobilitás és a Migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligens adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Kapacitás**                        | Nem      | Igen | Igen | Igen            |
| **fájlok/mappák száma**            | Nem      | Igen | Igen | Igen            |
| **Életkor eloszlása az idő függvényében**      | Nem      | Igen | Igen | Igen            |
| **Hozzáférési idő**                     | Nem      | Igen | Igen | Igen            |
| **Módosítás időpontja**                   | Nem      | Igen | Igen | Igen            |
| **Létrehozás ideje**                   | Nem      | Igen | Igen | Igen (csak SMB) |
| **Fájlonként/objektum jelentés állapota** | Részleges | Igen | Igen | Igen            |

## <a name="licensing"></a>Licencek

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Megoldás neve**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Az adatmobilitás és a Migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligens adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N/A | Igen | Igen | Igen |
| **Azure-kötelezettségvállalás** | Igen   | Igen | Igen | Igen |

## <a name="other-features"></a>Egyéb jellemzők

### <a name="azure-file-sync"></a>Azure File Sync

- Belső kivonatoló ellenőrzés

### <a name="dobimigrate"></a>DobiMigrate

- Áttelepítés előzetes ellenőrzése
- Áttelepítés megtervezése
- Száraz Futtatás a kivágási teszteléshez
- A cél oldali felhasználói tevékenység észlelése és riasztása a Kivágás előtt
- Házirend-alapú áttelepítések
- Ütemezett másolási iterációk
- Konfigurálható beállítások a gyökérkönyvtár biztonságának kezelésére
- Igény szerinti ellenőrzés futtatása
- Adatolvasási ellenőrzés a forráson és a célhelyen
- Grafikus, interaktív hiba-kezelési munkafolyamat
- Bizonyos műveletek korlátozása, például a törlések és a frissítések propagálásának lehetősége
- A forráshoz való hozzáférési idő megőrzése (a célhelyen kívül)
- A visszagörgetési művelet végrehajtása a forrásra áttelepítés közben
- A kiválasztott SMB-fájlattribútumok áttelepíthetők
- Az NTFS-alapú biztonsági leírók tisztításának lehetősége
- Lehetőség a NFSv3-engedélyek felülbírálására és az új módú bitek megcélzására
- Képesség a NFSv3 POSIX-Nfsv4 névleképezője ACL-re való átalakítására
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Az adatmobilitás és a Migrálás

- Kivonatoló ellenőrzés

### <a name="intelligent-data-management"></a>Intelligens adatkezelés

- Projekt/címtár-alapú áttelepítések
- Hibák automatikus újrapróbálkozása
- Értékelés/jelentéskészítés: fájltípusok, fájlméret, projekt-alapú
- Értékelés/jelentéskészítés: egyéni metaadatokon alapuló keresések
- Teljes körű adatéletciklus-kezelési megoldás archiválásra, replikálásra, elemzésre
- Időalapú elemzések elérése a blobon, az S3-ban
- Címkézés
- Támogatás 24 x 7 x 365
- Kivonatoló ellenőrzés

*A lista utolsó ellenőrzése március 31., 2021.*

## <a name="see-also"></a>Lásd még

- [A tárterület áttelepítése – áttekintés](../../../common/storage-migration-overview.md)
- [Azure-megoldás kiválasztása adatátvitelhez](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migrálás Azure-fájlmegosztásokba](/azure/storage/files/storage-files-migration-overview)
- [Migrálás Data Lake Storagera az Azure-hoz készült WANdisco LiveData platformmal](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Az Azure Storage-ba másolhatja vagy áthelyezheti az AzCopy](https://aka.ms/azcopy)
- [Nagyméretű adatkészletek migrálása az Azure Blob Storageba a AzReplicate (minta alkalmazás)](https://github.com/Azure/AzReplicate/tree/master/)
