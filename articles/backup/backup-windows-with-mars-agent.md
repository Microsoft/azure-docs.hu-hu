---
title: Windows rendszerű gépek biztonsági telepítése MARS-ügynökkel
description: A windowsos Microsoft Azure a Microsoft Azure Recovery Services- (MARS-) ügynök használatával.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54628c15ffb51c7157132c9a91f41c16873df340
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519170"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Windows Server-fájlok és -mappák biztonsági mentése az Azure-ba

Ez a cikk azt ismerteti, hogyan [](backup-overview.md) lehet biztonsági Azure Backup a Windows rendszerű gépekről a Azure Backup Recovery Services- (MARS Microsoft Azure ügynök használatával. A MARS más néven Azure Backup ügynök.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
>
> * Az előfeltételek ellenőrzése
> * Biztonsági mentési szabályzat és ütemezés létrehozása.
> * Igény szerinti biztonsági mentés végrehajtása.

## <a name="before-you-start"></a>Előkészületek

* [Megtudhatja, Azure Backup MARS-ügynökkel](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)hogyan biztonságimenti a Windows rendszerű gépeket.
* Ismerje meg azt [a biztonsági mentési](backup-architecture.md#architecture-back-up-to-dpmmabs) architektúrát, amely a MARS-ügynököt egy másodlagos MABS- vagy Data Protection Manager futtatja.
* Tekintse [át, hogy mit támogat](backup-support-matrix-mars-agent.md) a marsi ügynök, és miről tud biztonságimentet.
* [Ellenőrizze az internet-hozzáférést](install-mars-agent.md#verify-internet-access) a gépeken, amelyekről biztonsági elérést kíván kapni.
* Ha a MARS-ügynök nincs telepítve, itt megtudhatja, hogyan [telepítheti.](install-mars-agent.md)

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési házirend határozza meg, hogy mikor készítsen pillanatképeket az adatokról helyreállítási pontok létrehozásához. Azt is meghatározza, hogy mennyi ideig kell tartani a helyreállítási pontokat. A MARS-ügynökkel konfigurálhatja a biztonsági mentési szabályzatot.

Azure Backup nem veszi figyelembe automatikusan a nyári időszámítást (DST). Ez az alapértelmezett beállítás némi eltérést okozhat a tényleges időpont és az ütemezett biztonsági mentési idő között.

Biztonsági mentési szabályzat létrehozása:

1. A MARS-ügynök letöltése és regisztrálása után nyissa meg az ügynökkonzolt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.  

1. A **Műveletek alatt** válassza a Biztonsági mentés **ütemezése lehetőséget.**

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)
1. A Biztonsági mentés ütemezése varázslóban válassza az **Első lépések Tovább**  >  **lehetőséget.**
1. A **Select Items to Back up (Elemek kiválasztása biztonságimentni kívánt elemek) alatt** válassza az Add Items **(Elemek hozzáadása) lehetőséget.**

    ![Elemek hozzáadása biztonságimentő elemekhez](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Az Elemek **kiválasztása mezőben** jelölje ki a biztonságimentni kívánt elemeket, majd kattintson az **OK gombra.**

    ![A biztonságimentni kívánt elemek kiválasztása](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Az Elemek **kiválasztása a biztonságimentő-hez lapon** válassza a Tovább **lehetőséget.**
1. A Biztonsági **mentés ütemezésének megadása lapon** adja meg, hogy mikor kell napi vagy heti biztonsági mentést készíteni. Ezután kattintson a **Tovább** gombra.

    * A helyreállítási pont a biztonsági másolat készítésekor jön létre.
    * A környezetben létrehozott helyreállítási pontok száma a biztonsági mentés ütemezésétől függ.
    * Naponta legfeljebb három biztonsági mentést ütemezhet. A következő példában két napi biztonsági mentést készítünk, egyet éjfélkor, egyet pedig 18:00-kor.

        ![Napi biztonsági mentési ütemezés beállítása](./media/backup-configure-vault/day-schedule.png)

    * Heti biztonsági mentéseket is futtathat. A következő példában a biztonsági mentések minden alternatív vasárnap és szerda 9:30-kor és 13:00-kor vannak készítve.

        ![Heti biztonsági mentési ütemezés beállítása](./media/backup-configure-vault/week-schedule.png)

1. Az **Adatmegőrzési szabályzat kiválasztása lapon** adja meg, hogyan tárolja az adatok előzménymásolatát. Ezután kattintson a **Tovább** gombra.

    * A megőrzési beállítások határozzák meg, hogy mely helyreállítási pontokat és mennyi ideig kell tárolni őket.
    * Napi megőrzési beállítás esetén azt jelzi, hogy a napi megőrzési időpontban a rendszer a legutóbbi helyreállítási pontot őrzi meg a megadott számú napig. Havi adatmegőrzési szabályzatot is megadhat, amely jelzi, hogy minden hónap 30. napján létrehozott helyreállítási pontot 12 hónapig kell tárolni.
    * A napi és heti helyreállítási pontok megőrzése általában egybeesik a biztonsági mentés ütemezésével. Így amikor az ütemezés biztonsági mentést vált ki, a rendszer a biztonsági mentés által létrehozott helyreállítási pontot a napi vagy heti megőrzési szabályzat által megadott időtartamra tárolja.
    * A következő példában:

        * A napi biztonsági másolatok hét napig 18:00-kor és 18:00-kor vannak megőrizve.
        * A szombat éjfélkor és 18:00-kor készült biztonsági másolatok négy hétig vannak megőrizve.
        * A hónap utolsó szombatján éjfélkor és 18:00-kor készült biztonsági másolatok 12 hónapig vannak megőrizve.
        * A március utolsó szombatján készült biztonsági másolatok 10 évig vannak megőrizve.

        ![Példa adatmegőrzési szabályzatra](./media/backup-configure-vault/retention-example.png)

1. A Kezdeti **biztonsági mentés típusának kiválasztása** lapon döntse el, hogy a kezdeti biztonsági mentést a hálózaton keresztül szeretné-e készíteni, vagy offline biztonsági mentést szeretne használni. A kezdeti biztonsági mentés hálózaton keresztüli készítéséhez válassza az **Automatikusan a hálózaton keresztül** Tovább  >  **lehetőséget.**

    További információ az offline biztonsági mentésről: Az Azure Data Box [használata offline biztonsági mentéshez.](offline-backup-azure-data-box.md)

    ![Kezdeti biztonsági mentés típusának kiválasztása](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. A Jóváhagyás **lapon tekintse** át az adatokat, majd válassza a Befejezés **lehetőséget.**

    ![A biztonsági mentés típusának ellenőrzése](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, válassza a Bezárás **lehetőséget.**

    ![A biztonsági mentés ütemezési folyamatának megtekintése](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Hozzon létre egy házirendet minden gépen, ahol az ügynök telepítve van.

### <a name="do-the-initial-backup-offline"></a>A kezdeti biztonsági mentés offline

A kezdeti biztonsági mentést automatikusan futtathatja a hálózaton keresztül, vagy offline is. A kezdeti biztonsági mentés offline átvitele akkor hasznos, ha nagy mennyiségű adat átviteléhez nagy hálózati sávszélességre van szükség.

Offline átvitel:

1. Írja a biztonsági mentési adatokat egy átmeneti helyre.
1. Az AzureOfflineBackupDiskPrep eszközzel másolhatja az adatokat az előkészítési helyről egy vagy több SATA-lemezre.

    Az eszköz létrehoz egy Azure Import-feladatot. További információ: [Mi az az Azure Import/Export szolgáltatás?](../import-export/storage-import-export-service.md)
1. A SATA-lemezek küldése egy Azure-adatközpontba.

    Az adatközpontban a rendszer a lemezadatokat egy Azure-tárfiókba másolja. Azure Backup átmásolja az adatokat a tárfiókból a tárolóba, és a növekményes biztonsági mentések ütemezve vannak.

További információ az offline adatokat átvéve: [Use Azure Data Box for offline backup](offline-backup-azure-data-box.md)..

### <a name="enable-network-throttling"></a>Hálózati szabályozás engedélyezése

A hálózati szabályozás engedélyezésével szabályozhatja, hogy a MARS-ügynök hogyan használja a hálózati sávszélességet. A szabályozás akkor hasznos, ha munkaidőben kell biztonsági másolatot készítenie az adatokról, de szabályozni szeretné, hogy a biztonsági mentési és visszaállítási tevékenység mekkora sávszélességet használ fel.

A hálózati Azure Backup [szolgáltatásminőséget (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) használ a helyi operációs rendszeren.

A biztonsági mentések hálózati szabályozása a Windows Server 2012 és újabb, illetve a Windows 8 és újabb rendszereken érhető el. Az operációs rendszereknek a legújabb szervizcsomagokat kell futniuk.

A hálózati szabályozás engedélyezése:

1. A MARS-ügynökben válassza a **Tulajdonságok módosítása lehetőséget.**
1. A Szabályozás **lapon** jelölje be az Internetes sávszélesség-használat szabályozásának engedélyezése a **biztonsági mentési műveletekhez jelölőnégyzetet.**

    ![Hálózati szabályozás beállítása biztonsági mentési műveletekhez](./media/backup-configure-vault/throttling-dialog.png)
1. Adja meg a megengedett sávszélességet a munkaidőben és a munkaidőn kívüli órákban. A sávszélesség-értékek 512 Kbps-ről kezdődnek, és 1023 Mb/s-ra esnek. Ez után válassza az **OK** gombot.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

1. A MARS-ügynökben válassza a **Biztonságimentás most lehetőséget.**

    ![Biztonsági frissítés a Windows Serveren](./media/backup-configure-vault/backup-now.png)

1. Ha a MARS-ügynök verziója 2.0.9169.0-s vagy újabb, akkor egyéni megőrzési dátumot állíthat be. A **Biztonsági másolat megőrzése eddig** szakaszban válasszon ki egy dátumot a naptárból.

   ![Megőrzési dátum testreszabása a naptár használatával](./media/backup-configure-vault/mars-ondemand.png)

1. A Jóváhagyás **lapon tekintse** át a beállításokat, majd válassza a **Biztonságimentés lehetőséget.**
1. A varázslót a **Bezárás** gombra választva zárja be. Ha a biztonsági mentés befejezése előtt bezárja a varázslót, a varázsló továbbra is a háttérben fut.

A kezdeti biztonsági mentés befejezése után a Feladat **befejezve** állapot jelenik meg a Biztonsági mentés konzolon.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Igény szerinti biztonsági mentési szabályzat megőrzési viselkedésének beállítása

> [!NOTE]
> Ezek az információk csak a 2.0.9169.0-s verziónál régebbi MARS-ügynökverziókra vonatkoznak.
>

| Biztonsági mentés ütemezése beállítás | Az adatmegőrzés időtartama
| -- | --
| Nap | **Alapértelmezett megőrzés:** A "napi biztonsági mentések megőrzési napjainak megtartása" értéknek felel meg. <br/><br/> **Kivétel:** Ha egy hosszú távú megőrzésre (hetekre, hónapokra vagy évekre) beállított napi rendszerességgel ütemezett biztonsági mentés meghiúsul, akkor a rendszer a hiba utáni, igény szerinti biztonsági mentést veszi figyelembe a hosszú távú megőrzéshez. Ellenkező esetben a rendszer a következő ütemezett biztonsági mentést hosszú távú megőrzésre tekint.<br/><br/> **Példaforgatókönyv:** Az ütemezett biztonsági mentés csütörtök 8:00-kor meghiúsult. Ezt a biztonsági mentést heti, havi vagy éves megőrzésre kellett tekinteni. Így az első, igény szerinti biztonsági mentés a következő ütemezett biztonsági mentés előtt aktiválódik péntek 8:00-kor, automatikusan meg lesz jelölve heti, havi vagy éves megőrzésre. Ez a biztonsági másolat helyettesíti a csütörtök 8:00-kori biztonsági mentést.
| Hét | **Alapértelmezett megőrzés:** Egy nap. Az olyan adatforrások igény szerinti biztonsági mentései, amelyek heti biztonsági mentési szabályzatot tartalmaznak, a következő napon törlődnek. Akkor is törlődnek, ha az adatforrás legújabb biztonsági másolatai. <br/><br/> **Kivétel:** Ha egy hosszú távú megőrzésre (hetekre, hónapokra vagy évekre) beállított heti rendszerességű biztonsági mentés sikertelen, akkor a rendszer a hiba utáni, igény szerinti biztonsági mentést veszi figyelembe a hosszú távú megőrzésre. Ellenkező esetben a rendszer a következő ütemezett biztonsági mentést hosszú távú megőrzésre tekint. <br/><br/> **Példaforgatókönyv:** Az ütemezett biztonsági mentés csütörtök reggel 8:00-kor meghiúsult. Ezt a biztonsági mentést havi vagy éves megőrzésre kellett tekinteni. Így a rendszer a csütörtök 8:00-kor a következő ütemezett biztonsági mentés előtt aktivált első igény szerinti biztonsági mentést automatikusan megcímkézi havi vagy éves megőrzésre. Ez a biztonsági másolat helyettesíti a csütörtök 8:00-kori biztonsági mentést.

További információ: [Biztonsági mentési szabályzat létrehozása.](#create-a-backup-policy)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [állíthatók vissza fájlok az Azure-ban.](backup-azure-restore-windows-server.md)
* Gyakori kérdések a fájlok és mappák biztonsági [mentése ről](backup-azure-file-folder-backup-faq.yml)
