---
title: Adatok helyreállítása Azure Backup Server
description: Állítsa helyre a védelemmel ellátott adatokat Recovery Services-tárolóba az adott tárban regisztrált bármely Azure Backup Server.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: ed8c937f97ec7a74662a8b46a354b0a6db39a2b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98806023"
---
# <a name="recover-data-from-azure-backup-server"></a>Adatok helyreállítása az Azure Backup Serverről

A Azure Backup Server segítségével visszaállíthatja azokat az adatokat, amelyekről biztonsági másolatot készített egy Recovery Services-tárolóba. Ennek folyamata integrálva van a Azure Backup Server felügyeleti konzolba, és hasonló a más Azure Backup-összetevők helyreállítási munkafolyamataihoz.

> [!NOTE]
> Ez a cikk a [System Center Data Protection Manager 2012 R2 használna 7 vagy újabb verziójára](https://support.microsoft.com/kb/3065246)alkalmazható, a [legújabb Azure Backup ügynökkel](https://aka.ms/azurebackup_agent)együtt.
>
>

Adatok helyreállítása Azure Backup Serverról:

1. A Azure Backup Server felügyeleti konzol **helyreállítás** lapján válassza a **külső DPM hozzáadása** lehetőséget (a képernyő bal felső részén).

    ![Külső DPM hozzáadása](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Töltse le az új tár **hitelesítő adatait** a tárolóhoz tartozó **Azure Backup Server** , ahol az adatok helyreállítása történik, válassza ki a Azure Backup Server az Recovery Services-tárolóban regisztrált Azure Backup kiszolgálók listájából, és adja meg az ahhoz a kiszolgálóhoz társított **titkosítási jelszót** , amelynek az adatait helyre kívánja állítani.

    ![Külső DPM hitelesítő adatai](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Csak Azure Backup az ugyanahhoz a regisztrációs tárolóhoz társított kiszolgálók tudják helyreállítani egymás adataikat.
   >
   >

    A külső Azure Backup Server sikeres hozzáadása után a külső kiszolgáló és a helyi Azure Backup Server adatainak tallózásával tallózhat a **helyreállítás** lapról.
3. Tallózással keresse meg a külső Azure Backup Server által védett üzemi kiszolgálók elérhető listáját, és válassza ki a megfelelő adatforrást.

    ![Külső DPM-kiszolgáló tallózása](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Válassza ki **a hónapot és az évet** a **helyreállítási pontok** legördülő menüből, válassza ki a helyreállítási pont létrehozásakor szükséges **helyreállítási dátumot** , és válassza ki a **helyreállítási időpontot**.

    Az alsó ablaktáblán megjelenik a fájlok és mappák listája, amely bármely helyre megtekinthető és helyreállítható.

    ![Külső DPM-kiszolgáló helyreállítási pontjai](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kattintson a jobb gombbal a megfelelő elemre, és válassza a **helyreállítás** lehetőséget.

    ![Külső DPM-helyreállítás](./media/backup-azure-alternate-dpm-server/recover.png)
6. Tekintse át a **helyreállítás kijelölését**. Ellenőrizze a helyreállított biztonsági másolat adatait és időpontját, valamint azt a forrást, amelyről a biztonsági másolatot létrehozta. Ha a kijelölés helytelen, a **Mégse** gombra kattintva lépjen vissza a helyreállítás lapra a megfelelő helyreállítási pont kiválasztásához. Ha a kijelölés helyes, válassza a **tovább** lehetőséget.

    ![Külső DPM-helyreállítás összegzése](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Válassza **a helyreállítás másik helyre** lehetőséget. **Tallózással keresse** meg a megfelelő helyet a helyreállításhoz.

    ![Külső DPM-helyreállítás alternatív helye](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Válassza a másolás, **kihagyás** vagy **felülírás** **létrehozásához** kapcsolódó lehetőséget.

   * **Másolat létrehozása** – létrehoz egy másolatot a fájlról, ha a név ütközést okoz.
   * **Kihagyás** – ha a név ütközik, nem állítja helyre a fájlt, amely elhagyja az eredeti fájlt.
   * Felülírás – ha a név ütközik, **felülírja** a fájl meglévő példányát.

     Válassza ki a megfelelő beállítást a **Biztonság visszaállításához**. Alkalmazhatja azon célszámítógép biztonsági beállításait, amelyen az adat helyreáll, vagy a helyreállítási pont létrehozásakor a termékre vonatkozó biztonsági beállítások.

     Annak megállapítása, hogy az **értesítés** küldése sikeres volt-e, amint a helyreállítás sikeresen befejeződött.

     ![Külső DPM helyreállítási értesítései](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Az **összegző** képernyő felsorolja az eddig kiválasztott beállításokat. Miután kiválasztotta a **helyreállítás** lehetőséget, az adatokat a rendszer a megfelelő helyszíni helyre állítja vissza.

    ![Külső DPM helyreállítási beállítások összegzése](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > A helyreállítási feladatot a Azure Backup Server **figyelés** lapján lehet figyelni.
   >
   >

    ![Helyreállítás figyelése](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. A DPM-kiszolgáló **helyreállítás** lapján a külső **DPM törlése** lehetőség kiválasztásával távolíthatja el a külső DPM-kiszolgáló nézetét.

    ![Külső DPM törlése](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Hibaüzenetek hibaelhárítása

| Nem. | Hibaüzenet | Hibaelhárítási lépések |
|:---:|:--- |:--- |
| 1. |Ez a kiszolgáló nincs regisztrálva a tároló hitelesítő adatai által megadott tárolóban. |**OK:** Ez a hiba akkor jelenik meg, ha a tároló hitelesítőadat-fájlja nem a helyreállítási kísérlethez Azure Backup Server társított Recovery Services tárolóhoz tartozik. <br> **Megoldás:** Töltse le a tároló hitelesítőadat-fájlját abban a Recovery Services-tárból, amelyhez a Azure Backup Server regisztrálva van. |
| 2. |Vagy a helyreállítható adatértékek nem érhetők el, vagy a kiválasztott kiszolgáló nem DPM-kiszolgáló. |**OK:** Nincs más Azure Backup-kiszolgáló regisztrálva az Recovery Services-tárolóban, vagy a kiszolgálók még nem töltötték fel a metaadatokat, vagy a kiválasztott kiszolgáló nem Azure Backup Server (Windows Server vagy Windows-ügyfél használatával). <br> **Megoldás:** Ha más Azure Backup-kiszolgálók is regisztrálva vannak a Recovery Services-tárolóban, győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van. <br>Ha más Azure Backup-kiszolgálók is regisztrálva vannak a Recovery Services-tárolóban, várjon egy nappal a telepítés után, hogy elindítsa a helyreállítási folyamatot. Az éjszakai feladatok feltöltik az összes védett biztonsági mentés metaadatait a felhőbe. Az adathelyreállításhoz elérhetők lesznek. |
| 3. |Nincs másik DPM-kiszolgáló regisztrálva ehhez a tárolóhoz. |**OK:** Nincsenek olyan Azure Backup kiszolgálók, amelyek regisztrálva vannak azon a tárolón, amelyről a helyreállítási kísérlet történt.<br>**Megoldás:** Ha más Azure Backup-kiszolgálók is regisztrálva vannak a Recovery Services-tárolóban, győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van.<br>Ha más Azure Backup-kiszolgálók is regisztrálva vannak a Recovery Services-tárolóban, várjon egy nappal a telepítés után, hogy elindítsa a helyreállítási folyamatot. Az éjszakai feladattal feltöltheti a felhőbe irányuló összes védett biztonsági mentés metaadatait. Az adathelyreállításhoz elérhetők lesznek. |
| 4. |A megadott titkosítási jelszó nem egyezik a következő kiszolgálóhoz tartozó jelszóval: **\<server name>** |**OK:** Az Azure Backup Server által helyreállított adatokból az adatok titkosítása során használt titkosítási jelszó nem egyezik meg a megadott titkosítási jelszóval. Az ügynök nem tudja visszafejteni az adattitkosítást, így a helyreállítás sikertelen lesz.<br>**Megoldás:** Adja meg ugyanazt a titkosítási jelszót, amely ahhoz a Azure Backup Server van társítva, amelynek az adatai helyreállnak. |

## <a name="next-steps"></a>Következő lépések

További gyakori kérdéseket is áttekinthet:

* Az Azure-beli virtuális gépek biztonsági mentésével kapcsolatos [Gyakori kérdések](backup-azure-vm-backup-faq.yml)
* [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) az Azure Backup-ügynökről
