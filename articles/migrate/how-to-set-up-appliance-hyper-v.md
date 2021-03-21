---
title: Azure Migrate berendezés beállítása a Hyper-V-hez
description: Ismerje meg, hogyan állíthat be egy Azure Migrate berendezést a Hyper-V virtuális gépek felméréséhez és áttelepítéséhez.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 3a7486ce94b335f835a88b7f357c9c719f86d667
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704758"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Berendezés beállítása Hyper-V virtuális gépekhez

Ennek a cikknek a segítségével beállíthatja a Azure Migrate berendezést a Hyper-V virtuális gépek kiértékeléséhez a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával.

A [Azure Migrate készülék](migrate-appliance.md)  egy Azure Migrate által használt, könnyű berendezés: a kiszolgáló értékelése/áttelepítése a helyszíni Hyper-V virtuális gépek felderítésére, valamint a VM-metaadatok/teljesítményadatok küldésére az Azure-ba.

A készüléket néhány módszerrel is üzembe helyezheti:

- A Hyper-V virtuális gépen a letöltött virtuális merevlemez használatával állítható be. Ez a cikkben ismertetett módszer.
- Állítsa be a Hyper-V virtuális gépre vagy fizikai gépre egy PowerShell-telepítő parancsfájl használatával. [Ezt a módszert](deploy-appliance-script.md) akkor kell használni, ha virtuális merevlemezt nem tud beállítani, vagy ha Azure Government.

A berendezés létrehozása után győződjön meg róla, hogy tud csatlakozni Azure Migrate: kiszolgáló-értékeléshez, először konfigurálja, majd regisztrálja azt a Azure Migrate projektben.

## <a name="appliance-deployment-vhd"></a>Berendezés üzembe helyezése (VHD)

A készülék beállítása VHD-sablon használatával:

- Adja meg a készülék nevét, és állítson be egy Azure Migrate Project-kulcsot a portálon.
- Töltsön le egy tömörített Hyper-V virtuális merevlemezt a Azure Portal.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel a Azure Migrate Project Key használatával.

### <a name="generate-the-azure-migrate-project-key"></a>A Azure Migrate projekt kulcsának előállítása

1. A **Migrálási célok** > **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** területen válassza a **Felderítés** lehetőséget.
2. A **felderítési gépek** a  >  **gépek virtualizáltak?** területen válassza **az igen, a Hyper-V** lehetőséget.
3. **1.: Azure Migrate projekt kulcsának létrehozásakor** adja meg a Hyper-V virtuális gépek felderítéséhez beállított Azure Migrate berendezés nevét. a névnek alfanumerikusnak kell lennie 14 karakternél vagy kevesebb értékkel.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárja be a gépek felderítése lapot.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="download-the-vhd"></a>A VHD letöltése

