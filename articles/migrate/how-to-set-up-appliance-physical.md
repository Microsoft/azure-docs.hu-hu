---
title: Azure Migrate berendezés beállítása fizikai kiszolgálókhoz
description: Megtudhatja, hogyan állíthat be Azure Migrate készüléket a fizikai kiszolgálók felderítéséhez és értékeléséhez.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 9052cbd3dc728b50b62c33f3a11a5e36a7504f29
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771566"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Berendezés beállítása fizikai kiszolgálókhoz

Ez a cikk bemutatja, hogyan állíthatja be a Azure Migrate berendezést, ha a fizikai kiszolgálókat a Azure Migrate: Discovery and Assessment Tool eszközzel értékeli.

A Azure Migrate készülék egy könnyű berendezés, amelyet Azure Migrate: a felderítés és az értékelés a következő műveleteket hajtja végre:

- Helyszíni kiszolgálók felderítése.
- Metaadatok és teljesítményadatok küldése a felderített kiszolgálókhoz Azure Migrate: felderítés és Értékelés.

[További](migrate-appliance.md) információ az Azure Migrate készülékről.


## <a name="appliance-deployment-steps"></a>Berendezések üzembe helyezésének lépései

A készülék beállítása:

- Adja meg a készülék nevét, és állítson be egy Project-kulcsot a portálon.
- Töltse le a Azure Migrate telepítő parancsfájlt tartalmazó tömörített fájlt a Azure Portal.
- Bontsa ki a tömörített fájl tartalmát. Indítsa el a PowerShell-konzolt rendszergazdai jogosultságokkal.
- Futtassa a PowerShell-szkriptet a berendezés webalkalmazásának elindításához.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a projekttel a Project Key használatával.

### <a name="generate-the-project-key"></a>A projekt kulcsának előállítása

1. Az **áttelepítési céloknál** a  >  **Windows, a Linux és az SQL Server**  >  **Azure Migrate: felderítés és értékelés** **területen** válassza a felderítés lehetőséget.
2. A **felderítési kiszolgálók**  >  **kiszolgálók virtualizáltak?** területen válassza a **fizikai vagy egyéb (AWS, GCP, Xen stb.)** lehetőséget.
3. **1: a Project Key létrehozása** területen adja meg annak a Azure Migrate készüléknek a nevét, amelyet a fizikai vagy virtuális kiszolgálók felderítéséhez fog beállítani. A névnek legfeljebb 14 karakterből kell állnia.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárjuk be a felderítési kiszolgálók lapot.
1. Az Azure-erőforrások sikeres létrehozása után a rendszer létrehoz egy **Project-kulcsot** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="download-the-installer-script"></a>A telepítő parancsfájl letöltése

