---
title: Tudnivalók az Azure-beli virtuális gépeken SAP HANA adatbázis biztonsági mentéséről
description: Ez a cikk az Azure Virtual Machines szolgáltatásban futó SAP HANA adatbázisok biztonsági mentését ismerteti.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: efb9c3f786e429df404e261f053a9c9a9b032e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96296454"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Tudnivalók az Azure-beli virtuális gépeken SAP HANA adatbázis biztonsági mentéséről

SAP HANA adatbázisok olyan kritikus fontosságú munkaterhelések, amelyek alacsony helyreállítási időcélkitűzést (RPO) és gyors helyreállítási időszakot (RTO) igényelnek. [Az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentését](./tutorial-backup-sap-hana-db.md) [Azure Backup](./backup-overview.md)használatával végezheti el.

Azure Backup az SAP által [tanúsított Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) , hogy natív biztonsági mentési támogatást nyújtson a SAP HANA natív API-jai segítségével. Ez az ajánlat olyan Azure Backup, amely az **infrastruktúra-alapú** biztonsági mentések Azure Backupi Mantra szolgáltatásával összhangban van, így nincs szükség a biztonsági mentési infrastruktúra üzembe helyezésére és felügyeletére. Mostantól zökkenőmentesen végezheti el az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentését és visszaállítását (az[M sorozatú virtuális gépek](../virtual-machines/m-series.md) mostantól is támogatottak), és a Azure Backup által biztosított vállalati felügyeleti képességeket

## <a name="added-value"></a>Hozzáadott érték

SAP HANA adatbázisok biztonsági mentése és visszaállítása Azure Backup használatával a következő előnyöket nyújtja:

* **15 perces helyreállítási időkorlát (RPO)**: már lehetséges a kritikus adatmennyiség 15 percen belüli helyreállítása.
* **Egykattintásos, időponthoz** tartozó visszaállítások: a termelési és az alternatív HANA-kiszolgálókra történő biztonsági mentés egyszerűen elvégezhető. A biztonsági mentések és a katalógusok a visszaállítások elvégzéséhez való láncolása mind az Azure által a színfalak mögött van kezelve.
* **Hosszú távú adatmegőrzés**: szigorú megfelelőségi és naplózási igények. Megtarthatja a biztonsági mentéseket évekig, a megőrzési időtartam alapján, amelyen keresztül a helyreállítási pontokat automatikusan metszi a beépített életciklus-kezelési képesség.
* **Biztonsági mentési felügyelet az Azure-ból**: Azure Backup felügyeleti és figyelési képességeinek használata a jobb felügyeleti élmény érdekében. Az Azure CLI-t is támogatja.

