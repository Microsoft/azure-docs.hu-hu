---
title: Az adatbázisok biztonsági mentésével kapcsolatos hibák elhárítása SAP HANA
description: Leírja, hogy miként lehet elhárítani a SAP HANA-adatbázisok biztonsági mentésekor Azure Backup használata során előforduló gyakori hibákat.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 22800adc323bda8a60278160f24bc559103fb57e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713337"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>SAP HANA-adatbázisok Azure-beli biztonsági mentésének hibáinak megoldása

Ez a cikk a SAP HANA adatbázisok Azure-beli virtuális gépeken történő biztonsági mentésével kapcsolatos hibaelhárítási információkat tartalmaz. A jelenleg támogatott SAP HANA biztonsági mentési forgatókönyvekkel kapcsolatos további információkért tekintse meg a [forgatókönyvek támogatása](sap-hana-backup-support-matrix.md#scenario-support)című témakört.

## <a name="prerequisites-and-permissions"></a>Előfeltételek és engedélyek

A biztonsági mentések konfigurálása előtt tekintse át az [előfeltételeket](tutorial-backup-sap-hana-db.md#prerequisites) , valamint azt, hogy [Mit tartalmaz az előzetes regisztrációs parancsfájl](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

## <a name="common-user-errors"></a>Gyakori felhasználói hibák

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Hibaüzenet**      | <span style="font-weight:normal">A Azure Backup nem rendelkezik a biztonsági mentés végrehajtásához szükséges szerepkör-jogosultságokkal</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Lehetséges okok**    | Lehetséges, hogy a szerepkör felül lett írva.                          |
| **Javasolt művelet** | A probléma megoldásához futtassa a szkriptet a **felderítési adatbázis** panelen, vagy töltse le [itt](https://aka.ms/scriptforpermsonhana). Azt is megteheti, hogy hozzáadja a "SAP_INTERNAL_HANA_SUPPORT" szerepkört a munkaterhelés biztonsági mentési felhasználóhoz (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Hibaüzenet      | <span style="font-weight:normal">Nem sikerült csatlakozni a HANA rendszerhez</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Lehet, hogy a SAP HANA példány nem található.<br/>A HANA-adatbázissal való interakcióhoz Azure Backup szükséges engedélyek nincsenek beállítva. |
| **Javasolt művelet** | Ellenőrizze, hogy a SAP HANA-adatbázis működik-e. Ha az adatbázis működik és fut, ellenőrizze, hogy az összes szükséges engedély be van-e állítva. Ha az engedélyek bármelyike hiányzik, futtassa az [Előregisztráció parancsfájlt](https://aka.ms/scriptforpermsonhana) a hiányzó engedélyek hozzáadásához. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Hibaüzenet      | <span style="font-weight:normal">A megadott SAP HANA példány érvénytelen vagy nem található.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Egyetlen Azure-beli virtuális gépen több SAP HANA példányról nem készíthető biztonsági másolat. |
| **Javasolt művelet** | Futtassa az [előregisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) arra a SAP HANA példányra, amelyről biztonsági másolatot szeretne készíteni. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Hibaüzenet      | <span style="font-weight:normal">A megadott SAP HANA művelet nem támogatott</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A SAP HANA Azure Backup nem támogatja a növekményes biztonsági mentést és a SAP HANA natív ügyfeleken végrehajtott műveleteket (Studio/cockpit/DBA pilótafülke) |
| **Javasolt művelet** | További [információkért tekintse](./sap-hana-backup-support-matrix.md#scenario-support)meg a következő témakört:. |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Hibaüzenet      | <span style="font-weight:normal">A biztonsági mentési napló lánca megszakadt</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Lehetséges, hogy a napló biztonsági mentésének célhelye a backint-ről a fájlrendszerre lett frissítve, vagy a backint végrehajtható fájl módosítva lett. |
| **Javasolt művelet** | A probléma megoldásához indítson el egy teljes biztonsági mentést                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Hibaüzenet      | <span style="font-weight:normal">SDC a MDC frissítését észlelte</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A SAP HANA példány frissítve lett a SDC-ről a MDC-re. A frissítés után a biztonsági mentések sikertelenek lesznek. |
| **Javasolt művelet** | A probléma megoldásához kövesse a SDC-ben felsorolt lépéseket a [MDC frissítéséhez](#sdc-to-mdc-upgrade-with-a-change-in-sid) |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hibaüzenet      | <span style="font-weight:normal">A rendszer érvénytelen backint-konfigurációt észlelt</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Helytelenül vannak megadva a háttérbeli paraméterek Azure Backup |
| **Javasolt művelet** | Ellenőrizze, hogy a következő (backint) paraméterek be vannak-e állítva:<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: FALSE]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Ha backint paraméterek találhatók a GAZDAGÉPen, távolítsa el őket. Ha a paraméterek nem találhatók meg a GAZDAGÉP szintjén, de az adatbázis szintjén manuálisan lettek módosítva, állítsa azokat a megfelelő értékekre a korábban leírtak szerint. Vagy futtassa a [védelem leállítása és a biztonsági mentési adatok megőrzése](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) a Azure Portal, majd válassza a **biztonsági mentés folytatása** lehetőséget. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Hibaüzenet  |A visszaállításhoz használt forrás-és célhelyek nem kompatibilisek  |
|---------|---------|
|Lehetséges okok   | A visszaállításra kijelölt forrás-és célként használt rendszerek nem kompatibilisek        |
|Javasolt művelet   |   Győződjön meg arról, hogy a visszaállítási forgatókönyv nem szerepel a lehetséges inkompatibilis visszatárolások következő listáján: <br><br>   **1. eset:** A SYSTEMDB nem nevezhető át a visszaállítás során.  <br><br> **2. eset:** Forrás-SDC és cél-MDC: a forrásadatbázis nem állítható vissza SYSTEMDB vagy bérlői adatbázisként a célhelyen. <br><br> **3. eset:** Forrás-MDC és cél-SDC: a forrásadatbázis (SYSTEMDB vagy bérlői adatbázis) nem állítható vissza a célhelyre. <br><br>  További információ: **1642148** -es Megjegyzés az [SAP support kezdőpanelban](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Visszaállítási ellenőrzések

### <a name="single-container-database-sdc-restore"></a>Egy tároló-adatbázis (SDC) visszaállítása

Ügyeljen a bevitelre, miközben egyetlen tároló-adatbázist (SDC) állít vissza a HANA-hoz egy másik SDC-gépre. Az adatbázis nevét kisbetűs értékkel kell megadni, és zárójelek között a "SDC" utótaggal kell kiegészíteni. A HANA-példány a fővárosokban fog megjelenni.

Tegyük fel, hogy a "H21" SDC HANA-példányról biztonsági másolat készül. A biztonsági mentési elemek lapon megjelenik a biztonsági mentési elem neve **"H21 (SDC)"** néven. Ha megpróbálja visszaállítani az adatbázist egy másik cél SDC, tegyük fel, hogy a H11 a következő bemeneti adatokat kell megadni.

![Visszaállított SDC-adatbázis neve](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Vegye figyelembe a következő szempontokat:

- Alapértelmezés szerint a visszaállított adatbázis neve a biztonsági mentési elem nevével lesz feltöltve. Ebben az esetben a H21 (SDC).
- A cél kiválasztása, mivel a H11 nem módosítja automatikusan a visszaállított adatbázis nevét. **Ezt a H11 (SDC) kell szerkeszteni**. A SDC kapcsolatban a visszaállított adatbázis neve a célként megadott példány-azonosító kisbetűkkel és a "SDC" zárójelek között.
- Mivel a SDC csak egyetlen adatbázissal rendelkezhet, a jelölőnégyzet bejelölésével engedélyezheti a meglévő adatbázis-adatmennyiség felülbírálását a helyreállítási pontra vonatkozó adattal.
- A Linux megkülönbözteti a kis-és nagybetűket. Ezért ügyeljen arra, hogy megőrizze a kis-és nagybetűket.

### <a name="multiple-container-database-mdc-restore"></a>Több tároló-adatbázis (MDC) visszaállítása

A HANA-hoz készült több Container Database-ben a standard konfiguráció SYSTEMDB + 1 vagy több bérlői adatbázis. A teljes SAP HANA példány visszaállítása a SYSTEMDB és a bérlői adatbázisok visszaállítását jelenti. Először az egyik visszaállítja a SYSTEMDB, majd a bérlői adatbázist folytatja. A rendszeradatbázis lényegében azt jelenti, hogy felülbírálja a kiválasztott cél rendszerinformációit. Ez a visszaállítás felülbírálja a BackInt kapcsolatos információkat is a cél példányban. Tehát miután a rendszeradatbázist visszaállította egy cél példányra, futtassa újra az előzetes regisztrációs parancsfájlt. A következő bérlői adatbázis-visszaállítások sikeresek lesznek.

## <a name="back-up-a-replicated-vm"></a>Replikált virtuális gép biztonsági mentése

### <a name="scenario-1"></a>1\. példa

Az eredeti virtuális gépet Azure Site Recovery vagy Azure-beli virtuális gép biztonsági másolatának használatával replikálták. Az új virtuális gép úgy lett felépítve, hogy szimulálja a régi virtuális gépet. Vagyis a beállítások pontosan ugyanazok. (Ennek az az oka, hogy az eredeti virtuális gép törölve lett, és a visszaállítás a virtuális gép biztonsági másolatából vagy Azure Site Recovery) történt.

Ez a forgatókönyv két lehetséges esetet tartalmazhat. Ismerje meg, hogyan készíthet biztonsági mentést a replikált virtuális gépről mindkét esetben:

1. Az új létrehozott virtuális gép neve azonos, és ugyanabban az erőforráscsoportban és előfizetésben található, mint a törölt virtuális gép.

    - A bővítmény már megtalálható a virtuális gépen, de a szolgáltatások egyike sem látható
    - Az előzetes regisztrációs parancsfájl futtatása
    - Regisztrálja újra a bővítményt ugyanarra a gépre vonatkozóan a Azure Portalban (**biztonsági mentési**  ->  **nézet részletei** – > válassza ki a megfelelő Azure-beli virtuális gépet – > újra regisztrálja)
    - A már meglévő biztonsági másolati adatbázisok (a törölt virtuális gépről) sikeresen elindulnak a biztonsági mentés során

2. Az új létrehozott virtuális gép a következők valamelyikével rendelkezik:

    - egy másik név, mint a törölt virtuális gép
    - ugyanaz a neve, mint a törölt virtuális gép, de egy másik erőforráscsoport vagy előfizetés (a törölt virtuális géphez képest)

    Ebben az esetben hajtsa végre a következő lépéseket:

    - A bővítmény már megtalálható a virtuális gépen, de a szolgáltatások egyike sem látható
    - Az előzetes regisztrációs parancsfájl futtatása
    - Ha felderíti és megóvja az új adatbázisokat, megtekintheti az ismétlődő aktív adatbázisokat a portálon. Ennek elkerüléséhez [állítsa le a védelmet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) a régi adatbázisok megőrzéséhez. Ezután folytassa a fennmaradó lépésekkel.
    - A biztonsági mentést lehetővé tevő adatbázisok felderítése
    - Biztonsági másolatok engedélyezése ezeken az adatbázisokon
    - A már meglévő biztonsági másolati adatbázisok (a törölt virtuális gépről) továbbra is a tárolóban lesznek tárolva (a biztonsági mentések a szabályzatnak megfelelően megmaradnak)

### <a name="scenario-2"></a>2\. példa

Az eredeti virtuális gépet Azure Site Recovery vagy Azure-beli virtuális gép biztonsági másolatának használatával replikálták. Az új virtuális gép a tartalomból lett kiépítve – sablonként való használatra. Ez egy új biztonsági azonosítóval rendelkező virtuális gép.

Az alábbi lépéseket követve engedélyezheti a biztonsági mentéseket az új virtuális gépen:

- A bővítmény már megtalálható a virtuális gépen, de nem látható a szolgáltatások egyikén sem
- Futtassa az előzetes regisztrációs parancsfájlt. Az új virtuális gép SID-je alapján két forgatókönyv fordulhat elő:
  - Az eredeti virtuális gép és az új virtuális gép ugyanazzal a SID-vel rendelkezik. Az előzetes regisztrációs parancsfájl futtatása sikeresen megtörténik.
  - Az eredeti virtuális gép és az új virtuális gép eltérő biztonsági azonosítóval rendelkezik. Az előzetes regisztrációs parancsfájl sikertelen lesz. Ha segítséget szeretne kapni ebben a forgatókönyvben, forduljon az ügyfélszolgálathoz.
- Fedezze fel azokat az adatbázisokat, amelyekről biztonsági másolatot szeretne készíteni
- Biztonsági másolatok engedélyezése ezeken az adatbázisokon

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>A SDC verziófrissítése vagy a MDC verziófrissítése ugyanazon a virtuális gépen

Az operációs rendszer, a SDC verziójának változása vagy a MDC változása nem okozhatja a SID-változások kezelését a következőképpen:

- Győződjön meg arról, hogy az új operációsrendszer-verzió, a SDC vagy a MDC verziója jelenleg [támogatott a Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- A [védelem leállítása](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) az adatbázis megőrzése érdekében
- A frissítés vagy a frissítés végrehajtása
- Futtassa újra az előzetes regisztrációs parancsfájlt. A frissítési folyamat gyakran el is távolíthatja [a szükséges szerepköröket](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). Az előzetes regisztrációs szkript futtatása segít ellenőrizni az összes szükséges szerepkört.
- Az adatbázis védelmének újbóli folytatása

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC a MDC frissítéséhez a SID módosítása nélkül

A SDC-ről a MDC-re irányuló frissítések a következőképpen kezelhetők:

- Győződjön meg arról, hogy az új MDC-verziót jelenleg a [Azure Backup támogatja](sap-hana-backup-support-matrix.md#scenario-support)
- A [védelem leállítása](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) a régi SDC-adatbázis megőrzése érdekében
- Végezze el a frissítést. A befejezést követően a HANA rendszer most már MDC a System DB és a bérlői adatbázisok
- Az [előzetes regisztrációs parancsfájl](https://aka.ms/scriptforpermsonhana) újrafuttatása
- Regisztrálja újra a bővítményt ugyanarra a gépre vonatkozóan a Azure Portalban (**biztonsági mentési**  ->  **nézet részletei** – > válassza ki a megfelelő Azure-beli virtuális gépet – > újra regisztrálja)
- Válassza az egyazon virtuális géphez tartozó adatbázisok újbóli **felderítése** elemet. Ez a művelet a 3. lépésben szereplő új adatbázisok megjelenítését mutatja be SYSTEMDB és bérlői ADATBÁZISként, nem SDC
- A régebbi SDC-adatbázis továbbra is a tárolóban marad, és a szabályzatnak megfelelően megőrzött a régi biztonsági másolati adat.
- Az adatbázisok biztonsági mentésének konfigurálása

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC a MDC frissítéséhez a SID módosításával

A SDC-ről a MDC-re történő frissítés a következő módon kezelhető:

- Győződjön meg arról, hogy az új MDC-verziót jelenleg a [Azure Backup támogatja](sap-hana-backup-support-matrix.md#scenario-support)
- A **védelem leállítása** a régi SDC-adatbázis megőrzése érdekében
- Végezze el a frissítést. A befejezést követően a HANA rendszer most már MDC a System DB és a bérlői adatbázisok
- Futtassa újra az [előzetes regisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) a megfelelő részletekkel (új SID és MDC). A SID-változás miatt előfordulhat, hogy a parancsfájl sikeres futtatásával problémákba ütközik. Ha problémákba ütközik, lépjen kapcsolatba Azure Backup támogatási szolgálattal.
- Regisztrálja újra a bővítményt ugyanarra a gépre vonatkozóan a Azure Portalban (**biztonsági mentési**  ->  **nézet részletei** – > válassza ki a megfelelő Azure-beli virtuális gépet – > újra regisztrálja)
- Válassza az egyazon virtuális géphez tartozó adatbázisok újbóli **felderítése** elemet. Ez a művelet a 3. lépésben szereplő új adatbázisok megjelenítését mutatja be SYSTEMDB és bérlői ADATBÁZISként, nem SDC
- A régebbi SDC-adatbázis továbbra is a tárolóban marad, és a szabályzatnak megfelelően megőrizte a régi biztonsági mentést.
- Az adatbázisok biztonsági mentésének konfigurálása

## <a name="re-registration-failures"></a>Ismételt regisztrálási hibák

A következő tünetek közül egyet vagy többet kell megvizsgálnia az ismételt regisztrálási művelet elindítása előtt:

- Az összes művelet (például a biztonsági mentés, a visszaállítás és a biztonsági mentés konfigurálása) a virtuális gépen meghiúsul a következő hibakódok egyikével: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Ha a biztonsági mentési elem **biztonsági mentési állapota** területen **nem érhető el**, akkor zárja ki az összes többi olyan okot, amely ugyanazokat az állapotot eredményezheti:

  - Nincs engedélye a biztonsági mentéssel kapcsolatos műveletek elvégzésére a virtuális gépen
  - A virtuális gép le van állítva, ezért a biztonsági mentések nem hajthatók végre
  - Hálózati problémák

Ezek a tünetek a következő okok valamelyike miatt merülhetnek fel:

- A bővítmény törölve lett vagy el lett távolítva a portálról.
- A virtuális gép időben vissza lett állítva a helyi lemez visszaállítása révén.
- A virtuális gép leállt egy hosszabb ideig, így lejárt a bővítmény konfigurációja.
- A virtuális gép törölve lett, és egy másik virtuális gép lett létrehozva ugyanazzal a névvel és ugyanabban az erőforráscsoporthoz, mint a törölt virtuális gép.

Az előző forgatókönyvekben azt javasoljuk, hogy a virtuális gépen újra regisztrálja a műveletet.

## <a name="next-steps"></a>Következő lépések

- Tekintse át az Azure-beli virtuális gépeken SAP HANA adatbázisok biztonsági mentésével kapcsolatos [gyakori kérdéseket](./sap-hana-faq-backup-azure-vm.md) .
