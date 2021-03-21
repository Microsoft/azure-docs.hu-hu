---
title: Azure Migrate készülék beállítása a VMware-hez
description: Ismerje meg, hogyan állíthat be egy Azure Migrate készüléket a VMware virtuális gépek felméréséhez és áttelepítéséhez.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: bac82b2939e5b6a674c75be2cd330dd0fa4b8487
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035812"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Készülék beállítása VMware virtuális gépekhez

Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Migrate készüléket a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool és az ügynök nélküli áttelepítés számára a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz használatával.

Az [Azure Migrate készülék](migrate-appliance.md) egy Azure Migrate által használt, könnyű berendezés: a kiszolgálók értékelése és a kiszolgálók áttelepítése az vCenter Server-ban futó kiszolgálók felderítésére, a kiszolgáló konfigurációjának és teljesítmény-metaadatainak küldésére az Azure-ba, valamint a kiszolgálók ügynök nélküli áttelepítéssel történő replikálásához.

A készüléket néhány módszerrel is üzembe helyezheti:

- Hozzon létre egy kiszolgálót a vCenter Server egy letöltött PETESEJT-sablon használatával. Ez a cikkben ismertetett módszer.
- Állítsa be a készüléket egy meglévő kiszolgálóra egy PowerShell-telepítő parancsfájl használatával. [Ezt a módszert](deploy-appliance-script.md) akkor kell használni, ha nem használhat petesejt-sablont, vagy ha Azure Government van.

A berendezés létrehozása után győződjön meg róla, hogy tud csatlakozni Azure Migrate: kiszolgáló értékeléséhez, regisztrálja a Azure Migrate projekttel, és konfigurálja a berendezést a felderítés elindításához.

## <a name="deploy-with-ova"></a>Üzembe helyezés PETESEJTekkel

Ha a készüléket a következő PETESEJT-sablonnal szeretné beállítani:
1. Adja meg a készülék nevét, és állítson be egy Azure Migrate Project-kulcsot a portálon.
1. Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba. Ellenőrizze, hogy a petesejtek biztonságosak-e.
1. Hozza létre a berendezés virtuális gépet a petesejtek fájlból, és győződjön meg arról, hogy tud kapcsolódni Azure Migratehoz.
1. Konfigurálja a készüléket első alkalommal, és regisztrálja azt a projektben a Azure Migrate Project Key használatával.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. a Azure Migrate projekt kulcsának előállítása

1. A **Migrálási célok** > **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** területen válassza a **Felderítés** lehetőséget.
2. A **felderítési gépeken** a  >  **gépek virtualizáltak?** területen válassza **az igen, VMware vSphere Hypervisort**.
3. **1.: hozzon létre Azure Migrate projektfájlt**, adjon meg egy nevet a Azure Migrate berendezés számára, amelyet a VMware-környezetben található kiszolgálók felderítéséhez fog beállítani. A névnek legfeljebb 14 karakterből kell állnia.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Ne zárja be a felderítés oldalt az erőforrások létrehozása során.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="2-download-the-ova-template"></a>2. a petesejtek sablon letöltése

**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. A petesejtek fájlt, majd kattintson a **Letöltés** gombra.

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. Nyisson meg egy rendszergazdai parancssorablakot azon a kiszolgálón, amelyre letöltötte a fájlt.
2. Futtassa a következő parancsot a PETESEJT-fájl kivonatának létrehozásához:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:

    - Az Azure nyilvános felhőben:
    
        **Algoritmus** | **Letöltés** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74


### <a name="3-create-the-appliance-server"></a>3. a berendezés-kiszolgáló létrehozása

Importálja a letöltött fájlt, és hozzon létre egy kiszolgálót a VMware-környezetben

1. A vSphere-ügyfél konzolján kattintson a **fájl**  >  **telepítése OVF sablon** elemre.
2. A OVF-sablon központi telepítése varázslóban > **forrás** mezőben határozza meg a petesejt-fájl helyét.
3. A **név** és **hely** mezőben adjon meg egy rövid nevet a kiszolgálónak. Válassza ki azt a leltár objektumot, amelyben a kiszolgálót üzemeltetni fogja.
5. A **gazdagép/fürt** területen adja meg azt a gazdagépet vagy fürtöt, amelyen a kiszolgáló futni fog.
6. A **tárterület** területen határozza meg a kiszolgáló tárolási célját.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **hálózati megfeleltetés** területen válassza ki azt a hálózatot, amelyhez a kiszolgáló csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége, hogy metaadatokat küldjön a Azure Migratenak.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a berendezés-kiszolgáló tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkhöz.


