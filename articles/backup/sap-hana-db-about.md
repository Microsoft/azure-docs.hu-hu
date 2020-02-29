---
title: Tudnivalók az Azure-beli virtuális gépeken SAP HANA adatbázis biztonsági mentéséről
description: Ez a cikk az Azure Virtual Machines szolgáltatásban futó SAP HANA adatbázisok biztonsági mentését ismerteti.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 188cef6bc9771f779e3e9c7f7f5fe246e929b68a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918512"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Tudnivalók az Azure-beli virtuális gépeken SAP HANA adatbázis biztonsági mentéséről

SAP HANA adatbázisok olyan kritikus fontosságú munkaterhelések, amelyek alacsony helyreállítási időcélkitűzést (RPO) és gyors helyreállítási időszakot (RTO) igényelnek. [Az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentését](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)használatával végezheti el.

Azure Backup az SAP által [tanúsított Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) , hogy natív biztonsági mentési támogatást nyújtson a SAP HANA natív API-jai segítségével. Ez az ajánlat olyan Azure Backup, amely az **infrastruktúra-alapú** biztonsági mentések Azure Backupi Mantra szolgáltatásával összhangban van, így nincs szükség a biztonsági mentési infrastruktúra üzembe helyezésére és felügyeletére. Mostantól zökkenőmentesen végezheti el az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentését és visszaállítását (az[M sorozatú virtuális gépek](../virtual-machines/m-series.md) mostantól is támogatottak), és a Azure Backup által biztosított vállalati felügyeleti képességeket

## <a name="added-value"></a>Hozzáadott érték

SAP HANA adatbázisok biztonsági mentése és visszaállítása Azure Backup használatával a következő előnyöket nyújtja:

* **15 perces helyreállítási időkorlát (RPO)** : már lehetséges a kritikus adatmennyiség 15 percen belüli helyreállítása.
* **Egykattintásos, időponthoz**tartozó visszaállítások: a termelési és az alternatív HANA-kiszolgálókra történő biztonsági mentés egyszerűen elvégezhető. A biztonsági mentések és a katalógusok a visszaállítások elvégzéséhez való láncolása mind az Azure által a színfalak mögött van kezelve.
* **Hosszú távú adatmegőrzés**: szigorú megfelelőségi és naplózási igények. Megtarthatja a biztonsági mentéseket évekig, a megőrzési időtartam alapján, amelyen keresztül a helyreállítási pontokat automatikusan metszi a beépített életciklus-kezelési képesség.
* **Biztonsági mentési felügyelet az Azure-ból**: Azure Backup felügyeleti és figyelési képességeinek használata a jobb felügyeleti élmény érdekében. Az Azure CLI-t is támogatja.

A jelenleg támogatott biztonsági mentési és visszaállítási forgatókönyvek megtekintéséhez tekintse meg a [SAP HANA forgatókönyv támogatási mátrixát](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Biztonsági mentési architektúra

![Biztonsági mentési architektúra diagramja](./media/sap-hana-db-about/backup-architecture.png)

* A biztonsági mentési folyamat [egy Recovery Services-tároló létrehozásával](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) kezdődik az Azure-ban. Ezt a tárolót fogja használni a biztonsági másolatok és a létrehozott helyreállítási pontok tárolásához az idő múlásával.
* A SAP HANA-kiszolgálót futtató Azure-beli virtuális gép regisztrálva van a tárolóban, és a biztonsági mentésre kerülő adatbázisokat a rendszer [felderíti](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). Ahhoz, hogy a Azure Backup szolgáltatás lehetővé váljon az adatbázisok felderítése, egy [előregisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) kell futtatni a HANA-kiszolgálón root felhasználóként.
* Ez a szkript létrehozza a **AZUREWLBACKUPHANAUSER** db-felhasználót és a megfelelő kulcsot ugyanazzal a névvel a **hdbuserstore**-ben. Tekintse át az [engedélyek beállítása szakaszt](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions) , és tájékozódjon arról, hogy mi a szkript.
* Azure Backup a szolgáltatás most telepíti a HANA-hoz készült **Azure Backup beépülő modult** a regisztrált SAP HANA kiszolgálón.
* Az előregisztrációs parancsfájl által létrehozott **AZUREWLBACKUPHANAUSER** db-felhasználót a HANA-hoz készült **Azure Backup beépülő modul** használja az összes biztonsági mentési és visszaállítási művelet elvégzéséhez. Ha a parancsfájl futtatása nélkül kísérli meg SAP HANA-adatbázisok biztonsági mentésének konfigurálását, a következő hibaüzenet jelenhet meg: **UserErrorHanaScriptNotRun**.
* Ha a [biztonsági mentést](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) a felderített adatbázisokon szeretné konfigurálni, válassza ki a szükséges biztonsági mentési szabályzatot, és engedélyezze a biztonsági mentéseket.

* A biztonsági mentés konfigurálása után Azure Backup szolgáltatás a következő Backint paramétereket állítja be a védett SAP HANA-kiszolgálón lévő adatbázis szintjén:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Győződjön meg arról, hogy ezek a paraméterek *nem* találhatók meg a gazdagép szintjén. A gazdagép szintű paraméterek felülbírálják ezeket a paramétereket, és váratlan viselkedést okozhatnak.
>

* A **HANA Azure Backup beépülő modulja** fenntartja az összes biztonsági mentési ütemtervet és házirend-részletet. Elindítja az ütemezett biztonsági mentéseket, és a Backint API-kon keresztül kommunikál a **HANA Backup motorral** .
* A **HANA Backup motor** egy Backint-adatfolyamot ad vissza a biztonsági mentésre szolgáló adatokkal.
* Az összes ütemezett biztonsági mentést és igény szerinti biztonsági mentést (a Azure Portalból kiváltott) a **HANA Azure Backup beépülő modulja**kezdeményezi. A naplók biztonsági mentését azonban a **HANA Backup motor** felügyeli és aktiválja.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [állíthatja vissza egy Azure-beli virtuális gépen futó SAP HANA-adatbázist](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
