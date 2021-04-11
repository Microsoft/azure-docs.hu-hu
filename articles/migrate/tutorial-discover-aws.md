---
title: AWS-példányok észlelése Azure Migrate felderítéssel és értékeléssel
description: Ismerje meg, hogyan derítheti fel az AWS-példányokat Azure Migrate felderítéssel és értékeléssel.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 295cd5a6831cb64d146bb92bca74b82ff7ab29df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771481"
---
# <a name="tutorial-discover-aws-instances-with-azure-migrate-discovery-and-assessment"></a>Oktatóanyag: AWS-példányok felderítése Azure Migrateekkel: felderítés és Értékelés

Az Azure-ba való Migrálás részeként felderítheti a kiszolgálókat az értékeléshez és az áttelepítéshez.

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel Amazon Web Services (AWS) példányokat a Azure Migrate: felderítési és értékelési eszközzel, egy könnyű Azure Migrate berendezés használatával. A készüléket fizikai kiszolgálóként kell üzembe helyezni a gépek és a teljesítmény metaadatainak folyamatos felderítése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Azure-fiókot.
> * AWS-példányok előkészítése a felderítéshez.
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
**Berendezés** | Szüksége van egy EC2 virtuális gépre, amelyen futtatni szeretné a Azure Migrate készüléket. A gépnek a következőket kell tartalmaznia:<br/><br/> – A Windows Server 2016 telepítve van.<br/> _A készülék futtatása a Windows Server 2019 rendszerű gépen nem támogatott_.<br/><br/> -16 GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárterület, valamint egy külső virtuális kapcsoló.<br/><br/> -Statikus vagy dinamikus IP-cím, internet-hozzáféréssel, közvetlenül vagy proxyn keresztül.
**Windows-példányok** | Engedélyezze a bejövő kapcsolatokat a WinRM 5985-as porton (HTTP), hogy a készülék lekérje a konfiguráció és a teljesítmény metaadatait.
**Linux-példányok** | Bejövő kapcsolatok engedélyezése a 22-es porton (TCP).<br/><br/> A példányok `bash` alapértelmezett rendszerhéjként használhatók, ellenkező esetben a felderítés sikertelen lesz.

## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:

* Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
* Azure Active Directory-(HRE-) alkalmazások regisztrálásához szükséges engedélyek.

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások** területen válassza az **előfizetések** lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-aws/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben létre kíván hozni egy projektet.
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési** területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzáadás** gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-aws/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-aws/assign-role.png)

1. A készülék regisztrálásához az Azure-fióknak rendelkeznie kell a **HRE-alkalmazások regisztrálásához szükséges engedélyekkel.**
1. A Azure Portal területen navigáljon a  >  **felhasználók**  >  **felhasználói beállításainak** Azure Active Directory.
1. A **felhasználói beállítások** területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-aws/register-apps.png)

