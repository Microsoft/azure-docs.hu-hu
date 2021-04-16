---
title: SharePoint-farm biztonsági biztonságiának biztonsági használata az Azure-ba a DPM használatával
description: Ez a cikk áttekintést nyújt a SharePoint-farmok Azure-ba Azure Backup DPM/Azure Backup kiszolgálói védelméről
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 3524107b545c151fcf931b89c629a61d83f47ace
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515158"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>SharePoint-farm biztonsági biztonságiának biztonsági használata az Azure-ba a DPM használatával

A SharePoint-farmok biztonsági Microsoft Azure a System Center Data Protection Manager (DPM) használatával, ugyanúgy, mint más adatforrások. Azure Backup a biztonsági mentési ütemezésben rugalmasságot biztosít a napi, heti, havi vagy éves biztonsági mentési pontok létrehozásához, és megőrzési házirend-beállításokat biztosít a különböző biztonsági mentési pontokhoz. A DPM lehetővé teszi a helyi lemezmásolatok tárolására a gyors helyreállítási időre vonatkozó célkitűzések (RTO) és a másolatok Azure-ba való tárolására a gazdaságos, hosszú távú megőrzés érdekében.

A SharePoint azure-ba való biztonsági biztonságiolása a DPM-hez hasonló folyamat, mint a SharePoint helyi biztonságimentés a DPM-re. Ebben a cikkben az Azure-ral kapcsolatos konkrét szempontokat ismertetünk.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>A SharePoint által támogatott verziók és kapcsolódó védelmi forgatókönyvek

A támogatott SharePoint-verziók, illetve a biztonsági mentésükhöz szükséges DPM-verziók felsorolását a [Miről tud biztonsági másolatot készíteni a DPM?](/system-center/dpm/dpm-protection-matrix#applications-backup) című cikk tartalmazza.

## <a name="before-you-start"></a>Előkészületek

Néhány dolgot meg kell erősítenie, mielőtt biztonsági szolgáltatásokat ad egy SharePoint-farmról az Azure-ba.

### <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy az összes előfeltétel teljesült a számítási feladatok Microsoft Azure Backup [való](backup-azure-dpm-introduction.md#prerequisites-and-limitations) használatának előfeltételeivel. Az előfeltételek többek között a következők: biztonsági mentési tároló létrehozása, tároló hitelesítő adatainak letöltése, Azure Backup ügynök telepítése és a DPM/Azure Backup Server regisztrálása a tárolóban.

További előfeltételeket és korlátozásokat a SharePoint biztonsági modulról [a DPM-hez való biztonsági frissítését bemutató cikkben talál.](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

A SharePoint-farm biztonsági mentéséhez konfigurálja a SharePoint-védelmet a ConfigureSharePoint.exe segítségével, majd hozzon létre egy védelmi csoportot a DPM-ben. Útmutatásért lásd [a DPM dokumentációjának Biztonsági](/system-center/dpm/back-up-sharepoint#configure-backup) mentés konfigurálása dokumentumát.

## <a name="monitoring"></a>Figyelés

A biztonsági mentési feladat figyelése érdekében kövesse a DPM biztonsági [mentésének figyelése](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>SharePoint-adatok helyreállítása

A SharePoint-elemek lemezről a DPM segítségével való visszaállításával kapcsolatos információkért lásd: [SharePoint-adatok visszaállítása.](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával

1. SharePoint-tartalomadatbázis helyreállításához tallózhat a különböző helyreállítási pontok között (ahogy korábban látható), és kiválaszthatja a visszaállítani kívánt helyreállítási pontot.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kattintson duplán a SharePoint helyreállítási pontra az elérhető SharePoint-katalógus információinak a megjelenítése érdekében.

   > [!NOTE]
   > Mivel a SharePoint-farm hosszú távú megőrzésre van védve az Azure-ban, a DPM-kiszolgálón nem érhetők el katalógusadatok (metaadatok). Ennek eredményeképpen minden alkalommal, amikor helyre kell állítani egy időponthoz tartozó SharePoint-tartalom-adatbázist, újból katalogizálni kell a SharePoint-farmot.
   >
   >
3. Válassza **a Re-catalog (Újrakatalógus) lehetőséget.**

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Megnyílik **a Cloud Recatalog** állapotablaka.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    A katalogizálás befejezése után az állapot Sikeres *állapotra változik.* Válassza a **Bezárás** gombot.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Válassza ki a **DPM** Helyreállítás lapján látható SharePoint-objektumot a tartalom-adatbázis struktúrájának lekért beállításához. Kattintson a jobb gombbal az elemre, majd válassza a Helyreállítás **lehetőséget.**

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a cikk korábbi, helyreállítási lépéseit egy SharePoint-tartalomadatbázis lemezről való helyreállításához.

## <a name="switching-the-front-end-web-server"></a>A webkiszolgáló Front-End váltása

Ha egynél több előoldali webkiszolgálóval is van, és át szeretné váltani a DPM által a farm védelmére használt kiszolgálót, kövesse a Front-End [váltása webkiszolgálóracímű útmutatót.](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server)

## <a name="next-steps"></a>Következő lépések

* [Azure Backup Server DPM – gyakori kérdések](backup-azure-dpm-azure-server-faq.yml)
* [A System Center Data Protection Manager hibaelhárítása](backup-azure-scdpm-troubleshooting.md)