**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. VHD-fájl, majd kattintson a **Letöltés** gombra. 

   ![A felderítési gépek kiválasztása](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![A kulcs létrehozásának kiválasztása](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a virtuális merevlemez kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozza létre a virtuális gépet.

1. Bontsa ki a tömörített VHD-fájlt egy olyan mappába a Hyper-V-gazdagépen, amely a készülék virtuális gépét fogja üzemeltetni. Három mappa van kibontva.
2. Nyissa meg a Hyper-V kezelőjét. A **műveletek** területen kattintson a **virtuális gép importálása** elemre.

    ![VHD üzembe helyezése](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. A virtuális gép importálása varázslóban > a **Kezdés előtt** kattintson a **tovább** gombra.
3. A **mappában keresse** meg a kibontott virtuális merevlemezt tartalmazó mappát. Ezután kattintson a **Tovább** gombra.
1. A **virtuális gép kiválasztása lapon** kattintson a **tovább** gombra.
2. Az **importálási típus kiválasztása** területen kattintson **a virtuális gép másolása (új egyedi azonosító létrehozása)** elemre. Ezután kattintson a **Tovább** gombra.
3. A **cél kiválasztása** területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák** területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása** területen adja meg azt a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsolónak internetkapcsolattal kell rendelkeznie az Azure-ba való adatküldéshez.
6. Az **Összefoglalás** területen tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V kezelőjében > **Virtual Machines** indítsa el a virtuális gépet.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött VHD helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A Hyper-V kezelőjében > **Virtual Machines** kattintson a jobb gombbal a virtuális gépre > a **kapcsolat** elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása** lapon tegye a következőket:
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
      - Kattintson a **proxy beállítása** elemre, és adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
      - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
      - Csak a HTTP-proxyk használata támogatott.
      - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken az idő a virtuális gép felderítésének megfelelő működéséhez szinkronban kell lennie.
    - **Frissítések telepítése**: Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése** lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Szüksége lesz egy eszköz kódjára az Azure-beli hitelesítéshez. A **Bejelentkezés** gombra kattintva megnyílik egy modális az eszköz kódjával az alább látható módon.

    ![Az eszköz kódját ábrázoló modális](./media/tutorial-discover-vmware/device-code.png)

1. Kattintson a **kód másolása & a bejelentkezés** elemre az eszköz kódjának másolásához és egy új böngésző lapon található Azure-beli bejelentkezési kérés megnyitásához. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon illessze be az eszköz kódját, és jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. Ha a bejelentkezés lapot véletlenül a bejelentkezés nélkül zárta be, frissítenie kell a készülék Configuration Manager böngésző lapját, hogy ismét engedélyezze a bejelentkezés gombot.
1. Miután sikeresen bejelentkezett, lépjen vissza az előző lapra a készülék Configuration Managerrel.
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő [engedélyekkel](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése** lehetőségre kattintva megtekintheti a regisztráció részleteit.



### <a name="delegate-credentials-for-smb-vhds"></a>Az SMB virtuális merevlemezek hitelesítő adatainak delegálása

Ha virtuális merevlemezeket futtat az SMB-n, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez a készülékről:

1. Futtassa ezt a parancsot a készülék virtuális gépén. A HyperVHost1/HyperVHost2 például állomásnevek.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Ezt is megteheti a berendezés Helyicsoportházirend-szerkesztőján:
    - A **helyi számítógép-házirend**  >  **Számítógép konfigurációja** területen kattintson **Felügyeleti sablonok**  >  **rendszer**  >  **hitelesítő adatok delegálása** elemre.
    - Kattintson duplán a **új hitelesítő adatok delegálásának engedélyezése** lehetőségre, és válassza az **engedélyezve** lehetőséget.
    - A **Beállítások** területen kattintson a **Megjelenítés** elemre, és adja hozzá a listában felderíteni kívánt Hyper-V-gazdagépeket a **wsman/** előtagként.
    - A  **hitelesítő adatok delegálása** területen kattintson duplán a **friss hitelesítő adatok delegálása csak NTLM kiszolgálói hitelesítéssel** lehetőségre. Ismét adja hozzá a **wsman/** előtagként használni kívánt Hyper-V-gazdagépeket a listához.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a Hyper-V-gazdagépekhez vagy-fürtökhöz, és indítsa el a virtuális gépek felderítését.

1. Az **1. lépés: a Hyper-v gazdagép hitelesítő adatainak** megadása területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** egy olyan Hyper-V-gazdagéphez vagy-fürthöz, amelyet a berendezés a virtuális gépek felderítéséhez használ Kattintson a **Save (Mentés**) gombra.
1. Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat. A Hyper-V virtuális gépek felderítéséhez több hitelesítő adat is támogatott.
1. A **2. lépés: a Hyper-v-gazdagép/-fürt részleteinek** megadása elemnél kattintson a **felderítési forrás hozzáadása** lehetőségre a Hyper-v-gazdagép/ **-fürt IP-címének/teljes tartománynevének** , valamint a gazdagéphez/fürthöz való kapcsolódáshoz szükséges hitelesítő adatok rövid nevének
1. Egyszerre **egyetlen elemet is hozzáadhat** , vagy egy menetben **több elemet is hozzáadhat** . Emellett lehetőség van a Hyper-V-gazdagép/-fürt adatainak biztosítására is a **CSV importálásával**.

    ![A felderítési forrás hozzáadásának kijelölése](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Ha az **egyetlen elem hozzáadása** lehetőséget választja, meg kell adnia a hitelesítő adatok és a Hyper-V-gazdagép/fürt **IP-CÍMÉNEK/teljes tartománynevének** rövid nevét, majd kattintson a **Mentés** gombra.
    - Ha a **több elem hozzáadása** _(alapértelmezés szerint)_ lehetőséget választja, egyszerre több rekordot is hozzáadhat, ha a szövegmezőben a hitelesítő adatok rövid nevét adja meg a Hyper-V gazdagép/fürt **IP-címének/teljes tartománynevének** a megadásával. **Ellenőrizze** a hozzáadott rekordokat, és kattintson a **Save (Mentés**) gombra.
    - Ha a **CSV importálása** lehetőséget választja, letöltheti a CSV-sablonfájlt, feltöltheti a fájlt a Hyper-V gazdagép/fürt **IP-CÍMÉVEL/teljes tartománynevével** , valamint a hitelesítő adatok rövid nevét. Ezután importálja a fájlt a készülékbe, **ellenőrizze** a fájlban szereplő rekordokat, és kattintson a **Mentés** gombra.

1. A Save (Mentés) gombra kattintva a készülék megpróbálja ellenőrizni a kapcsolódást a hozzáadott Hyper-V-gazdagépekhez/-fürtökhöz, és megjeleníti a tábla **érvényesítési állapotát** az egyes gazdagépeken/fürtökön.
    - A sikeresen érvényesített gazdagépek/fürtök esetében további részleteket a saját IP-címére/teljes tartománynevére kattintva tekinthet meg.
    - Ha az érvényesítés sikertelen a gazdagépen, tekintse át a hibát, ha a tábla állapot oszlopában a **sikertelen érvényesítés** gombra kattint. Javítsa ki a problémát, és ismételje meg az érvényesítést.
    - Gazdagépek vagy fürtök eltávolításához kattintson a **Törlés** gombra.
    - Egy adott gazdagép nem távolítható el fürtből. Csak a teljes fürtöt távolíthatja el.
    - Hozzáadhat egy fürtöt, még akkor is, ha a fürt adott gazdagépével problémák léptek fel.
1. A felderítés megkezdése előtt bármikor **újraérvényesítheti** a gazdagépek/fürtök kapcsolatát.
1. Kattintson a **felderítés indítása** lehetőségre a virtuális gép felderítésének kikapcsolásához a sikeresen érvényesített gazdagépekről/fürtökről. A felderítés sikeres elindítása után megtekintheti a felderítési állapotot az egyes gazdagépeken/fürtökön a táblában.

Ez elindítja a felderítést. Gazdagépen körülbelül 2 percet vesz igénybe, hogy a felderített kiszolgálók metaadatai megjelenjenek a Azure Portalban.

## <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés befejezését követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultját.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók** darabszámát.


## <a name="next-steps"></a>Következő lépések

Próbálja ki a [Hyper-V értékelését](tutorial-assess-hyper-v.md) Azure Migrate kiszolgáló értékelésével.