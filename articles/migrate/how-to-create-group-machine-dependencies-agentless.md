---
title: Ügynök nélküli függőségi elemzés beállítása Azure Migrate Server Assessment-ben
description: Az ügynök nélküli függőségek elemzésének beállítása Azure Migrate Server Assessment-ben.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045853"
---
# <a name="analyze-server-dependencies-agentless"></a>Kiszolgáló függőségeinek elemzése (ügynök nélküli)

Ez a cikk azt ismerteti, hogyan állítható be az ügynök nélküli függőségi elemzés a Azure Migrate: Server Assessment használatával. A függőségek [elemzése](concepts-dependency-visualization.md) segítséget nyújt a kiszolgálók közötti függőségek azonosításában és értelmezésében az Azure-ba történő értékeléshez és áttelepítéshez.

> [!IMPORTANT]
> Az ügynök nélküli függőség elemzése jelenleg előzetes verzióban érhető el a VMware-környezetben futó, a Azure Migrate: Server Assessment eszközzel felderített kiszolgálók esetében.
> Ezt az előzetes verziót az ügyfélszolgálat támogatja, és az éles számítási feladatokhoz is használható.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuális korlátozások

- A függőség elemzése nézetben jelenleg nem lehet kiszolgálót felvenni vagy eltávolítani egy csoportból.
- Jelenleg nem érhető el függőségi Térkép a kiszolgálók csoportjához.
- Egy Azure Migrate projektben a függőségi adatgyűjtés egyidejűleg engedélyezhető a 1000-kiszolgálókhoz. A nagyobb számú kiszolgálót elemezheti a 1000-es kiszolgálók kötegei szerinti előkészítéssel.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott egy Azure Migrate projektet](./create-manage-projects.md) a Azure Migrate: Server Assessment Tool hozzáadásával.
- A függőségi elemzés végrehajtásához tekintse át a [VMware követelményeit](migrate-support-matrix-vmware.md#vmware-requirements) .
- A berendezés beállítása előtt tekintse át a [készülékre vonatkozó követelményeket](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) .
- [Tekintse át a függőségek elemzésének követelményeit](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) , mielőtt engedélyezné a függőségi elemzést a kiszolgálókon.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>A Azure Migrate berendezés üzembe helyezése és konfigurálása

1. [Tekintse át](migrate-appliance.md#appliance---vmware) az Azure Migrate berendezés üzembe helyezésének követelményeit.
2. Tekintse át azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati felhőkben](migrate-appliance.md#government-cloud-urls).
3. [Tekintse át](migrate-appliance.md#collected-data---vmware) a készülék által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-vmware.md#port-access-requirements) fel a port hozzáférési követelményeit a készülékhez.
5. [Telepítse a Azure Migrate készüléket](how-to-set-up-appliance-vmware.md) a felderítés elindításához. A készülék üzembe helyezéséhez le kell töltenie és importálnia kell egy PETESEJT-sablont a VMware-be a vCenter Server-t futtató kiszolgáló létrehozásához. A készülék telepítése után regisztrálnia kell a Azure Migrate projekttel, és konfigurálnia kell a felderítés elindításához.
6. A készülék konfigurálásakor a következőket kell megadnia a készülék Configuration Managerben:
    - Annak a vCenter Servernak a részletei, amelyhez csatlakozni szeretne.
    - vCenter Server hitelesítő adatok hatóköre a VMware-környezetben található kiszolgálók felderítéséhez.
    - A kiszolgáló hitelesítő adatai, amelyek lehetnek tartomány/Windows (nem tartományi)/Linux (nem tartományi) hitelesítő adatok. [További](add-server-credentials.md) információ a hitelesítő adatok megadásáról és kezeléséről.

## <a name="verify-permissions"></a>Engedélyek ellenőrzése

- [Létre kell hoznia egy vCenter Server írásvédett fiókot](./tutorial-discover-vmware.md#prepare-vmware) a felderítéshez és értékeléshez. A csak olvasási jogosultsággal rendelkező fióknak engedélyezve kell lennie **Virtual Machines**  >  **vendég műveleteihez**, hogy együttműködjön a kiszolgálókkal a függőségi adatok gyűjtéséhez.
- Szüksége lesz egy felhasználói fiókra, hogy a kiszolgáló értékelése hozzáférhessen a kiszolgálóhoz a függőségi adatok összegyűjtéséhez. A Windows-és Linux-kiszolgálók fiókra vonatkozó követelményeinek [megismerése](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) .

### <a name="add-credentials-and-initiate-discovery"></a>Hitelesítő adatok hozzáadása és felderítés kezdeményezése

1. Nyissa meg a készülék Configuration Managert, fejezze be a berendezés előfeltételeinek ellenőrzését és regisztrációját.
2. Navigáljon a **hitelesítő adatok kezelése és a felderítési források** panelre.
1.  Az **1. lépés: vCenter Server hitelesítő adatok megadása** lehetőségre kattintva adja meg a hitelesítő adatok **hozzáadása** lehetőséget annak a vCenter Server-fióknak a hitelesítő adatainak megadásához, amelyet a berendezés a vCenter Server futó kiszolgálók felderítéséhez használ majd.
1. A **2. lépés: adja meg vCenter Server részleteket**, kattintson a **felderítési forrás hozzáadása** lehetőségre a hitelesítő adatok rövid nevének a legördülő listából való kiválasztásához, adja meg a vCenter Server példány panelének **IP-címét/teljes tartománynevét** a :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="készülék Configuration Managerben vCenter Server részletek":::
1. A **3. lépés: adja meg a kiszolgálói hitelesítő adatokat a szoftverek leltározásához, az ügynök nélküli függőségek elemzéséhez és a SQL Server példányok és adatbázisok felderítéséhez**, kattintson a **hitelesítő adatok hozzáadása** lehetőségre, hogy több kiszolgáló hitelesítő adatokat szolgáltasson a szoftverek leltárának indításához.
1. Kattintson a **felderítés indítása** lehetőségre, hogy kirúgja vCenter Server felderítést.

 A vCenter Server felderítés befejezése után a készülék elindítja a telepített alkalmazások, szerepkörök és szolgáltatások felderítését (szoftver leltározása). A szoftver leltározása során a hozzáadott kiszolgálók hitelesítő adatai a kiszolgálókon lesznek megerősítve, és az ügynök nélküli függőségek elemzéséhez vannak érvényesítve. Engedélyezheti a kiszolgálók ügynök nélküli függőségi elemzését a portálon. Csak azok a kiszolgálók választhatók ki, amelyeken az érvényesítés sikeres, így az ügynök nélküli függőségek elemzése is engedélyezhető.

## <a name="start-dependency-discovery"></a>Függőségi felderítés elindítása

Válassza ki azokat a kiszolgálókat, amelyeken engedélyezni szeretné a függőségi felderítést.

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók** elemre.
2. Válassza ki a **berendezés nevét** , amelynek a felderítését szeretné áttekinteni.
1. A kiszolgálók érvényesítési állapotát a **függőségek (ügynök nélküli)** oszlopban tekintheti meg.
1. Kattintson a **függőségi elemzés** legördülő listára.
1. Kattintson a **kiszolgálók hozzáadása** elemre.
1. A **kiszolgálók hozzáadása** lapon válassza ki azokat a kiszolgálókat, amelyeken engedélyezni szeretné a függőségi elemzést. A függőségek leképezését csak azokon a kiszolgálókon engedélyezheti, amelyeken az érvényesítés sikeres volt. A következő érvényesítési ciklus az utolsó érvényesítési időbélyeg után 24 órával fog futni.
1. A kiszolgálók kiválasztása után kattintson a **kiszolgálók hozzáadása** elemre.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Függőségi elemzés indítása":::

A függőségek elemzésének a kiszolgálókon való engedélyezése után hat órán belül megjelenítheti a függőségeket. Ha egyszerre több kiszolgálót szeretne engedélyezni a függőségek elemzéséhez, a [PowerShell](#start-or-stop-dependency-analysis-using-powershell) használatával is megteheti.

## <a name="visualize-dependencies"></a>Függőségek megjelenítése

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók** elemre.
1. Válassza ki a **berendezés nevét** , amelynek a felderítését szeretné áttekinteni.
1. Keresse meg azt a kiszolgálót, amelynek a függőségeit szeretné áttekinteni.
1. A **függőségek (ügynök nélküli)** oszlopban kattintson a **függőségek megtekintése** elemre.
1. Módosítsa azt az időtartamot, ameddig a térképet meg szeretné **tekinteni az időtartam legördülő lista** használatával.
1. Bontsa ki az **ügyféloldali** csoportot a kiszolgálók listázásához, amely a kiválasztott kiszolgálótól függ.
1. Bontsa ki a **portot** a kiválasztott kiszolgálótól függő kiszolgálók listázásához.
1. Ha az egyik függő kiszolgáló Térkép nézetére szeretne navigálni, kattintson a kiszolgáló nevére > **Load Server Map** 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="Expand Server port Group és Load Server Map":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Ügyféloldali csoport kibontása":::

8. Bontsa ki a kijelölt kiszolgálót az egyes függőségek feldolgozási szintű részleteinek megtekintéséhez.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="A kiszolgáló kibontása a folyamatok megjelenítéséhez":::

> [!NOTE]
> A függőségi adatok feldolgozása nem mindig érhető el. Ha nem érhető el, a függőséget az "ismeretlen folyamat" jelölésű folyamat mutatja.

## <a name="export-dependency-data"></a>Függőségi adatgyűjtés exportálása

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók** elemre.
2. Kattintson a **függőségi elemzés** legördülő listára.
3. Kattintson az **alkalmazás függőségeinek exportálása** elemre.
4. Az **alkalmazás-függőségek exportálása** lapon válassza ki a berendezés nevét, amely a kívánt kiszolgálókat felfedi.
5. Válassza ki a kezdési és befejezési időpontot. Vegye figyelembe, hogy csak az elmúlt 30 napban töltheti le az adatgyűjtést.
6. Kattintson a **függőség exportálása** elemre.

A függőségi adatfájlok exportálása és letöltése CSV-formátumban történik. A letöltött fájl a függőségi elemzéshez engedélyezett összes kiszolgáló függőségi adatai között található. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Függőségek exportálása":::

### <a name="dependency-information"></a>Függőségi adatok

Az exportált CSV-fájl minden sora a megadott időpontban megfigyelt függőségnek felel meg.

Az alábbi táblázat összefoglalja az exportált CSV-fájl mezőit. Vegye figyelembe, hogy a kiszolgáló neve, az alkalmazás és a folyamat mező csak olyan kiszolgálókon töltődik fel, amelyeken engedélyezve van az ügynök nélküli függőségi elemzés.

**Mezőnév** | **Részletek**
--- | --- 
Időrés | Az a időrés, amely alatt a függőség megfigyelhető. <br/> A függőségi adat jelenleg 6 órás tárolóhelyre van rögzítve.
Forráskiszolgáló neve | A forráskiszolgáló neve 
Forrásoldali alkalmazás | Az alkalmazás neve a forráskiszolgálón  
Forrásoldali folyamat | A forráskiszolgáló folyamatának neve  
Célkiszolgáló neve | A célkiszolgáló neve
Cél IP-címe | A célkiszolgáló IP-címe
Célalkalmazás | Az alkalmazás neve a célkiszolgálón
Cél folyamat | A célkiszolgálón található folyamat neve  
Célport | Portszám a célkiszolgálón

## <a name="stop-dependency-discovery"></a>Függőségi felderítés leállítása

Válassza ki azokat a kiszolgálókat, amelyeken le szeretné állítani a függőségi felderítést.

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók** elemre.
1. Válassza ki a **berendezés nevét** , amelynek a felderítését szeretné áttekinteni.
1. Kattintson a **függőségi elemzés** legördülő listára.
1. Kattintson a **kiszolgálók eltávolítása** elemre.
1. A **kiszolgálók eltávolítása** lapon válassza ki azt a kiszolgálót, amelynél le szeretné állítani a függőségi elemzést.
1. A kiszolgálók kiválasztása után kattintson a **kiszolgálók eltávolítása** elemre.

Ha egyszerre több kiszolgálón szeretné leállítani a függőséget, akkor a [PowerShell](#start-or-stop-dependency-analysis-using-powershell) használatával teheti meg.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Függőségi elemzés indítása vagy leállítása a PowerShell használatával

Töltse le a PowerShell-modult [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) -tárházból a githubon.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

1. Jelentkezzen be az Azure-előfizetésbe a Connect-AzAccount parancsmag használatával.

    ```PowerShell
    Connect-AzAccount
    ```
    Azure Government használata esetén használja a következő parancsot.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Válassza ki azt az előfizetést, amelyben létrehozta a Azure Migrate projektet 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. A letöltött AzMig_Dependencies PowerShell-modul importálása

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Függőségi adatgyűjtés engedélyezése vagy letiltása

1. Szerezze be a felderített kiszolgálók listáját a Azure Migrate projektben az alábbi parancsokkal. Az alábbi példában a projekt neve FabrikamDemoProject, és a hozzá tartozó erőforráscsoport FabrikamDemoRG. A kiszolgálók listája FabrikamDemo_VMs.csv lesz mentve

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    A fájlban megtekintheti a kiszolgáló megjelenítendő nevét, a függőségi gyűjtemény aktuális állapotát és az összes felderített kiszolgáló ARM-AZONOSÍTÓját. 

2. A függőségek engedélyezéséhez vagy letiltásához hozzon létre egy bemeneti CSV-fájlt. A fájlnak rendelkeznie kell egy "ARM ID" fejléctel rendelkező oszloppal. A CSV-fájlban szereplő további fejlécek figyelmen kívül lesznek hagyva. A CSV-t az előző lépésben létrehozott fájl használatával hozhatja létre. Hozzon létre egy másolatot a fájlról, és őrizze meg azokat a kiszolgálókat, amelyeken engedélyezni vagy letiltani szeretné a függőségeket. 

    A következő példában a függőségi elemzés engedélyezve van a bemeneti fájlban található kiszolgálók listáján FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    A következő példában a függőségi elemzés le van tiltva a bemeneti fájlban található kiszolgálók listáján FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Hálózati kapcsolatok megjelenítése Power BIban

Azure Migrate olyan Power BI-sablont kínál, amellyel egyszerre több kiszolgáló hálózati kapcsolatai jeleníthetők meg, és a szűrés folyamat és kiszolgáló alapján. A megjelenítéshez a következő utasítások szerint töltse be a Power BI függőségi adattal.

1. Töltse le a PowerShell-modult és a Power BI sablont a GitHubon található [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) tárházból.

2. Jelentkezzen be az Azure-ba az alábbi utasítások alapján: 
    - Jelentkezzen be az Azure-előfizetésbe a Connect-AzAccount parancsmag használatával.

        ```PowerShell
        Connect-AzAccount
        ```

    - Azure Government használata esetén használja a következő parancsot.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Válassza ki azt az előfizetést, amelyben létrehozta a Azure Migrate projektet

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. A letöltött AzMig_Dependencies PowerShell-modul importálása

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Futtassa az alábbi parancsot. Ez a parancs letölti a függőségi adatmennyiséget egy CSV-fájlban, és feldolgozza azokat az egyedi függőségek listájának létrehozásához, amelyeket a Power BI vizualizációhoz használhat. Az alábbi példában a projekt neve FabrikamDemoProject, és a hozzá tartozó erőforráscsoport FabrikamDemoRG. A függőségek a FabrikamAppliance által felderített kiszolgálókon lesznek letöltve. Az egyedi függőségek a FabrikamDemo_Dependencies.csvba lesznek mentve

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. A letöltött Power BI sablon megnyitása

6. Töltse be Power BI a letöltött függőségi adatkészleteket.
    - Nyissa meg Power BI a sablont.
    - Kattintson az **adatlekérdezés** gombra az eszköztáron. 
    - Válassza a **text/CSV** elemet a gyakori adatforrások közül.
    - Válassza ki a letöltött függőségek fájlt.
    - Kattintson a **Betöltés** elemre.
    - Megjelenik egy tábla, amely a CSV-fájl nevével lesz importálva. A táblázat a jobb oldali mezők sávban látható. Nevezze át AzMig_Dependencies
    - Kattintson a frissítés elemre az eszköztáron.

    A hálózati kapcsolatok diagram és a forráskiszolgáló neve, a célkiszolgáló neve, a forrás folyamat neve, a cél folyamat neve szeletelők az importált értékekkel.

7. Jelenítse meg a hálózati kapcsolatok leképezését kiszolgálók és folyamatok alapján. Mentse a fájlt.

## <a name="next-steps"></a>Következő lépések

[Csoportosítsa a kiszolgálókat](how-to-create-a-group.md) az értékeléshez.