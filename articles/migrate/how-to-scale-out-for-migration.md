---
title: Azure Migrate kibővíthető berendezés beállítása az ügynök nélküli VMware-áttelepítés számára
description: Megtudhatja, hogyan állíthat be Azure Migrate kibővíthető készüléket a Hyper-V virtuális gépek áttelepítéséhez.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 634eb2d22e3fa570ac9412d4fb8afd917b5c2eaa
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564007"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>A VMware virtuális gépek ügynök nélküli áttelepítésének méretezése az Azure-ba

Ebből a cikkből megtudhatja, hogyan használható egy kibővíthető készülék nagy számú VMware virtuális gép (VM) áttelepítésére az Azure-ba az Azure Migrate Server áttelepítési eszköz ügynök nélküli módszerével a VMware virtuális gépek áttelepítéséhez.

A VMware virtuális gépek ügynök nélküli áttelepítési módszerének használatával a következőket teheti:

- Akár 300 virtuális gépet replikálhat egyetlen vCenter-kiszolgálóról egy Azure Migrate berendezés egyidejű használatával.
- Egyszerre legfeljebb 500 virtuális gépet replikálhat egyetlen vCenter-kiszolgálóról egy második kibővíthető berendezés áttelepítésre történő üzembe helyezésével.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

- Kibővíthető készülék hozzáadása a VMware virtuális gépek ügynök nélküli áttelepítéséhez
- Egyszerre legfeljebb 500 virtuális gépet telepíthet át a kibővíthető készülék használatával.

##  <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következő lépéseket kell elvégeznie:

- Hozza létre a Azure Migrate projektet.
- Telepítse az Azure Migrate berendezést (elsődleges berendezés), és fejezze be a vCenter-kiszolgáló által kezelt VMware virtuális gépek felderítését.
- Konfiguráljon egy vagy több áttelepítendő virtuális gép replikálását.
> [!IMPORTANT]
> A kibővített berendezés áttelepítéshez való hozzáadása előtt legalább egy replikáló virtuális gépnek kell lennie a projektben.