### <a name="4-configure-the-appliance"></a>4. a berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött petesejtek helyett [**PowerShell-parancsfájl**](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A vSphere-ügyfél konzolján kattintson a jobb gombbal a kiszolgálóra, majd válassza a **konzol megnyitása** lehetőséget.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozni tud a berendezés-kiszolgálóhoz, és nyissa meg a készülék Configuration Manager URL-címét: `https://appliance name or IP address: 44368` .

   Másik lehetőségként megnyithatja a Configuration Managert a berendezés-kiszolgáló asztaláról a Configuration Manager parancsikonjának kiválasztásával.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A Configuration Manager > **Előfeltételek beállítása** területen tegye a következőket:
   - **Kapcsolat**: a készülék ellenőrzi, hogy a kiszolgáló rendelkezik-e internet-hozzáféréssel. Ha a kiszolgáló proxyt használ:
     - A proxy **beállítása** elemre kattintva adja meg a proxy címeit az űrlapon `http://ProxyIPAddress` vagy a `http://ProxyFQDN` figyelési porton.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Csak a HTTP-proxyk használata támogatott.
     - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről. Az ellenőrzések befejezése után kattintson a **berendezés megtekintése** lehetőségre a berendezés-kiszolgálón futó szolgáltatások állapotának és verzióinak megtekintéséhez.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a berendezés megadott helyére a **telepítési utasításokban** megadott módon.

     Azure Migrate kiszolgáló áttelepítése a VDDK használatával replikálja a kiszolgálókat az Azure-ba való áttelepítés során. 
1. Ha szeretné, bármikor újrafuttathatja az **előfeltételeket** a berendezés konfigurálása során annak ellenőrzéséhez, hogy a berendezés teljesíti-e az összes előfeltételt.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="1. panel a készülék Configuration Managerben":::


## <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése** lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Szüksége lesz egy eszköz kódjára az Azure-beli hitelesítéshez. A **Bejelentkezés** gombra kattintva megnyílik egy modális az eszköz kódjával az alább látható módon.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Az eszköz kódját ábrázoló modális":::

1. Kattintson a **kód másolása & a bejelentkezés** elemre az eszköz kódjának másolásához és egy új böngésző lapon található Azure-beli bejelentkezési kérés megnyitásához. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon illessze be az eszköz kódját, és jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. Ha a bejelentkezés lapot véletlenül a bejelentkezés nélkül zárta be, frissítenie kell a készülék Configuration Manager böngésző lapját, hogy ismét engedélyezze a bejelentkezés gombot.
1. Miután sikeresen bejelentkezett, lépjen vissza az előző lapra a készülék Configuration Managerrel.
1. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő engedélyekkel a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése** lehetőségre kattintva megtekintheti a regisztráció részleteit.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="2. panel a készülék Configuration Managerben":::

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

### <a name="provide-vcenter-server-details"></a>vCenter Server részleteinek megadása

A készüléknek csatlakoznia kell a vCenter Serverhoz a kiszolgálók konfigurációjának és teljesítményének felderítéséhez.

1. Az **1. lépés: vCenter Server hitelesítő adatok** megadása lapon kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** ahhoz a vCenter Server-fiókhoz, amelyet a berendezés a vCenter Server futó kiszolgálók felderítéséhez használ majd.
    - A fenti cikkben leírtak szerint be kell állítania egy fiókot a szükséges engedélyekkel.
    - Ha a hatókör-felderítést meghatározott VMware-objektumokra (vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyéni virtuális gépekre) szeretné alkalmazni, tekintse át az [ebben a cikkben](set-discovery-scope.md) szereplő utasításokat a Azure Migrate által használt fiók korlátozásához.
1. A **2. lépés: adja meg a vCenter Server részleteket** lehetőségnél kattintson a **felderítési forrás hozzáadása** elemre, hogy a legördülő listából válassza ki a hitelesítő adatok rövid nevét, adja meg a vCenter Server **IP-címét/teljes tartománynevét** . A **portot** meghagyhatja az alapértelmezett értékre (443), vagy megadhat egy egyéni portot, amelyen vCenter Server figyeli, és kattintson a **Save (Mentés**) gombra.
1. A **Save (Mentés**) gombra kattintva a készülék megpróbálja ellenőrizni a vCenter Serverhoz való kapcsolódást a megadott hitelesítő adatokkal, és megjeleníti az **ellenőrzési állapotot** a táblában a vCenter Server IP-cím/FQDN használatával.
1. A felderítés elindítása előtt bármikor **újraérvényesítheti** vCenter Server a kapcsolatot.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3. panel a készülék Configuration Managerben vCenter Server részletekért":::

### <a name="provide-server-credentials"></a>Kiszolgáló hitelesítő adatainak megadása

A **3. lépés: adja meg a kiszolgálói hitelesítő adatokat a szoftverek leltározásához, az ügynök nélküli függőségek elemzéséhez és a SQL Server példányok és adatbázisok felderítéséhez**, dönthet úgy, hogy több kiszolgáló hitelesítő adatait is megadja, vagy ha nem szeretné használni ezeket a funkciókat, kihagyhatja a lépést, és folytathatja a vCenter Server felderítést. Később bármikor módosíthatja a szándékot.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="3. panel a készülék Configuration Manager kiszolgáló adataihoz":::

