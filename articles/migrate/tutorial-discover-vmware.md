---
title: VMware-környezetben futó kiszolgálók felderítése Azure Migrate észleléssel és értékeléssel
description: Ismerje meg, hogyan derítheti fel a VMware környezetben futó helyszíni kiszolgálókat a Azure Migrate felderítési és értékelési eszközzel
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/17/2021
ms.custom: mvc
ms.openlocfilehash: 1a0ad751a216e8da772fd5fdc96a0dc67cb27d01
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109852"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Oktatóanyag: VMware környezetben futó kiszolgálók felderítése Azure Migrateokkal: felderítés és Értékelés

Az Azure-ba való Migrálás részeként felderítheti a helyszíni leltárt és munkaterheléseket.

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel a VMware környezetben futó kiszolgálókat Azure Migrate: felderítési és értékelési eszközzel, egy egyszerű Azure Migrate berendezés használatával. A készüléket a vCenter Server futtató kiszolgálóként helyezi üzembe, a kiszolgálók és a hozzájuk tartozó metaadatok, a kiszolgálókon futó alkalmazások, a kiszolgálói függőségek és a SQL Server példányok és adatbázisok folyamatos felderítése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Azure-fiókot.
> * Készítse elő a VMware-környezetet a felderítéshez.
> * Hozzon létre egy projektet.
> * Állítsa be az Azure Migrate készüléket.
> * A folyamatos felderítés elindítása.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges.  

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy ezek az előfeltételek teljesülnek.


**Követelmény** | **Részletek**
--- | ---
**vCenter Server/ESXi-gazdagép** | A 5,5, 6,0, 6,5 vagy 6,7 verziót futtató vCenter Serverra van szüksége.<br/><br/> A kiszolgálókat egy 5,5-es vagy újabb verziót futtató ESXi-gazdagépen kell üzemeltetni.<br/><br/> A vCenter Server engedélyezze a bejövő kapcsolatokat a 443-as TCP-porton, hogy a készülék képes legyen a konfiguráció és a teljesítmény metaadatainak gyűjtésére.<br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter Serverhoz. Ha a vCenter Server egy másik portot figyel, akkor módosíthatja a portot, amikor megadja a vCenter Server részleteit a készülék Configuration Managerben.<br/><br/> Győződjön meg arról, hogy az ESXi-gazdagépeken engedélyezve van a bejövő hozzáférés a 443-es TCP-porton a telepített alkalmazások és az ügynök nélküli függőségek elemzésének elvégzéséhez a kiszolgálókon.
**Berendezés** | vCenter Server erőforrásokra van szükség ahhoz, hogy kiszolgálót foglaljon le a Azure Migrate berendezéshez:<br/><br/> -32 GB RAM, 8 vCPU és körülbelül 80 GB lemezes tárterület.<br/><br/> – Egy külső virtuális kapcsoló és internet-hozzáférés a berendezés-kiszolgálón közvetlenül vagy proxyn keresztül.
**Kiszolgálók** | A konfiguráció és a teljesítmény metaadatainak felderítése érdekében a Windows és a Linux operációs rendszer összes verziója támogatott. <br/><br/> Az alkalmazások felderítéséhez a kiszolgálókon a Windows és a Linux operációs rendszer összes verziója támogatott. [Tekintse meg az ügynök](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) nélküli függőségek elemzéséhez támogatott operációsrendszer-verziókat.<br/><br/> A telepített alkalmazások és az ügynök nélküli függőségek elemzésének elvégzéséhez telepíteni és futtatni kell a VMware-eszközöket (a 10.2.0 később), és futnia kell a kiszolgálókon. A Windows-kiszolgálóknak telepítve kell lennie a PowerShell 2,0-es vagy újabb verziójával.<br/><br/> SQL Server példányok és adatbázisok felderítéséhez [tekintse meg a támogatott](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) SQL Server-verziók és-kiadások, a támogatott Windows operációsrendszer-verziók és-hitelesítési mechanizmusok című szöveget.

## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:

- Közreműködő vagy tulajdonosi engedélyek az Azure-előfizetésben
- Engedélyek a Azure Active Directory-(HRE-) alkalmazások regisztrálásához
- Tulajdonos vagy közreműködő, valamint felhasználói hozzáférés rendszergazdai engedélyei az Azure-előfizetésben az ügynök nélküli kiszolgáló áttelepítése során használt Key Vault létrehozásához

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások** területen válassza az **előfizetések** lehetőséget.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Az Azure-előfizetés kereséséhez használt keresőmező":::


2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben létre kíván hozni egy projektet.
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési** területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzáadás** gombra.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését":::
    
6. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez":::

