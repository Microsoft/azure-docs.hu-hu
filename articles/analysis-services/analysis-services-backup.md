---
title: Adatbázis biztonsági mentése és visszaállítása Azure Analysis Services | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni és visszaállítani a modell metaadatait és adatait egy Azure Analysis Services adatbázisból.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 6c2ea063e37660767d266c15dcfc471ee5d73a02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732108"
---
# <a name="analysis-services-database-backup-and-restore"></a>Adatbázis biztonsági mentése és visszaállítása Analysis Services

A táblázatos modell adatbázisainak biztonsági mentése Azure Analysis Servicesban ugyanúgy történik, mint a helyszíni Analysis Services. Az elsődleges különbség az, hogy hol tárolja a biztonságimásolat-fájlokat. A biztonsági mentési fájlokat egy [Azure Storage-fiókban](../storage/common/storage-account-create.md)lévő tárolóba kell menteni. Használhat egy már meglévő Storage-fiókot és-tárolót, vagy létrehozhatók a kiszolgáló tárolási beállításainak konfigurálásakor.

> [!NOTE]
> A Storage-fiók létrehozása egy új számlázható szolgáltatáshoz vezethet. További információt az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/blobs/)ismertető témakörben talál.
> 
> 

> [!NOTE]
> Ha a Storage-fiók egy másik régióban található, konfigurálja a Storage-fiók tűzfala beállításait, hogy engedélyezze a hozzáférést a **kiválasztott hálózatokból**. A tűzfal **címtartomány** területen válassza ki az IP-címtartományt azon régió számára, amelyen a Analysis Services-kiszolgáló található. A Storage-fiók tűzfala beállításainak konfigurálása az összes hálózatról való hozzáférés engedélyezéséhez, azonban a kiválasztott hálózatok kiválasztása és az IP-címtartomány megadása javasolt. További információ: [hálózati kapcsolat – gyakori kérdések](analysis-services-network-faq.md#backup-and-restore).

A biztonsági mentések. ABF kiterjesztéssel lesznek mentve. A memóriában tárolt táblázatos modellek esetében a modellre vonatkozó adatokat és metaadatokat is tárolja a rendszer. A DirectQuery táblázatos modellek esetében csak a modell metaadatai vannak tárolva. A kiválasztott beállításoktól függően a biztonsági másolatok tömörítve és titkosítva is lehetnek.


## <a name="configure-storage-settings"></a>Tárolási beállítások konfigurálása
A biztonsági mentés előtt konfigurálnia kell a kiszolgáló tárolási beállításait.


### <a name="to-configure-storage-settings"></a>A tárolási beállítások konfigurálása
1.  Azure Portal > **Beállítások** területen kattintson a **biztonsági mentés** elemre.

    ![Biztonsági másolatok a beállításokban](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kattintson az **engedélyezve** elemre, majd a **tárolási beállítások** elemre.

    ![Engedélyezés](./media/analysis-services-backup/aas-backup-enable.png)

3. Válassza ki a Storage-fiókot, vagy hozzon létre egy újat.

4. Válasszon ki egy tárolót, vagy hozzon létre újat.

    ![Tároló kiválasztása](./media/analysis-services-backup/aas-backup-container.png)

5. Mentse a biztonsági mentési beállításokat.

    ![Biztonsági mentési beállítások mentése](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Biztonsági mentés a SSMS használatával

1. A SSMS kattintson a jobb gombbal egy adatbázisra, > **biztonsági mentést**.

2. A **biztonsági mentési adatbázis**  >  **biztonságimásolat-fájljában** kattintson a **Tallózás** gombra.

3. A **fájl mentése másként** párbeszédpanelen ellenőrizze a mappa elérési útját, majd írja be a biztonságimásolat-fájl nevét. 

4. A **biztonsági mentési adatbázis** párbeszédpanelen válassza a beállítások lehetőséget.

    **Fájl felülírásának engedélyezése** – ezzel a beállítással felülírhatja az azonos nevű biztonságimásolat-fájlokat. Ha ez a beállítás nincs bejelölve, a menteni kívánt fájl neve nem egyezhet meg egy olyan fájllal, amely már létezik ugyanazon a helyen.

    **Tömörítés alkalmazása** – ezzel a beállítással tömörítheti a biztonságimásolat-fájlt. A tömörített biztonságimásolat-fájlok lemezterületet takarítanak meg, de valamivel nagyobb CPU-kihasználtságot igényelnek. 

    **Biztonságimásolat-fájl titkosítása** – ezzel a beállítással titkosíthatja a biztonságimásolat-fájlt. Ehhez a beállításhoz felhasználó által megadott jelszó szükséges a biztonságimásolat-fájl biztonságossá tételéhez. A jelszó nem teszi lehetővé a biztonsági mentési információk olvasását a visszaállítási művelethez hasonló módon. Ha úgy dönt, hogy titkosítja a biztonsági mentéseket, tárolja biztonságos helyen a jelszót.

5. A biztonságimásolat-fájl létrehozásához és mentéséhez kattintson **az OK** gombra.


### <a name="powershell"></a>PowerShell
Használja a [Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase) parancsmagot.

## <a name="restore"></a>Visszaállítás
Visszaállításkor a biztonságimásolat-fájlnak a kiszolgálóhoz konfigurált Storage-fiókban kell lennie. Ha a biztonságimásolat-fájlt egy helyszíni helyről a Storage-fiókba kell áthelyeznie, használja a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) vagy a [AzCopy](../storage/common/storage-use-azcopy-v10.md) parancssori segédprogramot. 



> [!NOTE]
> Ha egy helyszíni kiszolgálóról végzi a visszaállítást, el kell távolítania az összes tartományi felhasználót a modell szerepköreiből, és hozzá kell adnia őket a szerepkörökhöz Azure Active Directory felhasználóként.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Visszaállítás a SSMS használatával

1. A SSMS kattintson a jobb gombbal egy adatbázisra, > a **visszaállítás** elemre.

2. A **biztonsági mentési adatbázis** párbeszédpanel biztonságimásolat- **fájl** területén kattintson a **Tallózás** gombra.

3. Az **adatbázisfájlok megkeresése** párbeszédpanelen válassza ki a visszaállítani kívánt fájlt.

4. A **Restore Database (adatbázis visszaállítása**) területen válassza ki az adatbázist.

5. Beállítások megadása A biztonsági beállításoknak meg kell egyezniük a biztonsági mentéskor használt biztonsági mentési beállításokkal.


### <a name="powershell"></a>PowerShell

Használja a [Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase) parancsmagot.


## <a name="related-information"></a>Kapcsolódó információk

[Azure Storage-fiókok](../storage/common/storage-account-create.md)  
[Magas rendelkezésre állás](analysis-services-bcdr.md)      
[Analysis Services hálózati kapcsolat – gyakori kérdések](analysis-services-network-faq.md)