1. Ha a "Alkalmazásregisztrációk" beállítások "nem" értékre van állítva, kérje meg a bérlőt/globális rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket. Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az **alkalmazás fejlesztői** szerepkörét egy fiókhoz, hogy engedélyezze a HRE-alkalmazás regisztrálását. [További információk](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-aws-instances"></a>AWS-példányok előkészítése

Állítson be egy fiókot, amelyet a készülék használhat az AWS-példányok eléréséhez.

- **Windows-kiszolgálók** esetén állítson be egy helyi felhasználói fiókot a felderítésbe felvenni kívánt Windows-kiszolgálókon. Adja hozzá a felhasználói fiókot a következő csoportokhoz:-távfelügyeleti felhasználók – Teljesítményfigyelő felhasználók – Teljesítménynapló felhasználói.
 - **Linux-kiszolgálók** esetében a felderíteni kívánt Linux-kiszolgálókon rendszergazdai fiók szükséges. Alternatív megoldásként tekintse meg a [támogatási mátrix](migrate-support-matrix-physical.md#physical-server-requirements) utasításait.
- A Azure Migrate a jelszó-hitelesítést használja az AWS-példányok felfedése során. Az AWS-példányok alapértelmezés szerint nem támogatják a jelszó-hitelesítést. A példány felderítése előtt engedélyeznie kell a jelszó-hitelesítést.
    - Windows-kiszolgálók esetében engedélyezze a WinRM 5985-es portját (HTTP). Ez lehetővé teszi a távoli WMI-hívásokat.
    - Linux-kiszolgálók esetén:
        1. Jelentkezzen be az egyes Linux-gépekre.
        2. Nyissa meg a sshd_config fájlt: VI/etc/ssh/sshd_config
        3. A fájlban keresse meg a **PasswordAuthentication** sort, és módosítsa az értéket **Igen** értékre.
        4. Mentse a fájlt, és a bezáráshoz. Indítsa újra az SSH-szolgáltatást.
    - Ha a Linux-kiszolgálók felderítéséhez root felhasználót használ, győződjön meg arról, hogy a kiszolgálókon a rendszergazdai bejelentkezés engedélyezett.
        1. Bejelentkezés az egyes Linux rendszerű gépekre
        2. Nyissa meg a sshd_config fájlt: VI/etc/ssh/sshd_config
        3. A fájlban keresse meg a **PermitRootLogin** sort, és módosítsa az értéket **Igen** értékre.
        4. Mentse a fájlt, és a bezáráshoz. Indítsa újra az SSH-szolgáltatást.

## <a name="set-up-a-project"></a>Projekt beállítása

Új projekt beállítása.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **projekt létrehozása** lehetőséget.
5. A **projekt létrehozása** lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-aws/new-project.png)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg a projekt üzembe helyezése megtörténik. A **Azure Migrate: a felderítési és értékelési** eszközt alapértelmezés szerint az új projekthez adja hozzá a rendszer.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Ha már létrehozott egy projektet, ugyanezzel a projekttel regisztrálhat további készülékeket, és több kiszolgáló nem használható fel. [További információ](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>A készülék beállítása

A Azure Migrate készülék egy könnyű berendezés, amelyet Azure Migrate: a felderítés és az értékelés a következő műveleteket hajtja végre:

- Helyszíni kiszolgálók felderítése.
- Metaadatok és teljesítményadatok küldése a felderített kiszolgálókhoz Azure Migrate: felderítés és Értékelés.

[További](migrate-appliance.md) információ az Azure Migrate készülékről.

A készülék beállítása:

1. Adja meg a készülék nevét, és állítson be egy Project-kulcsot a portálon.
1. Töltse le a Azure Migrate telepítő parancsfájlt tartalmazó tömörített fájlt a Azure Portal.
1. Bontsa ki a tömörített fájl tartalmát. Indítsa el a PowerShell-konzolt rendszergazdai jogosultságokkal.
1. Futtassa a PowerShell-szkriptet a berendezés webalkalmazásának elindításához.
1. Konfigurálja a készüléket első alkalommal, és regisztrálja a projekttel a Project Key használatával.

### <a name="1-generate-the-project-key"></a>1. a projekt kulcsának előállítása

1. Az **áttelepítési céloknál** a  >  **Windows, a Linux és az SQL Server**  >  **Azure Migrate: felderítés és értékelés** **területen** válassza a felderítés lehetőséget.
2. A **felderítési kiszolgálók**  >  **kiszolgálók virtualizáltak?** területen válassza a **fizikai vagy egyéb (AWS, GCP, Xen stb.)** lehetőséget.
3. **1: a Project Key létrehozása** területen adja meg annak a Azure Migrate készüléknek a nevét, amelyet a fizikai vagy virtuális kiszolgálók felderítéséhez fog beállítani. A névnek legfeljebb 14 karakterből kell állnia.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárjuk be a felderítési kiszolgálók lapot.
1. Az Azure-erőforrások sikeres létrehozása után a rendszer létrehoz egy **Project-kulcsot** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="2-download-the-installer-script"></a>2. a telepítő parancsfájl letöltése

**2.: töltse le Azure Migrate készüléket**, és kattintson a **Letöltés** gombra.

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa a nyilvános felhő használatára: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Példa kormányzati felhő használatára: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:
    - Nyilvános felhő esetén:

        **Forgatókönyv** | **Letöltés** _ | _ *Kivonatoló érték**
        --- | --- | ---
        Fizikai (85 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Azure Government esetén:

        **Forgatókönyv** | **Letöltés** _ | _ *Kivonatoló érték**
        --- | --- | ---
        Fizikai (85 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Futtassa a Azure Migrate telepítő parancsfájlt
A telepítő parancsfájl a következő műveleteket végzi el:

- Ügynököket és webalkalmazásokat telepít a fizikai kiszolgálók felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [További információk](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a tömörített fájlt egy olyan mappába a kiszolgálón, amely a készüléket fogja üzemeltetni.  Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
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

### <a name="4-configure-the-appliance"></a>4. a berendezés konfigurálása

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

    * Azure Migrate támogatja az ssh-keygen parancs által generált SSH titkos kulcsot RSA, DSA, ECDSA és ed25519 algoritmusok használatával.
    * A Azure Migrate jelenleg nem támogatja a jelszó-alapú SSH-kulcsot. Egy SSH-kulcsot jelszó nélkül használhat.
    * Jelenleg Azure Migrate nem támogatja a PuTTY által generált SSH titkos kulcs fájlját.
    * Azure Migrate támogatja az SSH titkos kulcs fájljának OpenSSH formátumát az alábbiak szerint:
    
    ![A titkos SSH-kulcs támogatott formátuma](./media/tutorial-discover-physical/key-format.png)


1. Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat. A fizikai kiszolgálók felderítéséhez több hitelesítő adat is támogatott.
1. A **2. lépés: fizikai vagy virtuális kiszolgáló adatainak** megadása elemnél kattintson a **felderítési forrás hozzáadása** lehetőségre a kiszolgáló **IP-címének/teljes tartománynevének** és a kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítő adatok rövid nevének megadásához.
1. Egyszerre **egyetlen elemet is hozzáadhat** , vagy egy menetben **több elemet is hozzáadhat** . Lehetőség van arra is, hogy a kiszolgáló adatait a **CSV importálásával** adja meg.


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

- Az Azure-beli virtuális gépekre való Migrálás [fizikai kiszolgálóinak felmérése](tutorial-migrate-aws-virtual-machines.md) .
- [Tekintse át a](migrate-appliance.md#collected-data---physical) berendezés által a felderítés során gyűjtött adatokat.