> [!Note]
> A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

Ha ezeket a funkciókat szeretné használni, az alábbi lépésekkel megadhatja a kiszolgálói hitelesítő adatokat. A készülék megkísérli automatikusan leképezni a hitelesítő adatokat a kiszolgálók számára a felderítési funkciók végrehajtásához.

- A **hitelesítő adatok hozzáadása** gombra kattintva adhat hozzá kiszolgálói hitelesítő adatokat. Ekkor megnyílik egy modális, ahol kiválaszthatja a **hitelesítő adatok típusát** a legördülő menüből.
- Megadhat tartomány/Windows (nem tartományi)/Linux (nem tartományi)/SQL Server hitelesítő adatokat. [További](add-server-credentials.md) információ a hitelesítő adatok megadásáról és kezeléséről.
- Minden hitelesítő adathoz meg kell adnia egy felhasználóbarát nevet a hitelesítő adatokhoz, hozzá kell adnia a **felhasználónevet** és a **jelszót** , majd kattintson a **Save (Mentés**) gombra.
- Ha a tartományi hitelesítő adatok lehetőséget választja, meg kell adnia a tartomány teljes tartománynevét is. A teljes tartománynév szükséges a hitelesítő adatok hitelességének ellenőrzéséhez az adott tartomány Active Directory.
- Tekintse át a fiókhoz [szükséges engedélyeket](add-server-credentials.md#required-permissions) a telepített alkalmazások felderítéséhez, az ügynök nélküli függőségek elemzéséhez vagy SQL Server példányok és adatbázisok felderítéséhez.
- Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat.
- Ha a **Mentés** vagy a **Hozzáadás** lehetőségre kattint, a készülék érvényesíti a tartomány hitelesítő adatait a tartomány Active Directory a hitelességük tekintetében. Ezzel elkerülhető, hogy a rendszer ne zárolja a fiókokat, amikor a berendezés több iterációt hajt végre a hitelesítő adatoknak a megfelelő kiszolgálókhoz való leképezéséhez.
- A hitelesítő adatok táblában megtekintheti az összes tartományi hitelesítő adat **érvényesítési állapotát** . Csak a tartományi hitelesítő adatok lesznek érvényesítve.
- Ha az ellenőrzés sikertelen, a **sikertelen** állapotra kattintva megtekintheti a hiba előfordulását, és a probléma kijavítása után kattintson a **hitelesítő adatok** újbóli ellenőrzése lehetőségre, hogy ismét érvényesítse a sikertelen tartományi hitelesítő adatokat.


### <a name="start-discovery"></a>Felderítés indítása

1. Kattintson a **felderítés indítása** lehetőségre, hogy kirúgja vCenter Server felderítést. A felderítés sikeres elindítását követően a források táblában található vCenter Server IP-cím/FQDN elemnél tekintheti meg a felderítési állapotot.
1. Ha megadta a kiszolgálói hitelesítő adatokat, a szoftver leltározása (telepített alkalmazások felderítése) automatikusan megkezdődik a vCenter Server felderítése után. A szoftver leltározása 12 óránként történik.
1. A [szoftver leltár](how-to-discover-applications.md) azonosítja a kiszolgálókon futó SQL Server-példányokat, és az információkat használja, a készülék megpróbál csatlakozni a példányokhoz a Windows-hitelesítéssel vagy SQL Server a készüléken megadott hitelesítő adatokkal, és adatokat gyűjt SQL Server adatbázisokról és azok tulajdonságairól. Az SQL-felderítés 24 óránként egyszer történik.
1. A szoftver leltározása során a hozzáadott kiszolgálók hitelesítő adatai a kiszolgálókon lesznek megerősítve, és az ügynök nélküli függőségek elemzéséhez vannak érvényesítve. Engedélyezheti a kiszolgálók ügynök nélküli függőségi elemzését a portálon. Csak azok a kiszolgálók választhatók ki, amelyeken az érvényesítés sikeres, így az ügynök nélküli függőségek elemzése is engedélyezhető.

A felderítés a következőképpen működik:
- Körülbelül 15 percet vesz igénybe, hogy a felderített kiszolgálók leltározása megjelenjen a portálon.
- A telepített alkalmazások felderítése hosszabb időt is igénybe vehet. Az időtartam a felderített kiszolgálók számától függ. 500-kiszolgálók esetében körülbelül egy óra elteltével a felderített leltár megjelenik a Azure Migrate-portálon.
- A kiszolgálók felderítését követően a portálon engedélyezheti az ügynök nélküli függőségek elemzését a kiszolgálókon.
- SQL Server példányok és adatbázisok adatai a felderítés megkezdése után 24 órán belül megjelennek a portálon.

## <a name="next-steps"></a>Következő lépések

Tekintse át a [VMware Assessment](./tutorial-assess-vmware-azure-vm.md) és az [ügynök nélküli áttelepítés](tutorial-migrate-vmware.md)oktatóanyagait.