A jelenleg támogatott biztonsági mentési és visszaállítási forgatókönyvek megtekintéséhez tekintse meg a [SAP HANA forgatókönyv támogatási mátrixát](./sap-hana-backup-support-matrix.md#scenario-support).

## <a name="backup-architecture"></a>Biztonsági mentési architektúra

![Biztonsági mentési architektúra diagramja](./media/sap-hana-db-about/backup-architecture.png)

* A biztonsági mentési folyamat [egy Recovery Services tároló létrehozásával](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) kezdődik az Azure-ban. Ezt a tárolót fogja használni a biztonsági másolatok és a létrehozott helyreállítási pontok tárolásához az idő múlásával.
* A SAP HANA-kiszolgálót futtató Azure-beli virtuális gép regisztrálva van a tárolóban, és a biztonsági mentésre kerülő adatbázisokat a rendszer [felderíti](./tutorial-backup-sap-hana-db.md#discover-the-databases). Ahhoz, hogy a Azure Backup szolgáltatás lehetővé váljon az adatbázisok felderítése, egy [előregisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) kell futtatni a HANA-kiszolgálón root felhasználóként.
* Ez a szkript létrehozza a **AZUREWLBACKUPHANAUSER** db-felhasználót és a megfelelő kulcsot ugyanazzal a névvel a **hdbuserstore**-ben. Ha többet szeretne megtudni a parancsfájlról, tekintse meg a  [Mi az előzetes regisztrációs parancsfájlt](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) ismertető szakaszát.
* Azure Backup a szolgáltatás most telepíti a HANA-hoz készült **Azure Backup beépülő modult** a regisztrált SAP HANA kiszolgálón.
* Az előregisztrációs parancsfájl által létrehozott **AZUREWLBACKUPHANAUSER** db-felhasználót a HANA-hoz készült **Azure Backup beépülő modul** használja az összes biztonsági mentési és visszaállítási művelet elvégzéséhez. Ha a parancsfájl futtatása nélkül kísérli meg SAP HANA-adatbázisok biztonsági mentésének konfigurálását, a következő hibaüzenet jelenhet meg: **UserErrorHanaScriptNotRun**.
* Ha a [biztonsági mentést](./tutorial-backup-sap-hana-db.md#configure-backup) a felderített adatbázisokon szeretné konfigurálni, válassza ki a szükséges biztonsági mentési szabályzatot, és engedélyezze a biztonsági mentéseket.

* A biztonsági mentés konfigurálása után Azure Backup szolgáltatás a következő Backint paramétereket állítja be a védett SAP HANA-kiszolgálón lévő adatbázis szintjén:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup: FALSE]
  * [parallel_data_backup_backint_channels: 1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Győződjön meg arról, hogy ezek a paraméterek *nem* találhatók meg a gazdagép szintjén. A gazdagép szintű paraméterek felülbírálják ezeket a paramétereket, és váratlan viselkedést okozhatnak.
>

* A **HANA Azure Backup beépülő modulja** fenntartja az összes biztonsági mentési ütemtervet és házirend-részletet. Elindítja az ütemezett biztonsági mentéseket, és a Backint API-kon keresztül kommunikál a **HANA Backup motorral** .
* A **HANA Backup motor** egy Backint-adatfolyamot ad vissza a biztonsági mentésre szolgáló adatokkal.
* Az összes ütemezett biztonsági mentést és igény szerinti biztonsági mentést (a Azure Portalból kiváltott) a **HANA Azure Backup beépülő modulja** kezdeményezi. A naplók biztonsági mentését azonban a **HANA Backup motor** felügyeli és aktiválja.
* A SAP HANA számára Azure Backup BackInt tanúsított megoldás, nem függ a mögöttes lemez-vagy virtuálisgép-típustól. A biztonsági mentést a HANA által generált streamek végzik.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Azure-beli virtuális gépek biztonsági mentésének használata az Azure SAP HANA Backup szolgáltatással

Az adatbázis-szintű biztonsági mentést és helyreállítást biztosító Azure-beli SAP HANA biztonsági mentés mellett az Azure-beli virtuális gép biztonsági mentési megoldásával biztonsági másolatot készíthet az operációs rendszerről és a nem adatbázis-lemezekről.

Az [Backint Certified Azure SAP HANA biztonsági mentési megoldás](#backup-architecture) használható az adatbázisok biztonsági mentéséhez és helyreállításához.

Az Azure-beli [virtuális gépek biztonsági mentése](backup-azure-vms-introduction.md) az operációs rendszer és az egyéb nem adatbázis-lemezek biztonsági mentésére is használható. A virtuális gép biztonsági mentését naponta egyszer kell elvégezni, és az összes lemezről biztonsági másolatot készít (kivéve **írásgyorsító (WA) operációsrendszer-lemezeket** és **Ultra lemezeket**). Mivel az adatbázis biztonsági mentése az Azure SAP HANA Backup megoldás használatával történik, az Azure-beli [virtuális gépek funkcióinak szelektív lemezes biztonsági mentésével és visszaállításával](selective-disk-backup-restore.md) csak az operációs rendszer és a nem adatbázis típusú lemezekről készíthet biztonsági mentést.

SAP HANA rendszert futtató virtuális gép visszaállításához kövesse az alábbi lépéseket:

* [Új virtuális gép visszaállítása az Azure virtuális gép biztonsági másolatából](backup-azure-arm-restore-vms.md) a legújabb helyreállítási pontról. Vagy hozzon létre egy új üres virtuális gépet, és csatolja a lemezeket a legújabb helyreállítási ponthoz.
* Ha a WA-lemezek ki vannak zárva, azok nem állíthatók vissza. Ebben az esetben hozzon létre üres WA-lemezeket és naplózási területeket.
* Miután az összes többi konfiguráció (például az IP, a rendszer neve stb.) be van állítva, a virtuális gép a Azure Backup adatbázis-adatok fogadására van beállítva.
* Most állítsa vissza az adatbázist a virtuális gépre az [Azure SAP HANA db biztonsági másolatból](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) a kívánt időpontra.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [állíthatja vissza egy Azure-beli virtuális gépen futó SAP HANA-adatbázist](./sap-hana-db-restore.md)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](./sap-hana-db-manage.md)
