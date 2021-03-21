---
title: Windows rendszerű gépek biztonsági mentése a MARS-ügynök használatával
description: A Windows rendszerű gépek biztonsági mentéséhez használja a Microsoft Azure Recovery Services (MARS) ügynököt.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54932192d61633da55657e2ba57adf4e30c4fbc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702768"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Windows Server-fájlok és-mappák biztonsági mentése az Azure-ba

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni a Windows rendszerű gépekről a [Azure Backup](backup-overview.md) szolgáltatás és a Microsoft Azure Recovery Services (MARS) ügynök használatával. A MARSot Azure Backup ügynöknek is nevezzük.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
>
> * Az előfeltételek ellenőrzése
> * Hozzon létre egy biztonsági mentési szabályzatot és az ütemtervet.
> * Végezzen el egy igény szerinti biztonsági mentést.

## <a name="before-you-start"></a>Előkészületek

* Megtudhatja [, hogyan használja a Azure Backup a Mars-ügynököt a Windows rendszerű gépek biztonsági mentésére](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Ismerje meg a MARS-ügynököt egy másodlagos MABS vagy Data Protection Manager kiszolgálón futtató [biztonsági mentési architektúrát](backup-architecture.md#architecture-back-up-to-dpmmabs) .
* Tekintse át a támogatott és a MARS-ügynök által [használható biztonsági mentést](backup-support-matrix-mars-agent.md) .
* [Ellenőrizze az internet-hozzáférést](install-mars-agent.md#verify-internet-access) azon gépeken, amelyekről biztonsági másolatot szeretne készíteni.
* Ha a MARS-ügynök nincs telepítve, Ismerje meg, hogyan telepítheti [itt](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési házirend azt adja meg, hogy mikor kell pillanatképeket készíteni a helyreállítási pontok létrehozásához. Azt is megadja, hogy mennyi ideig tart a helyreállítási pontok megőrzése. A MARS-ügynök használatával konfigurálhatja a biztonsági mentési szabályzatot.

Azure Backup nem veszi figyelembe automatikusan a nyári időszámítást (DST). Ez az alapértelmezett érték bizonyos eltéréseket okozhat a tényleges idő és az ütemezett biztonsági mentés ideje között.

Biztonsági mentési szabályzat létrehozása:

1. A MARS-ügynök letöltése és regisztrálása után nyissa meg az ügynök konzolját. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.  

1. A **műveletek** területen válassza a **biztonsági mentés időzítése** elemet.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)
1. Az ütemezett biztonsági mentés varázslóban válassza az **első lépések** lehetőséget  >  .
1. Az **elemek kiválasztása a biztonsági mentéshez** területen válassza az **elemek hozzáadása** lehetőséget.

    ![Elemek hozzáadása a biztonsági mentéshez](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Az **elemek kiválasztása** mezőben válassza ki a biztonsági mentésre kijelölt elemeket, majd kattintson **az OK gombra**.

    ![Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Az **elemek kiválasztása a biztonsági mentéshez** lapon válassza a **tovább** lehetőséget.
1. A **biztonsági mentési ütemezés megadása** lapon határozza meg, hogy mikor kell napi vagy heti biztonsági mentést készítenie. Ezután kattintson a **Tovább** gombra.

    * A biztonsági másolat létrehozásakor létrejön egy helyreállítási pont.
    * A környezetben létrehozott helyreállítási pontok száma a biztonsági mentési ütemtervtől függ.
    * Naponta legfeljebb három napi biztonsági mentést lehet ütemezni. A következő példában két napi biztonsági mentés következik be, egy éjfélkor, egy pedig 6:00 ÓRAKOR.

        ![Napi biztonsági mentési ütemterv beállítása](./media/backup-configure-vault/day-schedule.png)

    * A heti biztonsági mentéseket is futtathatja. A következő példában a biztonsági másolatok minden második vasárnap és szerdán, 9:30-kor és 1:00-kor találhatók.

        ![Heti biztonsági mentési ütemterv beállítása](./media/backup-configure-vault/week-schedule.png)

1. Az **adatmegőrzési szabály kiválasztása** lapon adja meg, hogyan szeretné tárolni az adatok korábbi másolatait. Ezután kattintson a **Tovább** gombra.

    * Az adatmegőrzési beállítások határozzák meg, hogy mely helyreállítási pontok legyenek tárolva, és mennyi ideig kell tárolni őket.
    * Napi adatmegőrzési beállítás esetén azt jelzi, hogy a napi megőrzéshez megadott idő elteltével a rendszer a megadott számú napig megőrzi a legutóbbi helyreállítási pontot. Vagy megadhat egy havi adatmegőrzési szabályt, amely azt jelzi, hogy a minden hónap 30-án létrehozott helyreállítási pontot 12 hónapig kell tárolni.
    * A napi és heti helyreállítási pontok megőrzése általában egybeesik a biztonsági mentési ütemtervvel. Tehát amikor az ütemterv elindít egy biztonsági mentést, a biztonsági másolat által létrehozott helyreállítási pont a napi vagy heti adatmegőrzési szabályzat által meghatározott időtartamra tárolódik.
    * A következő példában:

        * A napi biztonsági másolatok éjfélkor és 6:00 ÓRAKOR tartanak hét napig.
        * A szombaton éjfélkor és 6:00 ÓRAKOR készített biztonsági másolatok négy hétig tartanak.
        * A hónap utolsó szombatján éjfélkor és 6:00 ÓRAKOR készített biztonsági mentések 12 hónapig tartanak.
        * A március utolsó szombaton készített biztonsági másolatok 10 évig tartanak.

        ![Adatmegőrzési szabály – példa](./media/backup-configure-vault/retention-example.png)

1. A **kezdeti biztonsági mentés típusának kiválasztása** lapon döntse el, hogy szeretné-e a kezdeti biztonsági mentést végezni a hálózaton keresztül, vagy az offline biztonsági mentést. Ha a kezdeti biztonsági mentést a hálózaton keresztül szeretné végrehajtani, válassza a **automatikusan a hálózaton keresztül lehetőséget a**  >  **tovább** gombra.

    További információ az offline biztonsági mentésről: [a Azure Data Box használata az offline biztonsági mentéshez](offline-backup-azure-data-box.md).

    ![Válasszon kezdeti biztonsági mentési típust](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. A **jóváhagyás** lapon tekintse át az információkat, majd kattintson a **Befejezés gombra**.

    ![A biztonsági mentés típusának megerősítése](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Miután a varázsló befejezte a biztonsági mentési ütemterv létrehozását, válassza a **Bezárás** lehetőséget.

    ![A biztonsági mentési ütemterv előrehaladásának megtekintése](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Hozzon létre egy szabályzatot minden olyan gépen, amelyen az ügynök telepítve van.

### <a name="do-the-initial-backup-offline"></a>A kezdeti biztonsági mentés offline állapotba helyezése

A kezdeti biztonsági mentést automatikusan is futtathatja a hálózaton keresztül, vagy offline biztonsági mentést készíthet. A kezdeti biztonsági mentés offline kitöltése akkor hasznos, ha olyan nagy mennyiségű adattal rendelkezik, amely sok hálózati sávszélességet igényel az átvitelhez.

Kapcsolat nélküli átvitel:

1. A biztonsági mentési állapotot egy átmeneti helyre írja.
1. A AzureOfflineBackupDiskPrep eszközzel másolja az adatok az előkészítési helyről egy vagy több SATA-lemezre.

    Az eszköz létrehoz egy Azure importálási feladatot. További információ: Mi az [Az Azure import/export szolgáltatás](../import-export/storage-import-export-service.md).
1. A SATA-lemezek küldése egy Azure-adatközpontba.

    Az adatközpontban a lemezre vonatkozó Adatmásolás egy Azure Storage-fiókba történik. Azure Backup átmásolja az adatokat a Storage-fiókból a tárolóba, és a növekményes biztonsági mentések ütemezve lesznek.

További információ az offline előkészítésről: [Azure Data Box használata az offline biztonsági mentéshez](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Hálózati sávszélesség-szabályozás engedélyezése

A hálózati sávszélesség-szabályozás engedélyezésével szabályozhatja, hogy a MARS-ügynök hogyan használja a hálózati sávszélességet. A szabályozás akkor lehet hasznos, ha a munkaidőn belül biztonsági mentést kell végeznie, de szeretné szabályozni, hogy mekkora sávszélességet használ a biztonsági mentési és a helyreállítási tevékenység.

A hálózati sávszélesség-szabályozás Azure Backup a szolgáltatásminőség [(QoS) szolgáltatást](/windows-server/networking/technologies/qos/qos-policy-top) használja a helyi operációs rendszeren.

A biztonsági mentések hálózati szabályozása a Windows Server 2012-es és újabb verzióiban, valamint Windows 8 és újabb rendszereken érhető el. Az operációs rendszernek a legújabb szervizcsomagokat kell futtatnia.

A hálózati sávszélesség-szabályozás engedélyezése:

1. A MARS-ügynökben válassza a **Tulajdonságok módosítása** lehetőséget.
1. A **szabályozás** lapon válassza az **Internet sávszélesség-használat szabályozásának engedélyezése a biztonsági mentési műveletekhez** lehetőséget.

    ![Hálózati sávszélesség-szabályozás beállítása biztonsági mentési műveletekhez](./media/backup-configure-vault/throttling-dialog.png)
1. A munkaidőben és a munkaidőn belül engedélyezett sávszélesség megadására használható. A sávszélesség értéke 512 kbps, és akár 1 023 Mbps is lehet. Ez után válassza az **OK** gombot.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

1. A MARS-ügynökben válassza a **biztonsági mentés most** lehetőséget.

    ![Biztonsági mentés most a Windows Serverben](./media/backup-configure-vault/backup-now.png)

1. Ha a MARS-ügynök verziója 2.0.9169.0 vagy újabb, akkor egyéni megőrzési dátumot is beállíthat. A **biztonsági mentés eddig** szakaszban válassza ki a dátumot a naptárból.

   ![A naptár használata megőrzési dátum testreszabásához](./media/backup-configure-vault/mars-ondemand.png)

1. A **jóváhagyás** lapon tekintse át a beállításokat, majd válassza a **biztonsági mentés** lehetőséget.
1. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha bezárta a varázslót a biztonsági mentés befejeződése előtt, a varázsló továbbra is fut a háttérben.

A kezdeti biztonsági mentés befejeződése után a **feladatok befejezve** állapot jelenik meg a biztonsági mentési konzolon.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Igény szerinti biztonsági mentési szabályzatok megőrzési viselkedésének beállítása

> [!NOTE]
> Ez az információ csak a 2.0.9169.0 régebbi MARS-ügynök verzióira vonatkozik.
>

| Biztonsági mentés – ütemezett beállítás | Az adatmegőrzés időtartama
| -- | --
| Nap | **Alapértelmezett megőrzés**: megegyezik a napi biztonsági másolatok megőrzési napjaiban. <br/><br/> **Kivétel**: Ha a hosszú távú adatmegőrzésre beállított napi ütemezett biztonsági mentés (hét, hónap vagy év) meghiúsul, egy igény szerinti biztonsági mentést, amely a hiba után azonnal aktiválódik, hosszú távú adatmegőrzésre számít. Ellenkező esetben a következő ütemezett biztonsági mentés a hosszú távú adatmegőrzést veszi figyelembe.<br/><br/> **Példa**: a 8:00-as csütörtökön ütemezett biztonsági mentés sikertelen. Ezt a biztonsági mentést hetente, havonta vagy évenkénti megőrzés céljából kell figyelembe venni. Ezért az első igény szerinti biztonsági mentést a rendszer a következő ütemezett biztonsági mentés elindítását megelőzően, a 8:00 ÓRAKOR automatikusan címkézi, hetente, havonta vagy évenkénti megőrzéssel. Ez a biztonsági másolat helyettesíti a Csütörtök 8:00 AM biztonsági mentést.
| Hét | **Alapértelmezett megőrzés**: egy nap. A heti biztonsági mentési házirenddel rendelkező adatforrások igény szerinti biztonsági mentését a következő nap során törli a rendszer. A rendszer akkor is törli őket, ha az adatforrás legújabb biztonsági mentései vannak. <br/><br/> **Kivétel**: Ha a hosszú távú adatmegőrzésre beállított heti ütemezett biztonsági mentés (hét, hónap vagy év) meghiúsul, egy igény szerinti biztonsági mentés, amely a hiba után azonnal aktiválódik, hosszú távú adatmegőrzésre számít. Ellenkező esetben a következő ütemezett biztonsági mentés a hosszú távú adatmegőrzést veszi figyelembe. <br/><br/> **Példa**: a 8:00-as csütörtökön ütemezett biztonsági mentés sikertelen. A biztonsági mentést figyelembe kell venni a havi vagy az éves adatmegőrzés során. Így az első, a 8:00-as csütörtökön a következő ütemezett biztonsági mentés előtt kiváltott, igény szerinti biztonsági mentést a rendszer automatikusan felcímkézi a havi vagy az éves adatmegőrzési időszakra. Ez a biztonsági másolat helyettesíti a Csütörtök 8:00 AM biztonsági mentést.

További információt a [biztonsági mentési szabályzat létrehozása](#create-a-backup-policy)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [állíthatja vissza a fájlokat az Azure-ban](backup-azure-restore-windows-server.md).
* [Gyakori kérdések a fájlok és mappák biztonsági mentéséről](backup-azure-file-folder-backup-faq.md)
