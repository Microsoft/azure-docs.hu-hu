---
title: Az Azure Storage migrálási eszközeinek összehasonlítása – Strukturálatlan adatok
description: A strukturálatlan adatok áttelepítéséhez használt eszközök alapszintű funkciói és összehasonlítása
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 862feace6aab4f49ad3482c4ccd6510669c876a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576533"
---
# <a name="comparison-matrix"></a>Összehasonlító mátrix

Az alábbi összehasonlító mátrix a strukturálatlan adatok áttelepítéséhez használható különböző eszközök alapvető funkcióit mutatja be. 

## <a name="supported-azure-services"></a>Támogatott Azure-szolgáltatások

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Megoldás neve**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Adatmobilság és migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files támogatása (minden szint)** | Igen                          | Igen                      | Igen            | Yes                            |
| **Azure NetApp Files támogatás**      | Nem                           | Igen                      | Igen            | Yes                            |
| **Azure Blob Hot/Cool támogatás**   | No                           | Igen (NFS előzetes verzión keresztül)    | Igen            | Yes                            |
| **Azure Blob Archive szint támogatása** | Nem                           | Nem                       | Nem             | Igen (migrálási célként) |
| **Azure Data Lake Storage támogatás** | Nem                           | Nem                       | Nem             | Nem                             |
| **Támogatott források**      | Windows Server 2012 R2 és felfelé | NAS & fájlrendszerek | Bármely NAS és S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Támogatott protokollok (forrás/cél)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise (Komprise)](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Megoldás neve**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Adat mobilitása és migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Igen | Igen | Igen | Yes |
| **SMB 3.0**       | Igen | Igen | Igen | Yes |
| **SMB 3.1**       | Igen | Igen | Igen | Yes |
| **NFS v3**        | Nem  | Igen | Igen | Yes |
| **NFS v4.1**      | Nem  | Igen | Nem  | Igen |
| **Blob REST API** | Nem  | Nem  | Igen | Yes |
| **S3**            | Nem  | Igen | Igen | Yes |

## <a name="extended-features"></a>Bővített funkciók

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Megoldás neve**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Adat mobilitása és migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID/SID újratérképe**                   | Nem  | Igen                        | Igen | Nem                             |
| **Protokoll ACL-újratérképe**                | Nem  | Nem                         | Nem  | Nem                             |
| **Elosztott fájlrendszer támogatása**                           | Igen | Igen                        | Igen | Yes                            |
| **Szabályozás támogatása**                    | Igen | Igen                        | Igen | Yes                            |
| **Fájlmintakizárások**               | Nem  | Igen                        | Yes | Igen (másolási funkció használatával) |
| **Szelektív fájlattribútumok támogatása** | Igen | Igen                        | Yes | Igen (kiterjesztett attribútumok esetén)  |
| **Propagálások törlése**                   | Igen | Igen                        | Igen | Yes                            |
| **NTFS-illesztéseket követ**                 | Nem  | Igen                        | Nem  | Igen                            |
| **SMB-tulajdonos és csoporttulajdonos felülbírálása**    | Igen | Igen                        | Igen | Nem                             |
| **Felügyeleti lánc jelentéskészítése**            | Nem  | Igen                        | Nem  | Igen                            |
| **Alternatív adatfolyamok támogatása**    | Nem  | Igen                        | Igen | Nem                             |
| **Ütemezés a migráláshoz**              | Nem  | Igen                        | Igen | Yes                            |
| **ACL megőrzése**                        | Nem  | Igen                        | Igen | Yes                            |
| **DACL-támogatás**                          | Igen | Igen                        | Igen | Yes                            |
| **SACL-támogatás**                          | Igen | Igen                        | Igen | Nem                             |
| **A hozzáférési idő megőrzése**                | Igen | Igen                        | Igen | Yes                            |
| **Módosított idő megőrzése**              | Igen | Igen                        | Igen | Yes                            |
| **Létrehozási idő megőrzése**              | Nem  | Igen                        | Igen | Yes                            |
| **Azure Data Box támogatás**       | Igen | Igen                        | Nem  | Nem                             |
| **Pillanatképek migrálása**                | No  | Kézi                     | Igen | Nem                             |
| **Szimbolikus hivatkozás támogatása**                 | Nem  | Igen                        | Nem  | Igen                            |
| **A hard link támogatása**                     | No  | Migrálva külön fájlként | Igen | Yes                            |
| **Nyitott/zárolt fájlok támogatása**       | Igen | Igen                        | Igen | Yes                            |
| **Növekményes migrálás**                 | Igen | Igen                        | Igen | Yes                            |
| **Váltás támogatása**                    | Nem  | Igen                        | Yes | Nem (csak manuális)               |
| **[Egyéb jellemzők](#other-features)**         | [Link](#azure-file-sync)| [Link](#dobimigrate) | [Link](#data-mobility-and-migration) | [Link](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Értékelés és jelentéskészítés

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Megoldás neve**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Adatmobilság és migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Kapacitás**                        | Nem      | Igen | Igen | Yes            |
| **Fájlok/mappák létrehozása**            | Nem      | Igen | Igen | Yes            |
| **Koreloszlás az idő során**      | Nem      | Igen | Igen | Yes            |
| **Hozzáférési idő**                     | Nem      | Igen | Igen | Yes            |
| **Módosítás időpontja**                   | Nem      | Igen | Igen | Yes            |
| **Létrehozási idő**                   | Nem      | Igen | Igen | Yes            |
| **Fájlonként/objektumonkénti jelentés állapota** | Részleges | Igen | Igen | Yes            |

## <a name="licensing"></a>Licencek

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Adatdinamika](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Megoldás neve**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Adatmobilság és migrálás](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent adatkezelés](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N / A | Igen | Igen | Yes |
| **Azure-kötelezettségvállalás** | Igen   | Igen | Igen | Yes |

## <a name="other-features"></a>Egyéb jellemzők

### <a name="azure-file-sync"></a>Azure File Sync

- Belső kivonat ellenőrzése

### <a name="dobimigrate"></a>DobiMigrate

- Migrálás előzetes ellenőrzése
- A migrálás megtervezése
- Teszt a tesztelés átvágása céljából
- A céloldali felhasználói tevékenység észlelése és riasztása az átvágás előtt
- Szabályzatalapú migrálások
- Ütemezett másolási iterációk
- Konfigurálható beállítások a gyökérkönyvtár biztonságának kezelésével
- Igény szerinti ellenőrzés futtatása
- Adatok visszaolvasott ellenőrzése a forráson és a célon
- Grafikus, interaktív hibakezelési munkafolyamat
- Bizonyos műveletek propagálásának korlátozása, például törlések és frissítések
- Lehetőség a hozzáférési idő megőrzésére a forráson (a célhelyen kívül)
- Lehetőség a forrásra való visszaállítás végrehajtására a migrálás átváltása során
- A kiválasztott SMB-fájlattribútumok áttelepítésének képessége
- Az NTFS biztonsági leírók tisztításának képessége
- NfSv3-engedélyek felülbírálása és új módú bitek írása a célhoz
- Az NFSv3 POSIX-piszkozat ACLS konvertálása NFSv4 ACLS-re
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Adat mobilitása és migrálás

- Kivonatérvényesítés

### <a name="intelligent-data-management"></a>Intelligent adatkezelés

- Projekt-/könyvtáralapú migrálások
- Hibák automatikus újrapróbálása
- Értékelés/jelentéskészítés: Fájltípusok, fájlméret, projektalapú
- Értékelés/jelentéskészítés: Egyéni metaadat-alapú keresések
- Teljes körű adat-életciklus-kezelési megoldás archiváláshoz, replikációhoz és elemzéshez
- Hozzáférés időalapú elemzések blob- és S3-adatokhoz
- Címkézés
- 24 x 7 x 365 támogatása
- Kivonatérvényesítés

*A lista legutóbb 2021. március 31-én lett ellenőrizve.*

## <a name="see-also"></a>Lásd még

- [Tárolóáttelepítés áttekintése](../../../common/storage-migration-overview.md)
- [Azure-megoldás kiválasztása adatátvitelhez](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migrálás Azure-fájlmegosztásokba](/azure/storage/files/storage-files-migration-overview)
- [Áttelepítés Data Lake Storage WANdisco LiveData Platform for Azure használatával](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Adatok másolása vagy áthelyezése az Azure Storage-ba az AzCopyval](https://aka.ms/azcopy)
- [Nagyméretű adatkészletek áttelepítése Azure Blob Storage AzReplicate-sel (mintaalkalmazás)](https://github.com/Azure/AzReplicate/tree/master/)