1. A készülék regisztrálásához az Azure-fióknak rendelkeznie kell a **HRE-alkalmazások regisztrálásához szükséges engedélyekkel.**
1. A Azure Portal területen navigáljon a  >  **felhasználók**  >  **felhasználói beállításainak** Azure Active Directory.
1. A **felhasználói beállítások** területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése":::

9. Ha a "Alkalmazásregisztrációk" beállítások "nem" értékre van állítva, kérje meg a bérlőt/globális rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket. Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az **alkalmazás fejlesztői** szerepkörét egy fiókhoz, hogy engedélyezze a HRE-alkalmazás regisztrálását. [További információ](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>A VMware előkészítése

VCenter Server esetén győződjön meg arról, hogy a fiókja rendelkezik engedéllyel a virtuális gép létrehozásához PETESEJT-fájl használatával. Erre akkor van szükség, amikor a Azure Migrate készüléket VMware virtuális gépként telepíti, és egy PETESEJT-fájlt használ.

A Azure Migrate vCenter Server írásvédett fiókra van szüksége a VMware-környezetben futó kiszolgálók felderítéséhez és értékeléséhez. Ha a telepített alkalmazások és az ügynök nélküli függőségek elemzését is el szeretné végezni, akkor a fióknak **Virtual Machines > vendég műveleteihez** engedélyezett jogosultságokkal kell rendelkeznie.

### <a name="create-an-account-to-access-vcenter"></a>Fiók létrehozása a vCenter való hozzáféréshez

A vSphere webes ügyfélprogramban az alábbiak szerint állítson be egy fiókot:

1. Rendszergazdai jogosultságokkal rendelkező fiók használata a vSphere Web Client > válassza az **Adminisztráció** lehetőséget.
2. **Hozzáférés**, válassza az **SSO-felhasználók és-csoportok** lehetőséget.
3. A **felhasználók** területen adjon hozzá egy új felhasználót.
4. Az **új felhasználó** mezőbe írja be a fiók adatait. Ezután kattintson az **OK** gombra.
5. A **globális engedélyek** területen válassza ki a felhasználói fiókot, és rendelje hozzá a **csak olvasási** szerepkört a fiókhoz. Ezután kattintson az **OK** gombra.
6. Ha a telepített alkalmazások és az ügynök nélküli függőségek elemzését is el szeretné végezni, lépjen a **szerepkörök** > válassza ki a **csak olvasási** szerepkört, és a **jogosultságok** területen válassza a **vendég műveletek** lehetőséget. A jogosultságokat a vCenter Server alatt lévő összes objektumra propagálhatja a "propagálás gyermekeknek" jelölőnégyzet bejelölésével.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Jelölőnégyzet a vendég műveleteknek a csak olvasási szerepkörön való engedélyezéséhez":::


> [!NOTE]
> A felderítést korlátozhatja meghatározott vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, a gazdagépek mappájára vagy az egyes kiszolgálókra, a vCenter Server fiók hatókörével. [**További információ**](set-discovery-scope.md) a vCenter Server felhasználói fiók hatóköréről.

### <a name="create-an-account-to-access-servers"></a>Fiók létrehozása kiszolgálókhoz való hozzáféréshez

Szüksége van egy olyan felhasználói fiókra, amely rendelkezik a szükséges jogosultságokkal a kiszolgálókon a telepített alkalmazások felderítéséhez, az ügynök nélküli függőségek elemzéséhez, valamint SQL Server példányok és adatbázisok felderítéséhez. A felhasználói fiókot megadhatja a készülék Configuration Managerben. A készülék nem telepít ügynököket a kiszolgálókon.

1. Windows-kiszolgálók esetén hozzon létre egy fiókot (helyi vagy tartományi) rendszergazdai engedélyekkel a kiszolgálókon. SQL Server példányok és adatbázisok felderítéséhez a sysadmin (rendszergazda) kiszolgálói szerepkör tagjának kell lennie a Windows vagy SQL Server fióknak. [További információ](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) a szükséges szerepkör a felhasználói fiókhoz való hozzárendeléséről.
2. Linux-kiszolgálók esetében hozzon létre egy rendszergazdai jogosultságokkal rendelkező fiókot. Másik lehetőségként létrehozhat egy fiókot ezekkel az engedélyekkel a/bin/netstat-és/bin/ls-fájlokon: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.

> [!NOTE]
> Mostantól több kiszolgálói hitelesítő adatot is hozzáadhat a Configuration Managerben a telepített alkalmazások felderítéséhez, az ügynök nélküli függőségek elemzéséhez és a SQL Server példányok és adatbázisok felderítéséhez. Több tartomány/Windows (nem tartományi)/Linux (nem tartományi) és/vagy SQL Server hitelesítési hitelesítő adat is felvehető. [**További információ**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Projekt beállítása

Új projekt beállítása.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **projekt létrehozása** lehetőséget.
5. A **projekt létrehozása** lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="A projekt neve és a régió mezői":::

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg a projekt üzembe helyezése megtörténik. A **Azure Migrate: a felderítési és értékelési** eszközt alapértelmezés szerint az új projekthez adja hozzá a rendszer.

> [!NOTE]
> Ha már létrehozott egy projektet, ugyanezzel a projekttel regisztrálhat további készülékeket a felderítéshez és az értékeléshez. a kiszolgálók. [ **További információ**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>A készülék beállítása

Azure Migrate: a felderítés és az értékelés egy könnyű Azure Migrate berendezést használ. A készülék kiszolgáló-felderítést végez, és a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat küld Azure Migrate. A készülék egy olyan PETESEJT-sablon telepítésével állítható be, amely letölthető a projektből.

> [!NOTE]
> Ha valamilyen okból nem tudja beállítani a készüléket a sablon használatával, beállíthatja egy PowerShell-parancsfájl használatával egy meglévő Windows Server 2016-kiszolgálón. [**További információ**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Üzembe helyezés PETESEJTekkel

Ha a készüléket a következő PETESEJT-sablonnal szeretné beállítani:

1. Adja meg a készülék nevét, és állítson be egy Project-kulcsot a portálon.
1. Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba. Ellenőrizze, hogy a petesejtek biztonságosak-e.
1. Hozza létre a készüléket a petesejtek fájlból, és győződjön meg róla, hogy tud csatlakozni a Azure Migratehoz.
1. Konfigurálja a készüléket első alkalommal, és regisztrálja a projekttel a Project Key használatával.

### <a name="1-generate-the-project-key"></a>1. a projekt kulcsának előállítása

1. Az **áttelepítési céloknál** a  >  **Windows, a Linux és az SQL Server**  >  **Azure Migrate: felderítés és értékelés** **területen** válassza a felderítés lehetőséget.
2. A **felderítési kiszolgálók** a  >  **kiszolgálók virtualizáltak?** területen válassza **az igen, VMware vSphere Hypervisort**.
3. **1.: hozzon létre egy projektfájlt**, adja meg a VMware-környezetben található kiszolgálók felderítéséhez beállított Azure Migrate berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Ne zárjuk be a felderítés lapot az erőforrások létrehozása során.
1. Az Azure-erőforrások sikeres létrehozása után a rendszer létrehoz egy **Project-kulcsot** .
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

    - Azure Government esetén:
    
        **Algoritmus** | **Letöltés** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

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
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a berendezéshez, és nyissa meg a készülék Configuration Manager URL-címét: `https://appliance name or IP address: 44368` .

   Másik lehetőségként megnyithatja a Configuration Managert a berendezés-kiszolgáló asztaláról a Configuration Manager parancsikonjának kiválasztásával.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A Configuration Manager > **Előfeltételek beállítása** területen tegye a következőket:
   - **Kapcsolat**: a készülék ellenőrzi, hogy a kiszolgáló rendelkezik-e internet-hozzáféréssel. Ha a kiszolgáló proxyt használ:
     - A proxy címe vagy a figyelő port megadásához kattintson a **telepítő proxy** elemre `http://ProxyIPAddress` `http://ProxyFQDN` .
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Csak a HTTP-proxyk használata támogatott.
     - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről. Az ellenőrzések befejezése után kattintson a **berendezés megtekintése** lehetőségre a berendezés-kiszolgálón futó szolgáltatások állapotának és verzióinak megtekintéséhez.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a berendezés megadott helyére a **telepítési utasításokban** megadott módon.

     Azure Migrate kiszolgáló áttelepítése a VDDK használatával replikálja a kiszolgálókat az Azure-ba való áttelepítés során. 
1. Ha szeretné, bármikor újrafuttathatja az **előfeltételeket** a berendezés konfigurálása során annak ellenőrzéséhez, hogy a berendezés teljesíti-e az összes előfeltételt.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="1. panel a készülék Configuration Managerben":::

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a **projektből** a portálról másolt kulcsot. Ha nem rendelkezik a kulccsal, nyissa meg a **Azure Migrate: felderítés és értékelés> a meglévő készülékek felügyeletének észlelése>**, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
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
    - Ha a hatókör-felderítést meghatározott VMware-objektumokra (vCenter Server adatközpontok, fürtök, fürtök, gazdagépek, gazdagépek vagy különálló kiszolgálók mappára) szeretné használni, tekintse át az [ebben a cikkben](set-discovery-scope.md) szereplő utasításokat a Azure Migrate által használt fiók korlátozásához.
1. A **2. lépés: adja meg a vCenter Server részleteket** lehetőségnél kattintson a **felderítési forrás hozzáadása** elemre, hogy a legördülő listából válassza ki a hitelesítő adatok rövid nevét, adja meg a vCenter Server **IP-címét/teljes tartománynevét** . A **portot** meghagyhatja az alapértelmezett értékre (443), vagy megadhat egy egyéni portot, amelyen vCenter Server figyeli, és kattintson a **Save (Mentés**) gombra.
1. A **Save (Mentés**) gombra kattintva a készülék megpróbálja ellenőrizni a vCenter Serverhoz való kapcsolódást a megadott hitelesítő adatokkal, és megjeleníti az **ellenőrzési állapotot** a táblában a vCenter Server IP-cím/FQDN használatával.
1. A felderítés megkezdése előtt bármikor **újraérvényesítheti** vCenter Server a kapcsolatot.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3. panel a készülék Configuration Managerben vCenter Server részletekért":::

### <a name="provide-server-credentials"></a>Kiszolgáló hitelesítő adatainak megadása

A **3. lépés: adja meg a kiszolgálói hitelesítő adatokat a szoftverek leltározásához, az ügynök nélküli függőségek elemzéséhez és a SQL Server példányok és adatbázisok felderítéséhez**, dönthet úgy, hogy több kiszolgáló hitelesítő adatait is megadja, vagy ha nem szeretné használni ezeket a funkciókat, kihagyhatja a lépést, és folytathatja a vCenter Server felderítést. A szándékot később bármikor megváltoztathatja.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="3. panel a készülék Configuration Manager kiszolgáló adataihoz":::

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

     :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="3. panel a készülék Configuration Managerben több hitelesítő adat biztosításához":::

### <a name="start-discovery"></a>Felderítés indítása

1. Kattintson a **felderítés indítása** lehetőségre, hogy kirúgja vCenter Server felderítést. A felderítés sikeres elindítását követően a források táblában található vCenter Server IP-cím/FQDN elemnél tekintheti meg a felderítési állapotot.
1. Ha megadta a kiszolgálói hitelesítő adatokat, a szoftver leltározása (telepített alkalmazások felderítése) automatikusan megkezdődik a vCenter Server felderítése után. A szoftver leltározása 12 óránként történik.
1. A [szoftver leltár](how-to-discover-applications.md) azonosítja a kiszolgálókon futó SQL Server-példányokat, és az információkat használja, a készülék megpróbál csatlakozni a példányokhoz a Windows-hitelesítéssel vagy SQL Server a készüléken megadott hitelesítő adatokkal, és adatokat gyűjt SQL Server adatbázisokról és azok tulajdonságairól. Az SQL-felderítés 24 óránként egyszer történik.
1. A szoftver leltározása során a hozzáadott kiszolgálók hitelesítő adatai a kiszolgálókon lesznek megerősítve, és az ügynök nélküli függőségek elemzéséhez vannak érvényesítve. Engedélyezheti a kiszolgálók ügynök nélküli függőségi elemzését a portálon. Csak azok a kiszolgálók választhatók ki, amelyeken az érvényesítés sikeres, így az ügynök nélküli függőségek elemzése is engedélyezhető.

> [!Note]
>A Azure Migrate titkosítja Azure Migrate berendezés és a forrás SQL Server példányok közötti kommunikációt (a kapcsolat titkosítása tulajdonság értéke TRUE). Ezek a kapcsolatok a [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) -mel vannak titkosítva (igaz értékre állítva); az átviteli réteg az SSL használatával titkosítja a csatornát, és megkerüli a tanúsítványláncot a megbízhatóság ellenőrzéséhez. A berendezés-kiszolgálót úgy kell beállítani, hogy [**megbízzon a tanúsítvány legfelső szintű hitelesítésszolgáltatóján**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Ha nincs tanúsítvány kiépítve a kiszolgálón az indításkor, SQL Server létrehoz egy önaláírt tanúsítványt, amelyet a rendszer a bejelentkezési csomagok titkosítására használ. [**További információ**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

A felderítés a következőképpen működik:

- Körülbelül 15 percet vesz igénybe, hogy a felderített kiszolgálók leltározása megjelenjen a portálon.
- A telepített alkalmazások felderítése hosszabb időt is igénybe vehet. Az időtartam a felderített kiszolgálók számától függ. 500-kiszolgálók esetében körülbelül egy óra elteltével a felderített leltár megjelenik a Azure Migrate-portálon.
- A kiszolgálók felderítését követően a portálon engedélyezheti az ügynök nélküli függőségek elemzését a kiszolgálókon.
- SQL Server példányok és adatbázisok adatai a felderítés megkezdése után 24 órán belül megjelennek a portálon.

## <a name="next-steps"></a>Következő lépések

- [Kiszolgálók kiértékelése](./tutorial-assess-vmware-azure-vm.md) Azure-beli virtuális gépekre való áttelepítéshez.
- [SQL-kiszolgálók felmérése](./tutorial-assess-sql.md) az Azure SQL-re való áttelepítéshez.
- [Tekintse át a](migrate-appliance.md#collected-data---vmware) berendezés által a felderítés során gyűjtött adatokat.
