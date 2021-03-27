---
title: A Hyper-V-kiszolgálók felderítése Azure Migrate észleléssel és értékeléssel
description: Ismerje meg, hogyan derítheti fel a helyszíni kiszolgálókat a Hyper-V-ben a Azure Migrate felderítési és értékelési eszközzel.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: f461778f988fafeacc480e100b00be7d4c165dfb
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612517"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Oktatóanyag: a Hyper-V-n futó kiszolgálók felderítése Azure Migrateekkel: felderítés és Értékelés

Az Azure-ba való Migrálás részeként felderítheti a helyszíni leltárt és munkaterheléseket.

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel a helyszíni kiszolgálókat a Hyper-V-gazdagépeken a Azure Migrate: felderítési és értékelési eszközzel, egy egyszerű Azure Migrate berendezés használatával. A készüléket a Hyper-V-gazdagépen lévő kiszolgálóként helyezi üzembe a gép és a teljesítmény metaadatainak folyamatos felderítése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure-fiók beállítása
> * Készítse elő a Hyper-V környezetet a felderítéshez.
> * Hozzon létre egy projektet.
> * Állítsa be az Azure Migrate készüléket.
> * A folyamatos felderítés elindítása.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb elérési útját mutatják be, és az alapértelmezett beállításokat használják.  

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy ezek az előfeltételek teljesülnek.

**Követelmény** | **Részletek**
--- | ---
**Hyper-V gazdagép** | Azok a Hyper-V-gazdagépek, amelyeken a kiszolgálók találhatók, önállóak vagy fürtben is.<br/><br/> A gazdagépen a Windows Server 2019, a Windows Server 2016 vagy a Windows Server 2012 R2 rendszernek kell futnia.<br/><br/> Ellenőrizze, hogy a bejövő kapcsolatok engedélyezve vannak-e a WinRM 5985-es portján (HTTP), hogy a készülék csatlakozni tud-e a lekérési kiszolgáló metaadatainak és teljesítményadatait a CIM (CIM) munkamenet használatával.
**Berendezések üzembe helyezése** | A Hyper-V-gazdagépnek erőforrásokra van szüksége ahhoz, hogy lefoglaljon egy kiszolgálót a készülék számára:<br/><br/> -16 GB RAM, 8 vCPU, és körülbelül 80 GB lemezes tárterület.<br/><br/> – Egy külső virtuális kapcsolót és internet-hozzáférést a készüléken közvetlenül vagy egy proxyn keresztül.
**Kiszolgálók** | A kiszolgálók bármely Windows-vagy Linux-alapú operációs rendszert futtatnak.

## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:
- Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
- Azure Active Directory-(HRE-) alkalmazások regisztrálásához szükséges engedélyek.

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások** területen válassza az **előfizetések** lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben létre kíván hozni egy projektet.
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési** területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzáadás** gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-hyper-v/assign-role.png)

1. A készülék regisztrálásához az Azure-fióknak rendelkeznie kell a **HRE-alkalmazások regisztrálásához szükséges engedélyekkel.**
1. A Azure Portal területen navigáljon a  >  **felhasználók**  >  **felhasználói beállításainak** Azure Active Directory.
1. A **felhasználói beállítások** területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-hyper-v/register-apps.png)

