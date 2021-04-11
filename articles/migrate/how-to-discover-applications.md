---
title: A Azure Migrate-t tartalmazó helyszíni kiszolgálókon lévő szoftverhasználat felderítése
description: Megtudhatja, hogyan derítheti fel a helyszíni kiszolgálókon a Azure Migrate felderítéssel és értékeléssel rendelkező szoftvereket.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: d68c3729e8a63f8342cd51b62413aec3276c6165
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871010"
---
# <a name="discover-installed-software-inventory-and-sql-server-instances-and-databases"></a>Telepített szoftverek felderítése, és SQL Server példányok és adatbázisok

Ez a cikk bemutatja, hogyan derítheti fel a telepített szoftvereket, és hogyan SQL Server példányokat és adatbázisokat a VMware-környezetben futó kiszolgálókon Azure Migrate: felderítési és értékelési eszköz használatával.

A szoftver leltározása segít azonosítani és testre szabni az Azure-ba történő áttelepítési útvonalat a számítási feladatokhoz. A szoftveres leltár a Azure Migrate berendezés használatával hajtja végre a felderítést a kiszolgálói hitelesítő adatokkal. Teljesen ügynök nélküli – nincsenek ügynökök telepítve a kiszolgálókra az adatok összegyűjtéséhez.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott egy projektet](./create-manage-projects.md) a Azure Migrate: felderítési és értékelési eszköz hozzá lett adva.
- Tekintse át a [VMware-követelményeket](migrate-support-matrix-vmware.md#vmware-requirements) a szoftverek leltárának végrehajtásához.
- A berendezés beállítása előtt tekintse át a [készülékre vonatkozó követelményeket](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) .
- Tekintse át az [alkalmazás-felderítési követelményeket](migrate-support-matrix-vmware.md#application-discovery-requirements) , mielőtt elindítja a szoftverek leltározását a kiszolgálókon.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>A Azure Migrate berendezés üzembe helyezése és konfigurálása

1. [Tekintse át](migrate-appliance.md#appliance---vmware) az Azure Migrate berendezés üzembe helyezésének követelményeit.
2. Tekintse át azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati felhőkben](migrate-appliance.md#government-cloud-urls).
3. [Tekintse át](migrate-appliance.md#collected-data---vmware) a készülék által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-vmware.md#port-access-requirements) fel a port hozzáférési követelményeit a készülékhez.
5. [Telepítse a Azure Migrate készüléket](how-to-set-up-appliance-vmware.md) a felderítés elindításához. A készülék üzembe helyezéséhez le kell töltenie és importálnia kell egy PETESEJT-sablont a VMware-be a vCenter Server-t futtató kiszolgáló létrehozásához. A készülék telepítése után regisztrálnia kell a projektben, és konfigurálnia kell a felderítés elindításához.
6. A készülék konfigurálásakor a következőket kell megadnia a készülék Configuration Managerben:
    - Annak a vCenter Servernak a részletei, amelyhez csatlakozni szeretne.
    - vCenter Server hitelesítő adatok hatóköre a VMware-környezetben található kiszolgálók felderítéséhez.
    - Kiszolgáló hitelesítő adatai, amelyek lehetnek tartomány/Windows (nem tartományi)/Linux (nem tartományi) hitelesítő adatok. [További](add-server-credentials.md) információ a hitelesítő adatok megadásáról és kezeléséről.

## <a name="verify-permissions"></a>Engedélyek ellenőrzése

- [Létre kell hoznia egy vCenter Server írásvédett fiókot](./tutorial-discover-vmware.md#prepare-vmware) a felderítéshez és értékeléshez. A csak olvasási jogosultsággal rendelkező fióknak engedélyezve kell lennie az **Virtual Machines**  >  **vendég műveleteihez**, hogy a-kiszolgálókkal való kommunikációhoz a szoftveres leltárt lehessen végezni.
- Az alkalmazások felderítéséhez több tartományt és tartományon kívüli (Windows/Linux) hitelesítő adatot is hozzáadhat a készülék Configuration Managerben. Szüksége van egy vendég felhasználói fiókra a Windows-kiszolgálókhoz, valamint egy normál/normál felhasználói fiókot (nem sudo hozzáférés) az összes Linux-kiszolgálóhoz. [További](add-server-credentials.md) információ a hitelesítő adatok megadásáról és kezeléséről.

### <a name="add-credentials-and-initiate-discovery"></a>Hitelesítő adatok hozzáadása és felderítés kezdeményezése

1. Nyissa meg a készülék Configuration Managert, fejezze be a berendezés előfeltételeinek ellenőrzését és regisztrációját.
2. Navigáljon a **hitelesítő adatok kezelése és a felderítési források** panelre.
1.  Az **1. lépés: vCenter Server hitelesítő adatok megadása** lehetőségre kattintva adja meg a hitelesítő adatok **hozzáadása** lehetőséget annak a vCenter Server-fióknak a hitelesítő adatainak megadásához, amelyet a berendezés a vCenter Server futó kiszolgálók felderítéséhez használ majd.
1. A **2. lépés: adja meg vCenter Server részleteket**, kattintson a **felderítési forrás hozzáadása** lehetőségre a hitelesítő adatok rövid nevének a legördülő listából való kiválasztásához, adja meg a vCenter Server példány panelének **IP-címét/teljes tartománynevét** a :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="készülék Configuration Managerben vCenter Server részletek":::
1. A **3. lépés: adja meg a kiszolgálói hitelesítő adatokat a szoftverek leltározásához, az ügynök nélküli függőségek elemzéséhez és a SQL Server példányok és adatbázisok felderítéséhez**, kattintson a **hitelesítő adatok hozzáadása** lehetőségre, hogy több kiszolgáló hitelesítő adatokat szolgáltasson a szoftverek leltárának indításához.
1. Kattintson a **felderítés indítása** lehetőségre, hogy kirúgja vCenter Server felderítést.

 A vCenter Server felderítés befejezése után a készülék elindítja a telepített alkalmazások, szerepkörök és szolgáltatások felderítését (a szoftver leltározását). Az időtartam a felderített kiszolgálók számától függ. 500-kiszolgálók esetében körülbelül egy óra elteltével a felderített leltár megjelenik a Azure Migrate-portálon.

## <a name="review-and-export-the-inventory"></a>A leltár áttekintése és exportálása

A szoftver leltárának befejeződése után áttekintheti és exportálhatja a leltárt a Azure Portalban.

1. **Azure Migrate – Windows, Linux és SQL Server**  >  **Azure Migrate: felderítés és értékelés**, kattintson a megjelenített darabszámra a **felderített kiszolgálók** lap megnyitásához.

    > [!NOTE]
    > Ezen a ponton engedélyezheti a függőségi elemzést is a felderített kiszolgálókon, így megjelenítheti a függőségeket az értékelni kívánt kiszolgálók között. [További](concepts-dependency-visualization.md) információ a függőségek elemzéséről.

2. A **szoftverfrissítési** oszlopban kattintson a megjelenített darabszámra a felderített alkalmazások, szerepkörök és szolgáltatások áttekintéséhez.
4. A leltár exportálásához a **felderített kiszolgálók** területen kattintson az **alkalmazás leltározásának exportálása** lehetőségre.

A szoftver leltára Excel-formátumban van exportálva és letöltve. A **szoftver leltározási** táblázata az összes kiszolgálón felderített alkalmazást jeleníti meg.

## <a name="discover-sql-server-instances-and-databases"></a>SQL Server példányok és adatbázisok felderítése

- A szoftveres leltár a VMware-környezetben futó SQL Server példányokat is azonosítja.
- Ha nem adott meg Windows-hitelesítést vagy SQL Server hitelesítő adatokat a készülék Configuration Managerben, akkor adja hozzá a hitelesítő adatokat, hogy a készülék használhassa őket a megfelelő SQL Server példányokhoz való kapcsolódáshoz.

A csatlakozás után a készülék összegyűjti SQL Server példányok és adatbázisok konfigurációs és teljesítményadatokat. A SQL Server konfigurációs adatai 24 óránként frissülnek, és a teljesítményadatokat 30 másodpercenként rögzíti a rendszer. Így a SQL Server példány és az adatbázisok (például az adatbázis állapota, a kompatibilitási szint stb.) tulajdonságainak módosítása akár 24 órát is igénybe vehet a portálon való frissítéshez.

## <a name="next-steps"></a>Következő lépések

- [Felmérés létrehozása](how-to-create-assessment.md) a felderített kiszolgálókhoz.
- [SQL-kiszolgálók felmérése](./tutorial-assess-sql.md) az Azure SQL-re való áttelepítéshez.
