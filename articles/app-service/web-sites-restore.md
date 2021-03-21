---
title: Alkalmazás visszaállítása biztonsági másolatból
description: Ismerje meg, hogyan állíthatja vissza az alkalmazást biztonsági másolatból. Bizonyos csatolt adatbázisok az alkalmazással együtt is visszaállíthatók egy művelettel.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: c1b0ce62905424032c2100a1a032fa43ba97578f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999399"
---
# <a name="restore-an-app-in-azure"></a>Alkalmazás visszaállítása az Azure-ban
Ebből a cikkből megtudhatja, hogyan állíthatja vissza az alkalmazást olyan [Azure app Serviceban](../app-service/overview.md) , amelyekről korábban készített biztonsági mentést (lásd: [az alkalmazás biztonsági mentése az Azure-ban](manage-backup.md)). Az alkalmazást visszaállíthatja a csatolt adatbázisaival egy korábbi állapotba, vagy létrehozhat egy új alkalmazást az eredeti alkalmazás biztonsági másolatai alapján. A Azure App Service a következő adatbázisokat támogatja a biztonsági mentéshez és visszaállításhoz:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [beépített mySQL](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

A biztonsági másolatokból történő visszaállítás a **standard** és a **prémium** szinten futó alkalmazások számára érhető el. Az alkalmazás skálázásával kapcsolatos további információkért lásd: alkalmazás vertikális felskálázása [Az Azure-ban](manage-scale-up.md). A **prémium** szint nagyobb számú napi biztonsági mentést tesz lehetővé, mint a **standard** szint.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Alkalmazás visszaállítása meglévő biztonsági másolatból
1. Az alkalmazás **Beállítások** lapján a Azure Portalban kattintson a **biztonsági** mentések elemre a **biztonsági mentések** lap megjelenítéséhez. Ezután kattintson a **visszaállítás** gombra.
   
    ![Válassza a Visszaállítás most lehetőséget][ChooseRestoreNow]
2. A **visszaállítás** lapon először válassza ki a biztonsági mentés forrását.
   
    ![A biztonsági mentési forrás kiválasztásának helyét bemutató képernyőkép.](./media/web-sites-restore/021ChooseSource1.png)
   
    Az **alkalmazás biztonsági mentése** lehetőség megjeleníti az aktuális alkalmazás összes meglévő biztonsági mentését, és egyszerűen kiválaszthat egyet.
    A **Storage (tárolás** ) beállítással bármely meglévő Azure Storage-fiókból és-tárolóból kiválaszthatja a Backup zip-fájlokat az előfizetésében.
    Ha egy másik alkalmazás biztonsági másolatát kísérli meg visszaállítani, használja a **Storage (tárolás** ) beállítást.
3. Ezután adja meg az alkalmazás visszaállításának célját a **visszaállítási célhelyen**.
   
    ![Képernyőfelvétel: Itt adhatja meg, hogy hol adja meg az alkalmazás visszaállításának célhelyét.](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Ha a **felülírás** lehetőséget választja, a rendszer törli az aktuális alkalmazás összes meglévő adatát, és felülírja azt. Mielőtt rákattintott **az OK** gombra, győződjön meg arról, hogy pontosan mit szeretne tenni.
   > 
   > 
   
   > [!WARNING]
   > Ha a App Service a visszaállítás során az adatbázisba írja az adatírást, az olyan tüneteket eredményezhet, mint például az elsődleges kulcs és az adatvesztés megsértése. Azt javasoljuk, hogy először állítsa le a App Servicet az adatbázis visszaállítása előtt.
   > 
   > 
   
    A **meglévő alkalmazás** lehetőség kiválasztásával visszaállíthatja az alkalmazás biztonsági másolatát egy másik alkalmazásba ugyanabban az erőforráscsoporthoz. Ennek a lehetőségnek a használata előtt létre kell hoznia egy másik alkalmazást az erőforráscsoporthoz, amely az adatbázis konfigurációját tükrözte az alkalmazás biztonsági másolatában meghatározottak szerint. Létrehozhat egy **új** alkalmazást is, amellyel visszaállíthatja a tartalmat a szolgáltatásba.

4. Kattintson az **OK** gombra.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Biztonsági másolat letöltése vagy törlése egy Storage-fiókból
1. A Azure Portal fő **Tallózás** lapján válassza a **Storage-fiókok** lehetőséget. Megjelenik a meglévő Storage-fiókok listája.
2. Válassza ki azt a Storage-fiókot, amely a letölteni vagy törölni kívánt biztonsági másolatot tartalmazza. Megjelenik a Storage-fiók lapja.
3. A Storage-fiók lapon válassza ki a kívánt tárolót
   
    ![Tárolók megtekintése][ViewContainers]
4. Válassza ki a letölteni vagy törölni kívánt biztonságimásolat-fájlt.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Kattintson a **Letöltés** vagy a **Törlés** lehetőségre attól függően, hogy mit szeretne tenni.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Visszaállítási művelet figyelése
Ha szeretné megtekinteni az alkalmazás-visszaállítási művelet sikerességét vagy sikertelenségét, lépjen a Azure Portal **tevékenység napló** lapjára.  
 

Görgessen le a kívánt visszaállítási művelet megkereséséhez, majd kattintson rá a kijelöléshez.

A Részletek lapon a visszaállítási művelettel kapcsolatos rendelkezésre álló információk láthatók.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

A parancsfájlok segítségével automatizálhatja a biztonsági mentési felügyeletet az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/)használatával.

Példákat a következő témakörben talál:

- [Azure CLI-minták](samples-cli.md)
- [Azure PowerShell minták](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