9. Ha a "Alkalmazásregisztrációk" beállítások "nem" értékre van állítva, kérje meg a bérlőt/globális rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket. Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az **alkalmazás fejlesztői** szerepkörét egy fiókhoz, hogy engedélyezze a HRE-alkalmazás regisztrálását. [További információ](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Hyper-V-gazdagépek előkészítése

A Hyper-V-gazdagépeket manuálisan vagy parancsfájl használatával is előkészítheti. Az előkészítési lépések a táblázatban vannak összegezve. A szkript ezeket automatikusan előkészíti.

**Lépés** | **Parancsfájl** | **Kézi**
--- | --- | ---
Gazdagépre vonatkozó követelmények ellenőrzése | Ellenőrzi, hogy a gazdagép a Hyper-V támogatott verzióját és a Hyper-V szerepkört futtatja-e.<br/><br/>Engedélyezi a WinRM szolgáltatást, és megnyitja a 5985 (HTTP) és a 5986 (HTTPS) portot a gazdagépen (a metaadat-gyűjteményhez szükséges). | A gazdagépen a Windows Server 2019, a Windows Server 2016 vagy a Windows Server 2012 R2 rendszernek kell futnia.<br/><br/> Ellenőrizze, hogy a bejövő kapcsolatok engedélyezve vannak-e a WinRM 5985-es portján (HTTP), hogy a készülék csatlakozni tud-e a lekérési kiszolgáló metaadatainak és teljesítményadatait a CIM (CIM) munkamenet használatával.<br/><br/> A parancsfájl jelenleg nem támogatott olyan gazdagépeken, amelyek nem angol nyelvű területi beállítással rendelkeznek.  
PowerShell-verzió ellenőrzése | Ellenőrzi, hogy a parancsfájlt egy támogatott PowerShell-verzión futtatja-e. | Győződjön meg arról, hogy a PowerShell 4,0-es vagy újabb verzióját futtatja a Hyper-V-gazdagépen.
Fiók létrehozása | Ellenőrzi, hogy rendelkezik-e a megfelelő engedélyekkel a Hyper-V-gazdagépen.<br/><br/> Lehetővé teszi, hogy a megfelelő engedélyekkel rendelkező helyi felhasználói fiókot hozzon létre. | 1. lehetőség: a Hyper-V gazdagéphez rendszergazdai hozzáféréssel rendelkező fiók előkészítése.<br/><br/> 2. lehetőség: helyi rendszergazdai fiók vagy tartományi rendszergazdai fiók előkészítése, és a fiók hozzáadása a következő csoportokhoz: távfelügyeleti felhasználók, Hyper-V-rendszergazdák és Teljesítményfigyelő felhasználók.
A PowerShell távelérésének engedélyezése | Engedélyezi a PowerShell távelérést a gazdagépen, így a Azure Migrate készülék PowerShell-parancsokat futtathat a gazdagépen egy WinRM-kapcsolaton keresztül. | A beállításhoz minden gazdagépen nyisson meg egy PowerShell-konzolt rendszergazdaként, és futtassa a következő parancsot: ``` powershell Enable-PSRemoting -force ```
A Hyper-V integrációs szolgáltatások beállítása | Ellenőrzi, hogy a Hyper-V integrációs szolgáltatások engedélyezve vannak-e a gazdagép által kezelt összes kiszolgálón. | [Engedélyezze a Hyper-V integrációs szolgáltatásokat](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) az egyes kiszolgálókon.<br/><br/> Ha a Windows Server 2003-et futtatja, [kövesse az alábbi utasításokat](prepare-windows-server-2003-migration.md).
Hitelesítő adatok delegálása, ha a kiszolgálói lemezek távoli SMB-megosztásokon találhatók | Hitelesítő adatok delegálása | A parancs futtatásával engedélyezheti a CredSSP számára a hitelesítő adatok delegálását az SMB-megosztásokat tartalmazó kiszolgálókat futtató gazdagépeken: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Ezt a parancsot távolról is futtathatja az összes Hyper-V-gazdagépen.<br/><br/> Ha új gazdagép-csomópontokat ad hozzá egy fürthöz, azt a rendszer automatikusan hozzáadja a felderítéshez, de manuálisan kell engedélyeznie a CredSSP.<br/><br/> A készülék beállításakor a CredSSP beállításával állíthatja be a [készüléket](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>A szkript futtatása

1. Töltse le a szkriptet a [Microsoft letöltőközpontból](https://aka.ms/migrate/script/hyperv). A parancsfájlt a Microsoft kriptográfiailag aláírja.
2. Ellenőrizze a parancsfájl integritását MD5 vagy SHA256 kivonatoló fájl használatával. A hashtag-értékek alább láthatók. Futtassa ezt a parancsot a szkript kivonatának létrehozásához:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Példa használata:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. A parancsfájl integritásának ellenőrzése után futtassa a parancsfájlt minden Hyper-V-gazdagépen a következő PowerShell-paranccsal emelt szintű engedélyekkel:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
A kivonatoló értékek a következők:

**Kivonat** |  **Érték**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Projekt beállítása

Új projekt beállítása.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **projekt létrehozása** lehetőséget.
5. A **projekt létrehozása** lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-hyper-v/new-project.png)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg a projekt üzembe helyezése megtörténik. A **Azure Migrate: a felderítési és értékelési** eszközt alapértelmezés szerint az új projekthez adja hozzá a rendszer.

![Az oldal Azure Migrate: alapértelmezés szerint hozzáadott felderítési és értékelési eszköz](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Ha már létrehozott egy projektet, ugyanezzel a projekttel regisztrálhat további készülékeket, és több kiszolgáló nem használható fel. [További információ](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>A készülék beállítása

A Azure Migrate egy egyszerűsített Azure Migrate berendezést használ. A készülék kiszolgáló-felderítést végez, és a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat küld Azure Migrate. A készülék a projektből letölthető VHD-fájl telepítésével állítható be.

> [!NOTE]
> Ha valamilyen okból nem tudja beállítani a készüléket a sablon használatával, beállíthatja egy PowerShell-parancsfájl használatával egy meglévő Windows Server 2016-kiszolgálón. [További információ](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

Ez az oktatóanyag a Hyper-V környezetben futó kiszolgálón állítja be a készüléket, a következőképpen:

1. Adja meg a készülék nevét, és állítson be egy Project-kulcsot a portálon.
1. Töltsön le egy tömörített Hyper-V virtuális merevlemezt a Azure Portal.
1. Hozza létre a készüléket, és győződjön meg arról, hogy tud csatlakozni Azure Migrate: felderítéshez és értékeléshez.
1. Konfigurálja a készüléket első alkalommal, és regisztrálja a projekttel a Project Key használatával.

### <a name="1-generate-the-project-key"></a>1. a projekt kulcsának előállítása

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: felderítés és értékelés**, majd **a felderítés** elemre.
2. A **felderítési kiszolgálók** a  >  **kiszolgálók virtualizáltak?** területen válassza **az igen, a Hyper-V** lehetőséget.
3. **1.: hozzon létre egy projektfájlt**, adja meg a kiszolgálók felderítéséhez beállítani kívánt Azure Migrate berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Ne zárja be a felderítési kiszolgáló oldalt az erőforrások létrehozásakor.
1. Az Azure-erőforrások sikeres létrehozása után a rendszer létrehoz egy **Project-kulcsot** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="2-download-the-vhd"></a>2. a VHD letöltése

**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. VHD-fájl, majd kattintson a **Letöltés** gombra.

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő PowerShell-parancsot a ZIP-fájl kivonatának létrehozásához
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:

    - Az Azure nyilvános felhőben:

        **Forgatókönyv** | **Letöltés** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - Azure Government esetén:

        **Példa** _ | _ *Letöltés** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. berendezés létrehozása

Importálja a letöltött fájlt, és hozzon létre egy készüléket.

1. Bontsa ki a tömörített VHD-fájlt egy olyan mappába a Hyper-V-gazdagépen, amely a készüléket fogja üzemeltetni. Három mappa van kibontva.
2. Nyissa meg a Hyper-V kezelőjét. A **műveletek** területen kattintson a **virtuális gép importálása** elemre.
2. A virtuális gép importálása varázslóban > a **Kezdés előtt** kattintson a **tovább** gombra.
3. A **mappában keresse** meg a kibontott virtuális merevlemezt tartalmazó mappát. Ezután kattintson a **Tovább** gombra.
1. A **virtuális gép kiválasztása lapon** kattintson a **tovább** gombra.
2. Az **importálási típus kiválasztása** területen kattintson **a virtuális gép másolása (új egyedi azonosító létrehozása)** elemre. Ezután kattintson a **Tovább** gombra.
3. A **cél kiválasztása** területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák** területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása** területen adja meg azt a virtuális kapcsolót, amelyet a berendezés használni fog. A kapcsolónak internetkapcsolattal kell rendelkeznie az Azure-ba való adatküldéshez.
6. Az **Összefoglalás** területen tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V kezelőjében > **Virtual Machines** indítsa el a készüléket.

### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkhöz.

### <a name="4-configure-the-appliance"></a>4. a berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött VHD helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A Hyper-V kezelőjében > **Virtual Machines** kattintson a jobb gombbal a készülékre > a **kapcsolat** elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a berendezéshez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása** lapon tegye a következőket:
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a készülék rendelkezik-e internet-hozzáféréssel. Ha a készülék proxyt használ:
      - Kattintson a **telepítési proxy** elemre, és határozza meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
      - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
      - Csak a HTTP-proxyk használata támogatott.
      - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken a kiszolgáló felderítésének megfelelő működéséhez az idő szinkronizálása szükséges.
    - **Frissítések telepítése**: Azure Migrate: a felderítés és az értékelés ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a **projektből** a portálról másolt kulcsot. Ha nem rendelkezik a kulccsal, nyissa meg a **Azure Migrate: felderítés és értékelés> a meglévő készülékek felügyeletének észlelése>**, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Szüksége lesz egy eszköz kódjára az Azure-beli hitelesítéshez. A **Bejelentkezés** gombra kattintva megnyílik egy modális az eszköz kódjával az alább látható módon.

    ![Az eszköz kódját ábrázoló modális](./media/tutorial-discover-vmware/device-code.png)

1. Kattintson a **kód másolása & a bejelentkezés** elemre az eszköz kódjának másolásához és egy új böngésző lapon található Azure-beli bejelentkezési kérés megnyitásához. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon illessze be az eszköz kódját, és jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. Ha a bejelentkezés lapot véletlenül a bejelentkezés nélkül zárta be, frissítenie kell a készülék Configuration Manager böngésző lapját, hogy ismét engedélyezze a bejelentkezés gombot.
1. Miután sikeresen bejelentkezett, lépjen vissza az előző lapra a készülék Configuration Managerrel.
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő engedélyekkel a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése** lehetőségre kattintva megtekintheti a regisztráció részleteit.

### <a name="delegate-credentials-for-smb-vhds"></a>Az SMB virtuális merevlemezek hitelesítő adatainak delegálása

Ha virtuális merevlemezeket futtat az SMB-n, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez a készülékről:

1. Futtassa ezt a parancsot a készüléken. A HyperVHost1/HyperVHost2 például állomásnevek.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Ezt is megteheti a berendezés Helyicsoportházirend-szerkesztőján:
    - A **helyi számítógép-házirend**  >  **Számítógép konfigurációja** területen kattintson **Felügyeleti sablonok**  >  **rendszer**  >  **hitelesítő adatok delegálása** elemre.
    - Kattintson duplán a **új hitelesítő adatok delegálásának engedélyezése** lehetőségre, és válassza az **engedélyezve** lehetőséget.
    - A **Beállítások** területen kattintson a **Megjelenítés** elemre, és adja hozzá a listában felderíteni kívánt Hyper-V-gazdagépeket a **wsman/** előtagként.
    - A  **hitelesítő adatok delegálása** területen kattintson duplán a **friss hitelesítő adatok delegálása csak NTLM kiszolgálói hitelesítéssel** lehetőségre. Ismét adja hozzá a **wsman/** előtagként használni kívánt Hyper-V-gazdagépeket a listához.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a Hyper-V-gazdagépekhez vagy-fürtökhöz, és indítsa el a kiszolgáló felderítését.

1. Az **1. lépés: a Hyper-v gazdagép hitelesítő adatainak** megadása területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** egy olyan Hyper-V-gazdagéphez vagy-fürthöz, amelyet a berendezés a kiszolgálók felderítéséhez használ. Kattintson a **Save (Mentés**) gombra.
1. Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat. A Hyper-V környezetben található kiszolgálók felderítéséhez több hitelesítő adat is támogatott.
1. A **2. lépés: a Hyper-v-gazdagép/-fürt részleteinek** megadása elemnél kattintson a **felderítési forrás hozzáadása** lehetőségre a Hyper-v-gazdagép/ **-fürt IP-címének/teljes tartománynevének** , valamint a gazdagéphez/fürthöz való kapcsolódáshoz szükséges hitelesítő adatok rövid nevének
1. Egyszerre **egyetlen elemet is hozzáadhat** , vagy egy menetben **több elemet is hozzáadhat** . Emellett lehetőség van a Hyper-V-gazdagép/-fürt adatainak biztosítására is a **CSV importálásával**.

    - Ha az **egyetlen elem hozzáadása** lehetőséget választja, meg kell adnia a hitelesítő adatok és a Hyper-V-gazdagép/fürt **IP-CÍMÉNEK/teljes tartománynevének** rövid nevét, majd kattintson a **Mentés** gombra.
    - Ha a **több elem hozzáadása** _(alapértelmezés szerint)_ lehetőséget választja, egyszerre több rekordot is hozzáadhat, ha a szövegmezőben a hitelesítő adatok rövid nevét adja meg a Hyper-V gazdagép/fürt **IP-címének/teljes tartománynevének** a megadásával. **Ellenőrizze** a hozzáadott rekordokat, és kattintson a **Save (Mentés**) gombra.
    - Ha a **CSV importálása** lehetőséget választja, letöltheti a CSV-sablonfájlt, feltöltheti a fájlt a Hyper-V gazdagép/fürt **IP-CÍMÉVEL/teljes tartománynevével** , valamint a hitelesítő adatok rövid nevét. Ezután importálja a fájlt a készülékbe, **ellenőrizze** a fájlban szereplő rekordokat, és kattintson a **Mentés** gombra.

1. A Save (Mentés) gombra kattintva a készülék megpróbálja ellenőrizni a kapcsolódást a hozzáadott Hyper-V-gazdagépekhez/-fürtökhöz, és megjeleníti a tábla **érvényesítési állapotát** az egyes gazdagépeken/fürtökön.
    - A sikeresen érvényesített gazdagépek/fürtök esetében további részleteket a saját IP-címére/teljes tartománynevére kattintva tekinthet meg.
    - Ha az érvényesítés sikertelen a gazdagépen, tekintse át a hibát, ha a tábla állapot oszlopában a **sikertelen érvényesítés** gombra kattint. Javítsa ki a problémát, és ismételje meg az érvényesítést.
    - Gazdagépek vagy fürtök eltávolításához kattintson a **Törlés** gombra.
    - Egy adott gazdagép nem távolítható el fürtből. Csak a teljes fürtöt távolíthatja el.
    - Hozzáadhat egy fürtöt, még akkor is, ha a fürt adott gazdagépével problémák léptek fel.
1. A felderítés megkezdése előtt bármikor **újraérvényesítheti** a gazdagépekhez és fürtökhöz való kapcsolódást.
1. Kattintson a **felderítés indítása** lehetőségre a kiszolgálók felderítésének kikapcsolásához a sikeresen érvényesített gazdagépekről/fürtökről. A felderítés sikeres elindítása után megtekintheti a felderítési állapotot az egyes gazdagépeken/fürtökön a táblában.

Ez elindítja a felderítést. Gazdagépen körülbelül 2 percet vesz igénybe, hogy a felderített kiszolgálók metaadatai megjelenjenek a Azure Portalban.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítés befejeződése után ellenőrizheti, hogy a kiszolgálók megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultját.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: felderítés és értékelés** lapon kattintson a **felderített kiszolgálók** darabszámát megjelenítő ikonra.

## <a name="next-steps"></a>Következő lépések

- A [Hyper-V környezetben található kiszolgálók értékelése](tutorial-assess-hyper-v.md) az Azure-beli virtuális gépekre való áttelepítéshez.
- [Tekintse át a](migrate-appliance.md#collected-data---hyper-v) berendezés által a felderítés során gyűjtött adatokat.