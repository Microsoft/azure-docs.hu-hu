---
title: Exchange-kiszolgáló biztonsági System Center DPM-en keresztül
description: Útmutató Exchange-kiszolgáló biztonságiának biztonsági Azure Backup a System Center 2012 R2 DPM használatával
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a89c37b8447b318c44faf0d4e0b1921d305e7f59
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519391"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Exchange-kiszolgáló biztonsági mentése az Azure Backupba a System Center 2012 R2 DPM-mel

Ez a cikk azt ismerteti, hogyan konfigurálható egy System Center 2012 R2 Data Protection Manager- (DPM-) kiszolgáló a Microsoft Exchange-kiszolgálók biztonsági Azure Backup.  

## <a name="updates"></a>Frissítések

Ahhoz, hogy sikeresen regisztrálja a DPM-kiszolgálót az Azure Backup-ban, telepítenie kell az System Center 2012 R2 DPM legújabb frissítését és az Azure Backup Agent legújabb verzióját. Szerezze be a legújabb összesítő frissítést a [Microsoft-katalógusból.](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)

> [!NOTE]
> A cikkben látható példákhoz telepítve van a Azure Backup Agent 2.0.8719.0-s verziója, a 6. összesítő frissítés pedig a 2012 R2 DPM-System Center telepítve.
>
>

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg [](backup-azure-dpm-introduction.md#prerequisites-and-limitations) arról, hogy a számítási feladatok Microsoft Azure Backup védelméhez szükséges összes előfeltétel teljesült. Ezek az előfeltételek a következők:

* Létrejött egy Backup-tároló az Azure-helyen.
* Az ügynök és a tároló hitelesítő adatai letöltődtek a DPM-kiszolgálóra.
* Az ügynök a DPM-kiszolgálóra van telepítve.
* A tároló hitelesítő adataival regisztrálták a DPM-kiszolgálót.
* Ha az Exchange 2016 védelmét védi, frissítsen a DPM 2012 R2 UR9-es vagy újabb verzióra.

## <a name="dpm-protection-agent"></a>DPM védelmi ügynök

A DPM védelmi ügynök Exchange-kiszolgálóra való telepítéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a tűzfalak megfelelően vannak konfigurálva. Lásd: [Configure firewall exceptions for the agent (Tűzfal-kivételek konfigurálása az ügynökhöz).](/system-center/dpm/configure-firewall-settings-for-dpm)
2. Telepítse az ügynököt az Exchange-kiszolgálóra a Felügyelet > ügynök > **Telepítés** a DPM Felügyeleti konzol. A [részletes lépésekért lásd: Install the DPM protection agent (A DPM](/system-center/dpm/deploy-dpm-protection-agent) védelmi ügynök telepítése).

## <a name="create-a-protection-group-for-the-exchange-server"></a>Védelmi csoport létrehozása az Exchange-kiszolgálóhoz

1. A DPM Felügyeleti konzol válassza a **Védelem,** majd  az Új lehetőséget az eszköz menüszalagján az Új védelmi csoport **létrehozása varázsló megnyitásához.**
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

    Nevezze el a védelmi csoportot, majd válassza ki az alábbi két lehetőséget:

   * Rövid távú védelmet szeretnék a Disk használatával.
   * Online védelmet szeretnék.
6. Kattintson a **Tovább** gombra.
7. Válassza az **Eseutil futtatása az** adatintegritás ellenőrzéséhez lehetőséget, ha ellenőrizni szeretné az Exchange Server-adatbázisok integritását.

    Miután kiválasztotta ezt a beállítást, a biztonsági mentés konzisztencia-ellenőrzése futni fog a DPM-kiszolgálón az **eseutil** parancs Exchange-kiszolgálón való futtatásával létrehozott I/O-forgalom elkerülése érdekében.

   > [!NOTE]
   > Ha ezt a beállítást választja, a Ese.dll- és Eseutil.exe-fájlokat a DPM-kiszolgáló C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin könyvtárába kell másolnia. Ellenkező esetben a következő hiba aktiválódik:  
   > ![eseutil hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **Tovább** gombra.
9. Válassza ki az adatbázist **a Biztonsági másolat másolása beállításhoz,** majd kattintson a **Tovább gombra.**

   > [!NOTE]
   > Ha nem választja ki a "Teljes biztonsági mentés" lehetőséget az adatbázis legalább egy DAG-példányához, a naplók nem lesznek csonkolva.
   >
   >
10. Konfigurálja a rövid távú **biztonsági mentés céljait,** majd válassza a **Tovább lehetőséget.**
11. Tekintse át a rendelkezésre álló lemezterületet, majd válassza a **Tovább lehetőséget.**
12. Válassza ki azt az időt, amikor a DPM-kiszolgáló létrehozza a kezdeti replikációt, majd válassza a Tovább **lehetőséget.**
13. Válassza ki a konzisztencia-ellenőrzés beállításait, majd válassza a **Tovább lehetőséget.**
14. Válassza ki azt az adatbázist, amelyről biztonsági mentéset kíván az Azure-ba, majd válassza a Tovább **lehetőséget.** Például:

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

1. Exchange-adatbázis helyreállításához válassza a **helyreállítás lehetőséget** a DPM Felügyeleti konzol.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válasszon ki egy online helyreállítási pontot a *helyreállítási* idő legördülő listából.
4. Válassza **a Helyreállítás** lehetőséget a Helyreállítási varázsló **indításhoz.**

Az online helyreállítási pontok esetében öt helyreállítási típus létezik:

* **Helyreállítás az exchange-kiszolgáló eredeti helyére:** Az adatok az eredeti Exchange-kiszolgálón lesznek helyreállítva.
* **Helyreállítás egy másik adatbázisba egy Exchange Serveren:** Az adatok egy másik Exchange-kiszolgálón található másik adatbázisba lesznek helyreállítva.
* **Helyreállítás helyreállítási adatbázisba:** Az adatok egy Exchange Recovery-adatbázisba (RDB) lesznek helyreállítva.
* **Másolás hálózati mappába:** Az adatok egy hálózati mappába lesznek helyreállítva.
* **Másolás szalagra:** Ha szalagtár vagy önálló szalagos meghajtó van csatlakoztatva és konfigurálva a DPM-kiszolgálón, a helyreállítási pont egy szabad szalagra lesz átmásolva.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Következő lépések

* [Azure Backup gyakori kérdések](backup-azure-backup-faq.yml)
