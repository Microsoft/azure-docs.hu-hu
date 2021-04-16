---
title: Az SAP HANA biztonsági mentési hibáinak elhárítása
description: Ez a cikk azt ismerteti, hogyan háríthatja el azokat a gyakori hibákat, amelyek akkor fordulhatnak elő, Azure Backup biztonsági SAP HANA biztonsági mentésekor.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: cdf4c26aa32d65ec63ec84d85e454adaaf2ece8d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517232"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure-beli SAP HANA biztonsági mentésének hibaelhárítása

Ez a cikk az Azure-beli virtuális gépeken SAP HANA adatbázisok biztonsági mentése hibaelhárítási információkat tartalmaz. A jelenleg támogatott biztonsági mentési SAP HANA további információért lásd: [Forgatókönyv támogatása.](sap-hana-backup-support-matrix.md#scenario-support)

## <a name="prerequisites-and-permissions"></a>Előfeltételek és engedélyek

A biztonsági mentések [konfigurálása](tutorial-backup-sap-hana-db.md#prerequisites) [előtt](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) tekintse meg az előfeltételeket és a Mit csinál az előzetes regisztrációs szkript? szakaszt.

## <a name="common-user-errors"></a>Gyakori felhasználói hibák

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Hibaüzenet**      | <span style="font-weight:normal">Azure Backup nem rendelkezik a biztonsági mentés elvégzéséhez szükséges szerepköri jogosultságokkal</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Lehetséges okok**    | Előfordulhat, hogy a szerepkört felülírták.                          |
| **Javasolt művelet** | A probléma megoldásához futtassa a szkriptet a **Felderítési adatbázis** panelen, vagy töltse le [ide.](https://aka.ms/scriptforpermsonhana) Másik lehetőségként adja hozzá a "SAP_INTERNAL_HANA_SUPPORT" szerepkört a Workload Backup-felhasználóhoz (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Hibaüzenet      | <span style="font-weight:normal">Nem sikerült csatlakozni a HANA-rendszerhez</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Előfordulhat SAP HANA hogy a példány nem omlott.<br/>A HANA-Azure Backup való kommunikációhoz szükséges engedélyek nincsenek beállítva. |
| **Javasolt művelet** | Ellenőrizze, hogy SAP HANA-e az adatbázis. Ha az adatbázis működik, ellenőrizze, hogy az összes szükséges engedély be van-e állítva. Ha az engedélyek bármelyike hiányzik, futtassa az [előregistration](https://aka.ms/scriptforpermsonhana) szkriptet a hiányzó engedélyek hozzáadásához. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Hibaüzenet      | <span style="font-weight:normal">A SAP HANA példány érvénytelen vagy nem található</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Több SAP HANA azure-beli virtuális gépen nem lehet biztonságimentni. |
| **Javasolt művelet** | Futtassa [az előkészítő](https://aka.ms/scriptforpermsonhana) szkriptet a SAP HANA példányon, amelyről biztonsági szeretne biztonsági példányt. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Hibaüzenet      | <span style="font-weight:normal">A megadott SAP HANA művelet nem támogatott</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Azure Backup a SAP HANA nem támogatja a növekményes biztonsági mentést és a natív ügyfeleken (Studio/ SAP HANA Vagy DBA-adatbázison) végrehajtott műveleteket |
| **Javasolt művelet** | További információért tekintse meg a [következőt:](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Hibaüzenet      | <span style="font-weight:normal">A biztonsági mentési napló lánca megszakadt</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Előfordulhat, hogy a napló biztonsági mentési célhelyét a backintről a fájlrendszerre frissítették, vagy a backint végrehajtható fájlja módosult |
| **Javasolt művelet** | Teljes biztonsági mentés aktiválása a probléma megoldásához                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Hibaüzenet      | <span style="font-weight:normal">SDC–MDC frissítés észlelhető</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Az SAP HANA-példány frissítve lett az SDC-ről az MDC-re. A biztonsági mentések sikertelenek lesznek a frissítés után. |
| **Javasolt művelet** | A probléma megoldásához kövesse az [SDC–MDC](#sdc-to-mdc-upgrade-with-a-change-in-sid) frissítésben felsorolt lépéseket |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hibaüzenet      | <span style="font-weight:normal">A rendszer érvénytelen backint-konfigurációt észlelt</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A háttérparaméterek helytelenül vannak megadva a Azure Backup |
| **Javasolt művelet** | Ellenőrizze, hogy be vannak-e állítva a következő (backint) paraméterek:<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Ha vannak backint-alapú paraméterek a HOST-ban, távolítsa el őket. Ha a paraméterek nincsenek jelen a GAZDAGÉP szinten, de manuálisan, adatbázisszinten lett módosítva, a korábbiakban leírtak szerint vissza kell állítani őket a megfelelő értékekre. Vagy futtassa a [védelem leállítását,](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) és őrizze meg a biztonsági mentési adatokat a Azure Portal, majd válassza a **Biztonsági mentés folytatása lehetőséget.** |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Hibaüzenet  |A visszaállítás forrás- és célrendszere nem kompatibilis  |
|---------|---------|
|Lehetséges okok   | A visszaállításhoz kiválasztott forrás- és célrendszerek nem kompatibilisek        |
|Javasolt művelet   |   Győződjön meg arról, hogy a visszaállítási forgatókönyv nem szerepel a lehetséges inkompatibilis visszaállítások alábbi listájában: <br><br>   **1. eset:** A SYSTEMDB nem nevezhető át a visszaállítás során.  <br><br> **2. eset:** Forrás – SDC és cél – MDC: A forrásadatbázis nem állítható vissza SYSTEMDB- vagy bérlőadatbázisként a célon. <br><br> **3. eset:** Forrás – MDC és cél – SDC: A forrásadatbázis (SYSTEMDB vagy bérlői adatbázis) nem állítható vissza a célra. <br><br>  További információért tekintse meg a **1642148-as** megjegyzést az [SAP támogatási indítópultban.](https://launchpad.support.sap.com) |

## <a name="restore-checks"></a>Visszaállítási ellenőrzések

### <a name="single-container-database-sdc-restore"></a>Egytárolós adatbázis (SDC) visszaállítása

A HANA egyetlen tárolóadatbázisának (SDC) egy másik SDC-gépre való visszaállítása közben kell gondoskodnia a bemenetekről. Az adatbázis nevét kisbetűvel kell adni, az "sdc" pedig szögletes zárójelben. A HANA-példány nagybetűkben jelenik meg.

Tegyük fel, hogy egy "H21" SDC HANA-példányról biztonságimentett. A biztonsági mentési elemek oldalán a biztonsági mentési elem neve **"h21(sdc)" lesz.** Ha megpróbálja visszaállítani ezt az adatbázist egy másik cél SDC-re, például h11-re, akkor a következő bemeneteket kell biztosítani.

![Visszaállított SDC-adatbázis neve](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Vegye figyelembe a következő szempontokat:

- Alapértelmezés szerint a visszaállított adatbázis neve a biztonságimásolat-elem nevével lesz kitöltve. Ebben az esetben h21(sdc).
- A cél H11-ként való kiválasztása nem módosítja automatikusan a visszaállított adatbázis nevét. **A parancsnak a h11(sdc)** gombra kell módosítania. Az SDC-vel kapcsolatban a visszaállított adatbázisnév lesz a célpéldány azonosítója, amely kisbetűket tartalmaz, az "sdc" pedig szögletes zárójelben.
- Mivel az SDC csak egyetlen adatbázissal is képes, be kell jelölje be a jelölőnégyzetet a meglévő adatbázisadatok felülbírálására a helyreállítási pont adataival.
- A Linux megkülönbözteti a kis- és nagybetűket. Ezért ügyeljen az eset megőrzésére.

### <a name="multiple-container-database-mdc-restore"></a>Több tárolóadatbázis (MDC) visszaállítása

A HANA több tárolóadatbázisában a standard konfiguráció a SYSTEMDB + 1 vagy több bérlői adatbázis. Egy teljes adatbázispéldány SAP HANA systemdb és bérlői adatbázisok visszaállítását jelenti. Az egyik először visszaállítja a SYSTEMDB adatbázist, majd folytatja a Bérlői adatbázist. A rendszer-adatbázis lényegében azt jelenti, hogy felülbírálja a kiválasztott cél rendszerinformációját. Ez a visszaállítás felülbírálja a célpéldány BackInt-hez kapcsolódó információit is. Tehát miután a rendszer-adatbázis visszaáll egy célpéldányra, futtassa újra az előregisztrációs szkriptet. Csak az ezt követő bérlői adatbázis-visszaállítások lesznek sikeresek.

## <a name="back-up-a-replicated-vm"></a>Replikált virtuális gép biztonsági visszaállítása

### <a name="scenario-1"></a>1\. példa

Az eredeti virtuális gép replikálása egy virtuális Azure Site Recovery azure-beli virtuális gép biztonsági mentésének használatával történt. Az új virtuális gép a régi virtuális gép szimulálására lett felépítve. Ez azt jelenti, hogy a beállítások pontosan ugyanazok. (Ennek az az oka, hogy az eredeti virtuális gép törölve lett, és a visszaállítás a virtuális gép biztonsági másolatából vagy az Azure Site Recovery).

Ez a forgatókönyv két lehetséges esetet tartalmazhat. Ismerje meg, hogyan lehet biztonsági adatokat biztonságira másolni a replikált virtuális gépről mindkét esetben:

1. A létrehozott új virtuális gép neve megegyezik, és ugyanabban az erőforráscsoportban és előfizetésben található, mint a törölt virtuális gép.

    - A bővítmény már jelen van a virtuális gépen, de egyik szolgáltatás számára sem látható
    - Az előzetes regisztrációs szkript futtatása
    - Regisztrálja újra ugyanannak a gépnek a bővítményét a Azure Portal **(** Biztonsági mentési nézet részletei -> válassza ki a megfelelő Azure-beli virtuális gépet  ->   >-regisztrációt)
    - A már meglévő biztonságimentett adatbázisoknak (a törölt virtuális gépről) sikeresen el kell kezdenie a biztonsági mentéset

2. A létrehozott új virtuális gép a következő két okkal rendelkezik:

    - a törölt virtuális gép nevétől eltérő név
    - ugyanaz a név, mint a törölt virtuális gépnek, de más erőforráscsoportban vagy előfizetésben található (a törölt virtuális géphez képest)

    Ebben az esetben tegye a következőket:

    - A bővítmény már jelen van a virtuális gépen, de egyik szolgáltatás számára sem látható
    - Az előzetes regisztrációs szkript futtatása
    - Ha felderíti és védi az új adatbázisokat, ismétlődő aktív adatbázisokat fog látni a portálon. Ennek elkerülése érdekében állítsa le a [védelmet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) a régi adatbázisok adatainak megőrzésével. Ezután folytassa a további lépésekkel.
    - Adatbázisok felderítése a biztonsági mentés engedélyezéséhez
    - Biztonsági mentések engedélyezése ezeken az adatbázisokon
    - A már meglévő biztonsági másolatba került adatbázisok (a törölt virtuális gépről) továbbra is a tárolóban lesznek tárolva (a biztonsági másolataik a szabályzatnak megfelelően lesznek megőrizve)

### <a name="scenario-2"></a>2\. példa

Az eredeti virtuális gép replikálása Azure Site Recovery Azure-beli virtuális gép biztonsági mentésének használatával történt. Az új virtuális gép a tartalomból lett kiépítve – sablonként használható. Ez egy új virtuális gép, új SID azonosítóval.

Kövesse az alábbi lépéseket a biztonsági mentések engedélyezéséhez az új virtuális gépen:

- A bővítmény már jelen van a virtuális gépen, de egyik szolgáltatás számára sem látható
- Futtassa az előzetes regisztrációs szkriptet. Az új virtuális gép biztonsági azonosítója alapján két forgatókönyv merülhet fel:
  - Az eredeti virtuális gép és az új virtuális gép biztonsági azonosítója megegyezik. Az előzetes regisztrációs szkript sikeresen le fog futni.
  - Az eredeti virtuális gép és az új virtuális gép eltérő BIZTONSÁGI okkal van megszűkve. Az előzetes regisztrációs szkript sikertelen lesz. Ha segítségre van szüksége ebben a forgatókönyvben, forduljon az ügyfélszolgálathoz.
- Felderítheti az adatbázisokat, amelyekről biztonsági mentéset szeretne.
- Biztonsági mentések engedélyezése ezeken az adatbázisokon

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>SDC-verziófrissítés vagy MDC-verziófrissítés ugyanazon a virtuális gépen

Az operációs rendszer, az SDC-verzióváltás vagy az MDC-verzió olyan módosítása, amely nem okoz SID-változást, a következőképpen kezelhető:

- Győződjön meg arról, hogy az új operációsrendszer-, SDC- vagy MDC-verziót a Azure Backup [](sap-hana-backup-support-matrix.md#scenario-support)
- [A védelem leállítása az adatbázis adatainak](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) megőrzésével
- Frissítés vagy frissítés végrehajtása
- Futtassa újra az előzetes regisztrációs szkriptet. A frissítési folyamat gyakran eltávolítja a [szükséges szerepköröket.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) Az előzetes regisztrációs szkript futtatása segít ellenőrizni az összes szükséges szerepkört.
- Az adatbázis védelmének újraindítása

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC-ről MDC-re való frissítés a SID módosítása nélkül

Az SDC-ről az MDC-be való frissítés, amely nem okoz biztonsági azonosító-változást, a következőképpen kezelhető:

- Győződjön meg arról, hogy az új MDC-verziót [a](sap-hana-backup-support-matrix.md#scenario-support) Azure Backup
- [Védelem leállítása a](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) régi SDC-adatbázis adatainak megőrzésével
- Hajtsa végre a frissítést. A befejezést követően a HANA-rendszer MDC lesz rendszer-adatbázissal és bérlői adatbázisokkal
- Az előzetes regisztrációs [szkript újrafuttat](https://aka.ms/scriptforpermsonhana)
- Regisztrálja újra ugyanannak a gépnek a bővítményét a Azure Portal **(** Biztonsági mentési nézet részletei -> válassza ki a megfelelő Azure-beli virtuális gépet  ->   >-regisztrációt)
- Válassza **az Ugyanazon virtuális gép adatbázisának** újrakérte lehetőséget. Ennek a műveletnek a 3. lépésben SYSTEMDB-ként és bérlői adatbázisként kell mutatnia az új adatbázisokat, nem pedig SDC-ként
- A régebbi SDC-adatbázis továbbra is létezni fog a tárolóban, és a szabályzatnak megfelelően megőrzi a régi biztonságimentett adatokat
- Az adatbázisok biztonsági mentésének konfigurálása

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC-ről MDC-re való frissítés biztonsági azonosítóval való módosítással

Az SDC-ről az MDC-re való frissítés, amely biztonsági azonosítóváltást okoz, a következőképpen kezelhető:

- Győződjön meg arról, hogy az új MDC-verziót [a](sap-hana-backup-support-matrix.md#scenario-support) Azure Backup
- **Védelem leállítása a** régi SDC-adatbázis adatainak megőrzésével
- Hajtsa végre a frissítést. A befejezést követően a HANA-rendszer MDC lesz rendszer-adatbázissal és bérlői adatbázisokkal
- Futtassa újra [az előzetes regisztrációs szkriptet](https://aka.ms/scriptforpermsonhana) a megfelelő adatokkal (új SID és MDC). A biztonsági azonosítók módosítása miatt problémákat okozhat a szkript sikeres futtatása. Ha problémákba Azure Backup forduljon az ügyfélszolgálathoz.
- Regisztrálja újra ugyanannak a gépnek a bővítményét a Azure Portal **(** Biztonsági mentési nézet részletei -> Válassza ki a megfelelő Azure-beli virtuális gépet  ->   >-regisztrációt)
- Válassza **az Ugyanazon virtuális gép adatbázisának újraszibabadulása** lehetőséget. Ennek a műveletnek a 3. lépésben SYSTEMDB és Tenant DB ként kell mutatnia az új adatbázisokat, nem pedig SDC-ként
- A régebbi SDC-adatbázis továbbra is létezni fog a tárolóban, és a szabályzatnak megfelelően megőrzi a régi biztonságimentett adatokat
- Az adatbázisok biztonsági mentésének konfigurálása

## <a name="re-registration-failures"></a>Újraregisztrációs hibák

Az újra regisztráló művelet aktiválása előtt ellenőrizze az alábbi jelenségek közül egyet vagy többet:

- Minden művelet (például biztonsági mentés, visszaállítás és biztonsági mentés konfigurálása) sikertelen a virtuális gépen a következő hibakódok egyikével: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg.**
- Ha **a biztonsági** mentési elem Biztonsági mentés állapota területén a **Nem** elérhető jelenik meg, zárja ki az összes többi olyan okét, amelyek ugyanezt az állapotot eredményezhetik:

  - Nincs engedélye a biztonsági mentéssel kapcsolatos műveletek végrehajtásához a virtuális gépen
  - A virtuális gép le van állított, így nem lehet biztonsági mentéseket készíteni
  - Hálózati problémák

Ezek a tünetek az alábbi okok közül egy vagy több miatt jelentkezhetnek:

- Egy bővítményt töröltek vagy eltávolítottak a portálról.
- A virtuális gépet visszaállították az időben a lemez egy helyhez való visszaállítása során.
- A virtuális gép hosszabb időre leállt, ezért a bővítménykonfiguráció lejárt.
- A virtuális gép törölve lett, és egy másik virtuális gép is létrejött ugyanazokkal a névvel és erőforráscsoportban, mint a törölt virtuális gép.

Az előző forgatókönyvekben javasoljuk, hogy aktiváljon egy újra regisztrálási műveletet a virtuális gépen.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [az](./sap-hana-faq-backup-azure-vm.yml) Azure-beli virtuális gépeken SAP HANA adatbázisok biztonsági mentése során gyakran feltett kérdéseket.
