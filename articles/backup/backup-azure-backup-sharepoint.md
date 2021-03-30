---
title: SharePoint-farm biztonsági mentése az Azure-ba a DPM használatával
description: Ez a cikk áttekintést nyújt a SharePoint-farmok Azure-ba való DPM/Azure Backup kiszolgáló védelméről
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 7661d64e487c8b8badca240852d17bcf736ba8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254431"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>SharePoint-farm biztonsági mentése az Azure-ba a DPM használatával

A SharePoint-farmról biztonsági mentést készíthet a System Center Data Protection Manager (DPM) szolgáltatással való Microsoft Azureére, ugyanúgy, mint az egyéb adatforrások biztonsági mentése. Azure Backup rugalmasságot biztosít a biztonsági mentési ütemezésben napi, heti, havi vagy éves biztonsági mentési pontok létrehozásához, és adatmegőrzési házirend-beállításokat biztosít a különböző biztonsági mentési pontokhoz. A DPM lehetővé teszi a helyi lemezek másolását a gyors helyreállítási idejű célkitűzésekhez (RTO), valamint a másolatok Azure-ba történő tárolását gazdaságos, hosszú távú adatmegőrzésre.

A SharePoint az Azure-ba történő biztonsági mentése nagyon hasonló folyamat a SharePoint helyi DPM történő biztonsági mentésének DPM. Az Azure-ra vonatkozó különös megfontolásokat ebben a cikkben fogjuk feltüntetni.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>A SharePoint által támogatott verziók és kapcsolódó védelmi forgatókönyvek

A támogatott SharePoint-verziók, illetve a biztonsági mentésükhöz szükséges DPM-verziók felsorolását a [Miről tud biztonsági másolatot készíteni a DPM?](/system-center/dpm/dpm-protection-matrix#applications-backup) című cikk tartalmazza.

## <a name="before-you-start"></a>Előkészületek

A SharePoint-farmok Azure-ba történő biztonsági mentése előtt néhány dolgot meg kell erősítenie.

### <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy teljesítette az [Microsoft Azure Backup használatára vonatkozó összes előfeltételt](backup-azure-dpm-introduction.md#prerequisites-and-limitations) a munkaterhelések elleni védelemhez. Az előfeltételek egyes feladatai közé tartozik a Backup-tároló létrehozása, a tár hitelesítő adatainak letöltése, Azure Backup ügynök telepítése, valamint a DPM/Azure Backup Server regisztrálása a tárolóval.

További előfeltételeket és korlátozásokat a [SharePoint biztonsági mentése a DPM](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations) című cikkben találhat.

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

A SharePoint-farm biztonsági mentéséhez konfigurálja a SharePoint-védelmet a ConfigureSharePoint.exe segítségével, majd hozzon létre egy védelmi csoportot a DPM-ben. Útmutatásért lásd: a [biztonsági mentés konfigurálása](/system-center/dpm/back-up-sharepoint#configure-backup) a DPM dokumentációjában.

## <a name="monitoring"></a>Figyelés

A biztonsági mentési feladat figyeléséhez kövesse a [DPM biztonsági mentésének figyelése](/system-center/dpm/back-up-sharepoint#monitoring) című témakör útmutatását.

## <a name="restore-sharepoint-data"></a>SharePoint-adatok helyreállítása

A SharePoint-elemek DPM-alapú lemezről történő visszaállításáról a SharePoint- [adatok visszaállítása](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)című témakörben olvashat bővebben.

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával

1. SharePoint tartalom-adatbázis helyreállításához tallózzon a különböző helyreállítási pontok között (ahogy az előzőekben látható), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![DPM SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. A SharePoint-katalógus elérhető adatainak megjelenítéséhez kattintson duplán a SharePoint helyreállítási pontra.

   > [!NOTE]
   > Mivel a SharePoint-farm védelme hosszú távú adatmegőrzést biztosít az Azure-ban, a DPM-kiszolgálón nem érhető el katalógus-információ (metaadatok). Ennek eredményeképpen, amikor egy időponthoz tartozó SharePoint tartalmi adatbázist helyre kell állítani, újra kell katalogizálni a SharePoint-farmot.
   >
   >
3. Válassza az **újrakatalogizálás** lehetőséget.

    ![DPM SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Megnyílik a **felhő-újrakatalogizálás** állapota ablak.

    ![DPM SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    A katalogizálás befejezése után az állapot *sikeresre* változik. Válassza a **Bezárás** gombot.

    ![DPM SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. A tartalom-adatbázis struktúrájának lekéréséhez válassza ki a DPM- **helyreállítás** lapon megjelenő SharePoint-objektumot. Kattintson a jobb gombbal az elemre, majd válassza a **helyreállítás** lehetőséget.

    ![DPM SharePoint-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a cikkben korábban ismertetett helyreállítási lépéseket a SharePoint tartalmi adatbázis lemezről történő helyreállításához.

## <a name="switching-the-front-end-web-server"></a>A Front-End webkiszolgáló váltása

Ha több előtér-webkiszolgálóval rendelkezik, és át szeretné váltani a DPM által a farm védeleméhez használt kiszolgálót, kövesse az [Front-End webkiszolgáló váltása](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server)című témakör útmutatását.

## <a name="next-steps"></a>Következő lépések

* [Azure Backup Server és DPM – gyakori kérdések](backup-azure-dpm-azure-server-faq.md)
* [A System Center Data Protection Manager hibaelhárítása](backup-azure-scdpm-troubleshooting.md)
