---
title: Adatok helyreállítása egy Azure Backup Server
description: Helyreállítja a védett adatokat egy Recovery Services-tárolóba az Azure Backup Server tárolóban regisztrált összes tárolóból.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 780c88175397fb06e704e57062ae5c6d3b93d8b8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519588"
---
# <a name="recover-data-from-azure-backup-server"></a>Adatok helyreállítása az Azure Backup Serverről

A biztonsági Azure Backup Server helyreállítási tárba biztonságimentett adatok helyreállítására használhatja. Ennek folyamata integrálva van az Azure Backup Server felügyeleti konzolba, és hasonló a többi összetevő helyreállítási Azure Backup munkafolyamatához.

> [!NOTE]
> Ez a cikk a [2012 R2 System Center Data Protection Manager UR7](https://support.microsoft.com/kb/3065246)vagy újabb verziójára vonatkozik, a legújabb Azure Backup [ügynökkel kombinálva.](https://aka.ms/azurebackup_agent)
>
>

Adatok helyreállítása egy Azure Backup Server:

1. A felügyeleti **konzol** Helyreállítás Azure Backup Server válassza a **"Külső DPM hozzáadása"** lehetőséget (a képernyő bal felső részén).

    ![Külső DPM hozzáadása](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Töltse  le az új tároló hitelesítő adatait az **Azure Backup Server-hez** társított tárolóból, ahol az adatok helyreállítása folyamatban van, válassza ki a  Azure Backup Server-t a Recovery Services-tárolóval regisztrált Azure Backup-kiszolgálók listájából, és adja meg a kiszolgálóhoz társított titkosítási jelszót, amelynek az adatai helyreállítása folyamatban van.

    ![Külső DPM hitelesítő adatok](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Csak Azure Backup regisztrációs tárolóhoz társított kiszolgálók állíthatók helyre egymás adatai.
   >
   >

    A Külső Azure Backup Server hozzáadása után a Helyreállítás lapon tallózhatja a külső kiszolgáló és a Azure Backup Server **adatait.**
3. Böngésszen a külső kiszolgáló által védett éles kiszolgálók Azure Backup Server és válassza ki a megfelelő adatforrást.

    ![Külső DPM-kiszolgáló tallózása](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Válassza **ki a hónapot és** az évet a  Helyreállítási pontok legördülő menüből, válassza ki a helyreállítási pont létrehozási dátumához szükséges helyreállítási dátumot, majd válassza a Helyreállítási idő **lehetőséget.** 

    Az alsó panelen megjelenik a fájlok és mappák listája, amelyek bármely helyen megkereshetők és helyreállíthatóak.

    ![Külső DPM-kiszolgáló helyreállítási pontjai](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kattintson a jobb gombbal a megfelelő elemre, és válassza a **Helyreállítás lehetőséget.**

    ![Külső DPM-helyreállítás](./media/backup-azure-alternate-dpm-server/recover.png)
6. Tekintse át a **Kiválasztás helyreállítása jelölőt.** Ellenőrizze a biztonsági másolat helyreállításának adatait és idejét, valamint azt a forrást, amelyből a biztonsági másolat létre lett hozva. Ha a kijelölés helytelen, válassza a Mégse **lehetőséget** a helyreállítási lapra való vissza lépéshez a megfelelő helyreállítási pont kiválasztásához. Ha a választás helyes, válassza a **Tovább lehetőséget.**

    ![Külső DPM-helyreállítás összegzése](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Válassza **a Helyreállítás másik helyre lehetőséget.** **Tallózással** keresse meg a helyreállításhoz megfelelő helyet.

    ![Külső DPM-helyreállítás alternatív helye](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Válassza a másolás létrehozásához, **a** kihagyáshoz vagy a **Felülíráshoz kapcsolódó beállítást.**

   * **Másolat létrehozása** – másolatot készít a fájlról, ha a névütközés történik.
   * **Kihagyás** – névütközés esetén nem állítható helyre a fájl, így az eredeti fájl nem marad meg.
   * **Felülírás** – névütközés esetén felülírja a fájl meglévő példányát.

     Válassza ki a megfelelő lehetőséget a **Biztonság visszaállítása beállításhoz.** Alkalmazhatja annak a célszámítógépnek a biztonsági beállításait, ahol az adatok helyreállítása folyamatban van, vagy alkalmazhatja a termékre a helyreállítási pont létrehozásakor érvényes biztonsági beállításokat.

     A helyreállítás **sikeres befejeződése** után határozza meg, hogy a rendszer küld-e értesítést.

     ![Külső DPM helyreállítási értesítések](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Az **Összefoglalás** képernyőn az eddig kiválasztott beállításokat sorolja fel. Ha a **Helyreállítás** lehetőséget választja, az adatok a megfelelő helyszíni helyre állíthatók vissza.

    ![A külső DPM helyreállítási beállításainak összefoglalása](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > A helyreállítási feladat a monitorozási folyamat **Figyelés** lapján Azure Backup Server.
   >
   >

    ![Helyreállítás monitorozása](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. A külső **DPM-kiszolgáló**  nézetének eltávolításához válassza a Külső DPM törlése lehetőséget a DPM-kiszolgáló Helyreállítás lapján.

    ![Külső DPM ürít](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Hibaüzenetek hibaelhárítása

| Nem. | Hibaüzenet | Hibaelhárítási lépések |
|:---:|:--- |:--- |
| 1. |Ez a kiszolgáló nincs regisztrálva a tároló hitelesítő adatai által megadott tárolóban. |**Ok:** Ez a hiba akkor jelenik meg, ha a kiválasztott tároló hitelesítőadat-fájlja nem tartozik a helyreállítási kísérlet Azure Backup Server társított helyreállítási tárhoz. <br> **Megoldás:** Töltse le a tároló hitelesítőadat-fájlját abból a Recovery Services-tárolóból, Azure Backup Server a tároló regisztrálva van. |
| 2. |A helyreállítható adatok nem érhetők el, vagy a kiválasztott kiszolgáló nem DPM-kiszolgáló. |**Ok:** Nincsenek más Azure Backup a Helyreállítási tárhoz regisztrált kiszolgálók, vagy a kiszolgálók még nem töltötték fel a metaadatokat, vagy a kiválasztott kiszolgáló nem Azure Backup Server (Windows Server vagy Windows-ügyfél használatával). <br> **Megoldás:** Ha más kiszolgálók is regisztrálva Azure Backup a Recovery Services-tárolóban, győződjön meg arról, hogy a Azure Backup ügynök telepítve van. <br>Ha más kiszolgálók is Azure Backup vannak regisztrálva a Helyreállítási tárban, várjon egy napot a telepítés után, és indítsa el a helyreállítási folyamatot. Az éjszakai feladat feltölti az összes védett biztonsági mentés metaadatait a felhőbe. Az adatok helyreállíthatóak lesznek. |
| 3. |Ehhez a tárolóhoz nincs más DPM-kiszolgáló regisztrálva. |**Ok:** Nincsenek más Azure Backup, amelyek regisztrálva vannak arra a tárolóra, amelyről a helyreállítást megkísérlik.<br>**Megoldás:** Ha más kiszolgálók is regisztrálva Azure Backup a Recovery Services-tárolóban, győződjön meg arról, hogy a Azure Backup ügynök telepítve van.<br>Ha más kiszolgálók is Azure Backup vannak regisztrálva a Helyreállítási tárban, várjon egy napot a telepítés után, és indítsa el a helyreállítási folyamatot. Az éjszakai feladat feltölti az összes védett biztonsági mentés metaadatait a felhőbe. Az adatok helyreállíthatóak lesznek. |
| 4. |A megadott titkosítási jelszó nem egyezik a következő kiszolgálóhoz társított jelszóval: **\<server name>** |**Ok:** A helyreállított Azure Backup Server adatainak titkosítása során használt titkosítási jelszó nem egyezik a megadott titkosítási jelszóval. Az ügynök nem tudja visszafejteni az adatokat, ezért a helyreállítás sikertelen lesz.<br>**Megoldás:** Pontosan ugyanazt a titkosítási jelszót adja meg, amely a Azure Backup Server, amelynek az adatai helyreállítása folyamatban van. |

## <a name="next-steps"></a>Következő lépések

További gyakori kérdéseket is áttekinthet:

* [Gyakori kérdések az Azure-beli](backup-azure-vm-backup-faq.yml) virtuális gépek biztonsági másolatairól
* [Gyakori kérdések](backup-azure-file-folder-backup-faq.yml) az Azure Backup-ügynökről