A fenti műveletek elvégzéséhez tekintse át a [VMWare virtuális gépek Azure-ba történő áttelepítésével foglalkozó oktatóanyagot az ügynök nélküli áttelepítési módszerrel](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Kibővíthető berendezés üzembe helyezése

A kibővíthető berendezések hozzáadásához kövesse az alábbi lépéseket:

1. A **felderítés** lehetőségre kattintva  >  **virtualizált a gépek?** 
1. Válassza **az igen, a VMware vSphere hypervisor lehetőséget.**
1. Válassza az ügynök nélküli replikáció lehetőséget a következő lépésben.
1. Válasszon ki **egy meglévő elsődleges készüléket** a készülék típusának kiválasztása menüben.
1. Válassza ki a kibővíteni kívánt elsődleges berendezést (az azt használó berendezést, amely a felderítést végezte).

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Gépek felderítése oldal a kibővíthető előkészítéshez":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. a Azure Migrate projekt kulcsának előállítása

1. A **Azure Migrate-projekt létrehozása kulcsban** adja meg a kibővíthető berendezés utótagjának nevét. Az utótag csak alfanumerikus karaktereket tartalmazhat, és legfeljebb 14 karakter hosszúságú lehet.
2. A szükséges Azure-erőforrások létrehozásának elindításához kattintson a **kulcs létrehozása** elemre. Ne zárja be a felderítés oldalt az erőforrások létrehozása során.
3. Másolja a generált kulcsot. A kibővíthető berendezés regisztrációjának befejezéséhez később szüksége lesz a kulcsra.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. a kibővíthető készülék telepítőjének letöltése

A **Azure Migrate készülék letöltése** területen kattintson a  **Letöltés** elemre. Le kell töltenie a PowerShell-telepítő parancsfájlt, hogy a kibővíthető készüléket egy Windows Server 2016 rendszert futtató meglévő kiszolgálóra telepítse, és a szükséges hardverkonfiguráció (32 GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárterület és internet-hozzáférés közvetlenül vagy egy proxyn keresztül).
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Parancsfájl letöltése kibővíthető készülékhez":::

> [!TIP]
> A letöltött zip-fájl ellenőrzőösszegét a következő lépésekkel ellenőrizheti:
>
> 1. Rendszergazdaként nyissa meg a parancssort
> 2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa a nyilvános felhő használatára: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. Töltse le a kibővíthető készülék telepítőjének legújabb verzióját a portálról, ha a számított kivonatoló érték nem egyezik a következő karakterlánccal: e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Futtassa a Azure Migrate telepítő parancsfájlt
A telepítő parancsfájl a következő műveleteket végzi el:

- Az átjáró-ügynök és a készülék Configuration Manager telepítése több egyidejű kiszolgáló-replikáció végrehajtásához.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a zip-fájlt egy olyan mappába a kiszolgálón, amely a kibővíthető készüléket fogja üzemeltetni.  Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezéssel rendelkező kiszolgálón.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell könyvtárat arra a mappára, ahol a tartalmat kibontották a letöltött zip-fájlból.
4. Futtassa a **AzureMigrateInstaller.ps1**  nevű szkriptet a következő paranccsal:

    - Nyilvános felhő esetén: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    A szkript elindítja a készülék Configuration Managert, amikor befejezi a végrehajtást.

Ha bármilyen probléma merül fel, a parancsfájl-naplókat a következő helyen érheti el: <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>időbélyeg</em>. log a hibaelhárításhoz.


### <a name="4-configure-the-appliance"></a>4. a berendezés konfigurálása

Mielőtt megkezdené, hogy [ezek az Azure-végpontok](migrate-appliance.md#public-cloud-urls) elérhetők legyenek a kibővíthető készülékről.

- Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a kibővíthető berendezési kiszolgálóhoz, és nyissa meg a készülék Configuration managerének URL-címét: **https://*kibővíthető berendezés neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja a Configuration Managert a kibővíthető berendezés kiszolgálójának asztaláról a Configuration Manager parancsikonjának használatával.
- Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
- A Configuration Manager > **Előfeltételek beállítása** területen tegye a következőket:
   - **Kapcsolat**: a készülék ellenőrzi, hogy a kiszolgáló rendelkezik-e internet-hozzáféréssel. Ha a kiszolgáló proxyt használ:
     1. A proxy **beállítása** elemre kattintva adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
     2. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     3. Csak a HTTP-proxyk használata támogatott.
     4. Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről. Az ellenőrzések befejezése után kattintson a **berendezés megtekintése** lehetőségre a berendezés-kiszolgálón futó szolgáltatások állapotának és verzióinak megtekintéséhez.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a berendezés megadott helyére a készülék Configuration Manager képernyőjének **telepítési utasításai** szerint.


### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése** lehetőségre, válassza ki az elsődleges készülék nevét, keresse meg a hozzá társított kibővítő készüléket, és másolja a megfelelő kulcsot.
1. Szüksége lesz egy eszköz kódjára az Azure-beli hitelesítéshez. A **Bejelentkezés** gombra kattintva megnyílik egy modális az eszköz kódjával az alább látható módon.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Az eszköz kódját ábrázoló modális":::

1. Kattintson a **kód másolása & a bejelentkezés** elemre az eszköz kódjának másolásához és egy új böngésző lapon található Azure-beli bejelentkezési kérés megnyitásához. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon illessze be az eszköz kódját, és jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. Ha a bejelentkezés lapot véletlenül a bejelentkezés nélkül zárta be, frissítenie kell a készülék Configuration Manager böngésző lapját, hogy ismét engedélyezze a bejelentkezés gombot.
1. Miután sikeresen bejelentkezett, lépjen vissza az előző lapra a készülék Configuration Managerrel.
1. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő engedélyekkel a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="2. panel a készülék Configuration Managerben":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Készülék konfigurációjának importálása az elsődleges berendezésből

A kibővített berendezés regisztrációjának befejezéséhez kattintson az **Importálás** gombra az elsődleges berendezésből származó szükséges konfigurációs fájlok beszerzéséhez.

1. Az **Importálás** gombra kattintva megnyílik egy előugró ablak, amely útmutatást ad a szükséges konfigurációs fájlok az elsődleges berendezésből való importálásához.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Konfigurációs modális importálása":::
1. Jelentkezzen be (távoli asztal) az elsődleges készülékre, és hajtsa végre a következő PowerShell-parancsokat:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Másolja a fenti parancsok futtatásával létrehozott zip-fájlt a kibővíthető készülékre. A zip-fájl tartalmazza a kibővíthető berendezés regisztrálásához szükséges konfigurációs fájlokat.
1. Az előző lépésben megnyitott előugró ablakban válassza ki a másolt konfigurációs zip-fájl helyét, majd kattintson a **Mentés** gombra.

A fájlok sikeres importálása után a kibővített berendezés regisztrálása befejeződik, és az utolsó sikeres importálás időbélyegét fogja megjeleníteni. A regisztráció részleteit a **részletek megtekintése** lehetőségre kattintva is megtekintheti.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="A képernyőfelvételen a kibővíthető berendezések regisztrálása Azure Migrate projekttel látható.":::

Ezen a ponton újra ellenőriznie kell, hogy a kibővíthető berendezés képes-e csatlakozni a vCenter-kiszolgálóhoz. Kattintson az **újraellenőrzés** gombra a kibővíthető berendezés vCenter Server kapcsolatának ellenőrzéséhez.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="A képernyőképen az érvényesíteni kívánt hitelesítő adatok és felderítési források láthatók.":::

> [!IMPORTANT]
> Ha szerkeszti a vCenter Server hitelesítő adatait az elsődleges készüléken, győződjön meg arról, hogy a konfigurációs fájlokat újra importálja a kibővíthető készülékre a legújabb konfiguráció beszerzéséhez és a folyamatban lévő replikálás folytatásához.<br/> Ha már nincs szüksége a kibővíthető készülékre, győződjön meg róla, hogy letiltja a kibővíthető készüléket. [**További információ**](./common-questions-appliance.md) arról, hogyan tilthatja le a kibővíthető készüléket, ha nincs rá szükség.

## <a name="replicate"></a>Replikálás

1. A kibővíthető berendezés regisztrálása után a Azure Migrate: kiszolgáló áttelepítése csempén kattintson a **replikálás** elemre.

2.  Kövesse a képernyőn megjelenő lépéseket a több virtuális gép replikálásának megkezdéséhez. 

A kibővíthető berendezéssel párhuzamosan replikálhatja a 500-es virtuális gépeket. A virtuális gépeket 200 kötegekben is áttelepítheti a Azure Portal használatával.

Azure Migrate kiszolgáló áttelepítési eszköze gondoskodik a virtuális gépek elosztásáról az elsődleges és a kibővíthető berendezés között a replikáláshoz. A replikáció befejezése után áttelepítheti a virtuális gépeket.

> [!TIP]
> Javasoljuk, hogy az optimális teljesítmény érdekében a virtuális gépeket a 200 kötegekben telepítse át, ha nagy számú virtuális gépet szeretne áttelepíteni.
  
## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a következőket:
- Kibővíthető berendezés konfigurálása
- Virtuális gépek replikálása kibővíthető készülék használatával


[További](./tutorial-migrate-vmware.md) információ a kiszolgálók Azure-ba való áttelepítéséről a Azure Migrate: Server Migration Tool használatával.