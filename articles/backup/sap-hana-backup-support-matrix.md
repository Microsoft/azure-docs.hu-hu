---
title: A SAP HANA biztonsági mentésének támogatási mátrixa
description: Ebben a cikkben megismerheti a támogatott forgatókönyveket és korlátozásokat, amikor a Azure Backup használatával biztonsági mentést készít az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokról.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: cbf910a0291e90965c9698a8b2a43c587cbfe0b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707234"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure-beli virtuális gépeken tárolt SAP HANA-adatbázisok biztonsági mentésére vonatkozó támogatási mátrix

A Azure Backup támogatja SAP HANA adatbázisok biztonsági mentését az Azure-ba. Ez a cikk az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentésének Azure Backup használatával összefoglalja a támogatott forgatókönyveket és korlátozásokat.

> [!NOTE]
> A napló biztonsági mentésének gyakorisága mostantól legalább 15 percet is igénybe vehet. A naplók biztonsági mentései csak az adatbázis sikeres teljes biztonsági mentésének befejeződése után kezdődnek.

## <a name="scenario-support"></a>Forgatókönyvek támogatása

| **Forgatókönyv**               | **Támogatott konfigurációk**                                | **Nem támogatott konfigurációk**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topológia**               | Csak az Azure Linux rendszerű virtuális gépeken futó SAP HANA                    | HANA Large instances (HLI)                                   |
| **Régiók**                   | **GA**<br> **Amerikai** Egyesült Államok, USA középső régiója, USA 2. keleti régiója, az USA keleti régiója, az USA északi középső régiója, az USA déli középső régiója, USA 2. nyugati régiója, USA nyugati középső régiója, Nyugat-Kanada <br> **Ázsia és a csendes-óceáni térség** – Ausztrália középső régiója, Ausztrália középső régiója 2, Kelet-Ausztrália, Délkelet-Ausztrália, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-korea, Kelet-Ázsia, Délkelet-Ázsia, Közép-India, Dél-India, Nyugat-india, Kelet-Kína, Észak-Kína, Kína készletek, Észak-Kína 2 <br> **Európa** – Nyugat-Európa, Észak-Európa, Közép-franciaország, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Észak-Németország, Középnyugat-Németország, Észak-Svájc, Nyugat-Svájc, központi Észak-Svájc, Kelet-Norvégia, Norvégia nyugati régiója <br> **Afrika/Me** – Dél-Afrika, Észak-Afrika, Nyugat-Európa, Észak-Európa, Egyesült Arab Emírségek középső régiója  <BR>  **Azure Government-régiók** | Dél-Franciaország, Közép-Németország, Északkelet-Németország, US Gov IOWA |
| **OPERÁCIÓSRENDSZER-verziók**            | SLES 12 SP2, SP3, SP4 és SP5; SLES 15, SP0, SP1, SP2 <br><br>  RHEL 7,4, 7,6, 7,7, 8,1 & 8,2                |                                             |
| **HANA-verziók**          | SDC a HANA 1. x-ben, MDC on HANA 2. x SPS04, SPS05 Rev <= 53 (a titkosításhoz engedélyezett forgatókönyvek esetében is ellenőrzött)      |                                                            |
| **HANA-telepítések**       | SAP HANA egyetlen Azure-beli virtuális gépen – csak vertikális felskálázással. <br><br> A magas rendelkezésre állású központi telepítések esetében a két különböző gép csomópontjait különálló, külön adatláncú csomópontként kezeli a rendszer.               | Bővítés <br><br> Magas rendelkezésre állású telepítések esetén a biztonsági mentés automatikusan nem végzi el a feladatátvételt a másodlagos csomópontra. A biztonsági mentés konfigurálását külön kell elvégezni az egyes csomópontokon.                                           |
| **HANA-példányok**         | Egyetlen SAP HANA példány egyetlen Azure-beli virtuális gépen – csak vertikális felskálázás | Több SAP HANA példány egyetlen virtuális gépen. Egyszerre csak egy ilyen példányt lehet védelemmel ellátni.                  |
| **HANA-adatbázisok típusai**    | Önálló adatbázis tároló (SDC) 1. x, több adatbázisból álló tárolón (MDC) a 2. x számítógépen | MDC a HANA 1. x-ben                                              |
| **HANA-adatbázis mérete**     | <= 8 TB méretű HANA-adatbázisok (ez nem a HANA rendszer memóriájának mérete)               |                                                              |
| **Biztonsági másolatok típusai**           | Teljes, különbözeti, növekményes és naplózott biztonsági másolatok                          |  Pillanatképek                                       |
| **Visszaállítási típusok**          | A támogatott visszaállítási típusokról a [1642148](https://launchpad.support.sap.com/#/notes/1642148) -es SAP HANA megjegyzésben tájékozódhat. |                                                              |
| **Biztonsági mentési korlátok**          | Akár 8 TB teljes biztonsági mentési méret SAP HANA példányban (Soft limit)         |                                                              |
| **Speciális konfigurációk** |                                                              | SAP HANA + dinamikus rétegek <br>  Klónozás a láma használatával        |

------

>[!NOTE]
>A Azure Backup nem módosítja automatikusan a nyári időmegtakarítást az Azure-beli virtuális gépen futó SAP HANA-adatbázis biztonsági mentésekor.
>
>Szükség szerint módosítsa manuálisan a szabályzatot.

> [!NOTE]
> Mostantól nyomon követheti a Azure Portal a HANA natív ügyfelektől (SAP HANA Studio/cockpit/DBA pilótafülke) indított [biztonsági mentési és visszaállítási](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) feladatokat.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést SAP HANA Azure-beli virtuális gépeken futó adatbázisokról](./backup-azure-sap-hana-database.md)
* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](./sap-hana-db-restore.md)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](sap-hana-db-manage.md)
* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során](./backup-azure-sap-hana-database-troubleshoot.md) felmerülő gyakori problémákat