**2.: töltse le Azure Migrate készüléket**, és kattintson a **Letöltés** gombra.

   ![A felderítési gépek kiválasztása](./media/tutorial-assess-physical/servers-discover.png)


   ![A kulcs létrehozásának kiválasztása](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. Nyisson meg egy rendszergazdai parancssorablakot azon kiszolgálókon, amelyeken letöltötte a fájlt.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa a nyilvános felhő használatára: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Példa kormányzati felhő használatára: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Ellenőrizze a készülék legújabb verzióját és a [kivonatoló értékek](tutorial-discover-physical.md#verify-security) beállításait.
 

## <a name="run-the-azure-migrate-installer-script"></a>A Azure Migrate telepítő parancsfájl futtatása
A telepítő parancsfájl a következő műveleteket végzi el:

- Ügynököket és webalkalmazásokat telepít a fizikai kiszolgálók felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a tömörített fájlt egy olyan mappába a kiszolgálón, amely a készüléket fogja üzemeltetni.  Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezéssel rendelkező kiszolgálón.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell könyvtárat arra a mappára, ahol a rendszer kibontotta a tartalmat a letöltött tömörített fájlból.
4. Futtassa a **AzureMigrateInstaller.ps1** nevű szkriptet a következő parancs futtatásával:

    - Nyilvános felhő esetén: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Azure Government esetén: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    A szkript a készülék webalkalmazásának sikeres befejeződése után elindítja a készüléket.

Ha bármilyen probléma merül fel, a parancsfájl-naplókat a C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log naplófájlban érheti el a hibaelhárításhoz.



### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkhöz.

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a berendezéshez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást az asztalról az alkalmazás parancsikonra kattintva.
2. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása** lapon tegye a következőket:
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a kiszolgáló rendelkezik-e internet-hozzáféréssel. Ha a kiszolgáló proxyt használ:
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
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő [engedélyekkel](./tutorial-discover-physical.md) a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése** lehetőségre kattintva megtekintheti a regisztráció részleteit.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Most kapcsolódjon a készülékről a felderíteni kívánt fizikai kiszolgálókhoz, és indítsa el a felderítést.

1. Az **1. lépés: hitelesítő adatok megadása a Windows-és Linux-alapú fizikai vagy virtuális kiszolgálók felderítéséhez** kattintson a **hitelesítő adatok hozzáadása** lehetőségre.
1. Windows Server esetén válassza ki a forrás típusát **Windows Serverként**, adjon meg egy rövid nevet a hitelesítő adatokhoz, adja hozzá a felhasználónevet és a jelszót. Kattintson a **Save (Mentés**) gombra.
1. Ha a Linux Serverhez jelszó-alapú hitelesítést használ, válassza ki a forrás típusát **Linux-kiszolgálóként (jelszó-alapú)**, adjon meg egy felhasználóbarát nevet a hitelesítő adatokhoz, adja hozzá a felhasználónevet és a jelszót. Kattintson a **Save (Mentés**) gombra.
1. Ha SSH-kulcson alapuló hitelesítést használ a Linux Serverhez, a forrás típusaként válassza a **Linux-kiszolgáló (SSH-kulcs-alapú)** lehetőséget, adjon meg egy felhasználóbarát nevet a hitelesítő adatokhoz, adja hozzá a felhasználónevet, keresse meg és válassza ki az SSH titkos kulcsot tartalmazó fájlt. Kattintson a **Save (Mentés**) gombra.

    - Azure Migrate támogatja az ssh-keygen parancs által generált SSH titkos kulcsot RSA, DSA, ECDSA és ed25519 algoritmusok használatával.
    - A Azure Migrate jelenleg nem támogatja a jelszó-alapú SSH-kulcsot. Egy SSH-kulcsot jelszó nélkül használhat.
    - Jelenleg Azure Migrate nem támogatja a PuTTY által generált SSH titkos kulcs fájlját.
    - Azure Migrate támogatja az SSH titkos kulcs fájljának OpenSSH formátumát az alábbiak szerint:
    
    ![A titkos SSH-kulcs támogatott formátuma](./media/tutorial-discover-physical/key-format.png)
1. Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat. A fizikai kiszolgálók felderítéséhez több hitelesítő adat is támogatott.
1. A **2. lépés: fizikai vagy virtuális kiszolgáló adatainak** megadása elemnél kattintson a **felderítési forrás hozzáadása** lehetőségre a kiszolgáló **IP-címének/teljes tartománynevének** és a kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítő adatok rövid nevének megadásához.
1. Egyszerre **egyetlen elemet is hozzáadhat** , vagy egy menetben **több elemet is hozzáadhat** . Lehetőség van arra is, hogy a kiszolgáló adatait a **CSV importálásával** adja meg.

    ![A felderítési forrás hozzáadásának kijelölése](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Ha az **egyetlen elem hozzáadása** lehetőséget választja, kiválaszthatja az operációs rendszer típusát, megadhatja a hitelesítő adatok rövid nevét, a kiszolgáló **IP-címét vagy teljes tartománynevét** , majd kattintson a **Mentés** gombra.
    - Ha úgy dönt, hogy **több elemet ad hozzá**, egyszerre több rekordot is hozzáadhat, ha a szövegmezőben a hitelesítő adatok rövid nevét adja meg a kiszolgáló **IP-címe/teljes tartományneve** mezőben. Ellenőrizze * * a felvett rekordokat, és kattintson a **Save (Mentés**) gombra.
    - Ha a **CSV importálása** _(alapértelmezés szerint kiválasztva)_ lehetőséget választja, letöltheti a CSV-sablonfájl fájlját, feltöltheti a fájlt a kiszolgáló **IP-címével/teljes tartománynevével** , valamint a hitelesítő adatok rövid nevét. Ezután importálja a fájlt a készülékbe, **ellenőrizze** a fájlban szereplő rekordokat, és kattintson a **Mentés** gombra.

1. A Save (Mentés) gombra kattintva a készülék megpróbálhatja érvényesíteni a hozzáadott kiszolgálókhoz való kapcsolódást, és megjeleníti az **ellenőrzési állapotot** a táblában az egyes kiszolgálókon.
    - Ha egy kiszolgáló érvényesítése meghiúsul, tekintse át a hibát, ha a tábla állapot oszlopában a **sikertelen érvényesítés** gombra kattint. Javítsa ki a problémát, és ismételje meg az érvényesítést.
    - Kiszolgáló eltávolításához kattintson a **Törlés** gombra.
1. A felderítés megkezdése előtt bármikor **újraérvényesítheti** a kiszolgálókhoz való kapcsolódást.
1. Kattintson a **felderítés indítása** gombra a sikeresen érvényesített kiszolgálók felderítésének elindításához. A felderítés sikeres elindítása után megtekintheti a felderítési állapotot a tábla minden egyes kiszolgálóján.


Ez elindítja a felderítést. Kiszolgálónként körülbelül 2 percet vesz igénybe, hogy a felderített kiszolgáló metaadatai megjelenjenek a Azure Portalban.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítés befejeződése után ellenőrizheti, hogy a kiszolgálók megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultját.
2. **Azure Migrate-Windows, Linux és SQL Server**  >  **Azure Migrate: felderítés és értékelés** lapon kattintson a **felderített kiszolgálók** számának megjelenítésére szolgáló ikonra.


## <a name="next-steps"></a>Következő lépések

Próbálja ki a Azure Migrate: Discovery and Assessment [fizikai kiszolgálók értékelését](tutorial-assess-physical.md) .