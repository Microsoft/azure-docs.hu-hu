---
title: Exchange-kiszolgáló biztonsági Azure Backup Server
description: Útmutató Exchange-kiszolgáló biztonságiának biztonsági Azure Backup a Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: f3a7fae5a1f5ec933c015546ddf2bdb2898e3904
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515498"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Exchange-kiszolgáló azure-ba való biztonsági Azure Backup Server

Ez a cikk azt ismerteti, hogyan konfigurálhatja a Microsoft Azure Backup Servert (MABS) a Microsoft Exchange-kiszolgálók Azure-ba való biztonságimentére.  

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy a Azure Backup Server [telepítve és elő van készítve.](backup-azure-microsoft-azure-backup.md)

## <a name="mabs-protection-agent"></a>MABS védelmi ügynök

A MABS védelmi ügynök Exchange-kiszolgálón való telepítéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a tűzfalak megfelelően vannak konfigurálva. Lásd: [Configure firewall exceptions for the agent (Tűzfal-kivételek konfigurálása az ügynökhöz).](/system-center/dpm/configure-firewall-settings-for-dpm)
2. Telepítse az ügynököt az Exchange-kiszolgálóra a **Management > Agents > Install** in MABS felügyeleti konzol. A [részletes lépésekért lásd: A MABS védelmi](/system-center/dpm/deploy-dpm-protection-agent) ügynök telepítése.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Védelmi csoport létrehozása az Exchange-kiszolgálóhoz

1. A MABS felügyeleti konzol válassza a **Védelem** lehetőséget,  majd válassza az Új lehetőséget az eszköz menüszalagján az Új védelmi csoport **létrehozása varázsló megnyitásához.**
2. A varázsló **üdvözlőképernyőjén** válassza a Tovább **lehetőséget.**
3. A Védelmi **csoport típusának kiválasztása képernyőn** válassza a **Kiszolgálók,** majd a Tovább **lehetőséget.**
4. Válassza ki a védeni kívánt Exchange Server-adatbázist, majd kattintson a **Tovább gombra.**

   > [!NOTE]
   > Ha az Exchange 2013 védelmét védi, ellenőrizze az [Exchange 2013 előfeltételeit.](/system-center/dpm/back-up-exchange)
   >
   >

    A következő példában az Exchange 2010-adatbázis van kiválasztva.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Válassza ki az adatvédelmi módszert.

    Nevezze el a védelmi csoportot, majd válassza ki az alábbi lehetőségek egyikét:

   * Rövid távú védelmet szeretnék a Disk használatával.
   * Online védelmet szeretnék.
6. Kattintson a **Tovább** gombra.
7. Válassza a **Run Eseutil to check data integrity (Eseutil** futtatása az adatintegritás ellenőrzéséhez) lehetőséget, ha ellenőrizni szeretné az Exchange Server-adatbázisok integritását.

    Miután kiválasztotta ezt a beállítást, a rendszer futtatja a biztonsági mentési konzisztencia-ellenőrzést a MABS-ben, hogy elkerülje az **eseutil** parancs exchange-kiszolgálón való futtatásával létrehozott I/O-forgalmat.

   > [!NOTE]
   > Ha ezt a beállítást választja, a Ese.dll- és Eseutil.exe-fájlokat a MABS-kiszolgáló C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin könyvtárába kell másolnia. Ellenkező esetben a következő hiba aktiválódik:  
   > ![eseutil hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **Tovább** gombra.
9. Válassza ki az adatbázist **a Biztonsági másolat másolása beállításhoz,** majd válassza a Tovább **lehetőséget.**

   > [!NOTE]
   > Ha nem választja ki a "Teljes biztonsági mentés" lehetőséget az adatbázis legalább egy DAG-példányához, a naplók nem csonkolnak.
   >
   >
10. Konfigurálja a rövid távú biztonsági **mentés céljait,** majd válassza a **Tovább lehetőséget.**
11. Tekintse át a rendelkezésre álló lemezterületet, majd válassza a **Tovább lehetőséget.**
12. Válassza ki azt az időt, amikor a MABS-kiszolgáló létrehozza a kezdeti replikációt, majd válassza a Tovább **lehetőséget.**
13. Válassza ki a konzisztencia-ellenőrzés beállításait, majd válassza a **Tovább lehetőséget.**
14. Válassza ki azt az adatbázist, amelyről biztonsági szeretne biztonsági mentéset az Azure-ba, majd válassza a Tovább **lehetőséget.** Például:

    ![Online védelmi adatok megadása](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Adja meg az ütemezést **a Azure Backup,** majd válassza a **Tovább lehetőséget.** Például:

    ![Online biztonsági mentés ütemezésének megadása](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Megjegyzés Az online helyreállítási pontok expressz teljes helyreállítási pontokon alapulnak. Ezért az online helyreállítási pontot az expressz teljes helyreállítási ponthoz megadott időpont után kell ütemeznie.
    >
    >
16. Konfigurálja a megőrzési **szabályzatot a Azure Backup,** majd válassza a **Tovább lehetőséget.**
17. Válasszon egy online replikációs lehetőséget, majd válassza a **Tovább lehetőséget.**

    Nagy méretű adatbázis esetén a kezdeti biztonsági mentés a hálózaton keresztüli létrehozása hosszú időt is vegyen át. A probléma elkerülése érdekében létrehozhat egy offline biztonsági mentést.  

    ![Online adatmegőrzési szabályzat megadása](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Erősítse meg a beállításokat, majd válassza a **Csoport létrehozása lehetőséget.**
19. Válassza a **Bezárás** gombot.

## <a name="recover-the-exchange-database"></a>Az Exchange-adatbázis helyreállítása

1. Exchange-adatbázis helyreállításához válassza a **Helyreállítás lehetőséget** a MABS felügyeleti konzol.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válasszon ki egy online helyreállítási pontot a *helyreállítási* idő legördülő listából.
4. Válassza **a Helyreállítás** lehetőséget a Helyreállítási varázsló **indításhoz.**

Az online helyreállítási pontok esetében öt helyreállítási típus létezik:

* **Helyreállítás az exchange-kiszolgáló eredeti helyére:** Az adatok az eredeti Exchange-kiszolgálón lesznek helyreállítva.
* **Helyreállítás másik adatbázisba egy Exchange Serveren:** Az adatok egy másik Exchange-kiszolgálón található másik adatbázisba lesznek helyreállítva.
* **Helyreállítás helyreállítási adatbázisba:** Az adatok egy Exchange Helyreállítási adatbázisba (RDB) lesznek helyreállítva.
* **Másolás hálózati mappába:** Az adatok egy hálózati mappába lesznek helyreállítva.
* **Másolás szalagra:** Ha szalagtárat vagy önálló szalagos meghajtót csatlakoztat és konfigurál a MABS-on, a helyreállítási pont egy szabad szalagra lesz átmásolva.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Következő lépések

* [Azure Backup gyakori kérdések](backup-azure-backup-faq.yml)
