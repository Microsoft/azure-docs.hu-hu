---
title: Azure-alkalmazás tárolókra bontás Java; Java-webalkalmazások tárolókra bontás és migrálása az Azure Kubernetes-be.
description: 'Oktatóanyag: a tárolóba helyezése & Java-webalkalmazásokat telepít át az Azure Kubernetes szolgáltatásba.'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: ea7cdfbd30cf698cecbb14a1d70916764ad3247a
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023112"
---
# <a name="java-web-app-containerization-and-migration-to-azure-kubernetes-service"></a>Java-webalkalmazások tárolókra bontás és migrálása az Azure Kubernetes Service-be

Ebből a cikkből megtudhatja, hogyan tárolóba helyezése Java-webalkalmazásokat (az Apache Tomcat használatával), és hogyan telepítheti át azokat az [Azure Kubernetes szolgáltatásba (ak)](https://azure.microsoft.com/services/kubernetes-service/) a Azure Migrate: app tárolókra bontás eszközzel. A tárolókra bontás folyamat nem igényli a kód elérését, és egyszerű módszert kínál a meglévő alkalmazások tárolóba helyezése. Az eszköz a kiszolgálókon futó alkalmazások futtatási állapotának használatával határozza meg az alkalmazás-összetevőket, és segít a tárolók rendszerképében való csomagolásban. A tároló alkalmazás ezután üzembe helyezhető az Azure Kubernetes szolgáltatásban (ak).

A Azure Migrate: az App tárolókra bontás eszköz jelenleg támogatja a-

- Containerizing ASP.NET az alkalmazásokat, és üzembe helyezheti őket Windows-tárolókban az AK-ban. [További információ](./tutorial-containerize-aspnet-kubernetes.md)
- Containerizing Java-Web Apps az Apache Tomcat-on (Linux-kiszolgálókon), és üzembe helyezheti őket Linux-tárolókban az AK-ban.


A Azure Migrate: az App tárolókra bontás eszköz segítségével

- Az **alkalmazás felderítése**: az eszköz távolról csatlakozik a Java-webalkalmazást futtató alkalmazás-kiszolgálókhoz (az Apache Tomcat-on fut), és felderíti az alkalmazás összetevőit. Az eszköz létrehoz egy Docker, amely használatával létrehozható egy tároló-rendszerkép az alkalmazáshoz.
- **A tároló rendszerképének összeállítása**: a Docker megvizsgálhatja és testreszabhatja az alkalmazás követelményeinek megfelelően, és felhasználhatja az alkalmazás-tároló rendszerképének összeállításához. Az alkalmazás-tároló képe egy megadott Azure Container Registry van leküldve.
- **Üzembe helyezés az Azure Kubernetes szolgáltatásban**: az eszköz ezután létrehozza a Kubernetes erőforrás-definíciós YAML-fájlokat, amelyek szükségesek a tároló alkalmazás üzembe helyezéséhez az Azure Kubernetes Service-fürtön. Testreszabhatja a YAML-fájlokat, és felhasználhatja őket az alkalmazás üzembe helyezéséhez az AK-ban.

> [!NOTE]
> A Azure Migrate: az App tárolókra bontás eszköz segítségével megtalálhatja a konkrét alkalmazások típusait (ASP.NET és Java-webalkalmazások az Apache Tomcat-on) és az alkalmazás-kiszolgálón lévő összetevőket. A kiszolgálók felderítéséhez, valamint a helyszíni gépeken futó alkalmazások, szerepkörök és szolgáltatások leltározásához használja a Azure Migrate: Discovery and Assessment képességet. [További információ](./tutorial-discover-vmware.md)

Habár az összes alkalmazás nem fog profitálni a tárolók jelentős újratervezése nélküli közvetlen elmozdulásával, a meglévő alkalmazások a tárolóba való áthelyezésének előnyei a következők:

- **Továbbfejlesztett infrastruktúra kihasználtsága:** A tárolók segítségével több alkalmazás is megoszthatja az erőforrásokat, és ugyanazon az infrastruktúrán üzemeltetheti őket. Ennek segítségével összevonhatja az infrastruktúrát, és javíthatja a kihasználtságot.
- **Egyszerűsített felügyelet:** Ha az alkalmazásokat egy modern felügyelt infrastruktúra-platformon üzemelteti, mint például az AK-t, leegyszerűsítheti a felügyeleti eljárásokat, miközben továbbra is megtarthatja az infrastruktúra feletti irányítást. Ezt úgy érheti el, ha kihasználja vagy csökkenti az infrastruktúra-karbantartási és-kezelési folyamatokat, amelyeket hagyományosan a tulajdonában lévő infrastruktúrával végez.
- **Alkalmazás-hordozhatóság:** A tároló-specifikáció formátumának és a koordináló platformok fokozottabb bevezetésével és egységesítésével az alkalmazás hordozhatósága már nem jelent problémát.
- **Modern felügyelet elfogadása a DevOps:** A segítségével az infrastruktúra mint kód és a DevOps való áttérés révén a modern eljárásokat alkalmazhatja a felügyelethez és a biztonsághoz.


Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Hozzon létre egy Azure-fiókot.
> * Telepítse a Azure Migrate: app tárolókra bontás eszközt.
> * A Java-webalkalmazások felderítése.
> * Hozza létre a tároló rendszerképét.
> * Telepítse a tároló alkalmazást az AK-ra.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

**Követelmény** | **Részletek**
--- | ---
**Számítógép azonosítása az eszköz telepítéséhez** | Egy Windows rendszerű gép, amely a Azure Migrate: app tárolókra bontás eszközt telepíti és futtatja. A Windows rendszerű gép lehet egy kiszolgáló (Windows Server 2016 vagy újabb) vagy ügyfél (Windows 10) operációs rendszer, ami azt jelenti, hogy az eszköz futtatható az asztalon is. <br/><br/> Az eszközt futtató Windows-gépnek hálózati kapcsolattal kell rendelkeznie a konténerizálni kívánt ASP.NET-alkalmazásokat üzemeltető kiszolgálókhoz/virtuális gépekhez.<br/><br/> Győződjön meg arról, hogy a (z) Azure Migrate: app tárolókra bontás eszközt futtató Windows-gépen 6 GB szabad terület áll rendelkezésre az alkalmazás-összetevők tárolásához. <br/><br/> A Windows-gépnek közvetlen vagy proxyn keresztüli internetkapcsolattal kell rendelkeznie. <br/> <br/>Telepítse a Microsoft Web Deploy eszközt azon a gépen, amelyen az App tárolókra bontás Helper eszköz és az alkalmazáskiszolgáló még nincs telepítve. Az eszközt [innen](https://aka.ms/webdeploy3.6) töltheti le
**Alkalmazáskiszolgálók** | – Engedélyezze a Secure Shell-(SSH-) kapcsolatokat a 22-es porton a Java-alkalmazás (ok) at tároló kiszolgáló (k) et futtató kiszolgálón. <br/>
**Java-webalkalmazás** | Az eszköz jelenleg támogatja <br/><br/> – A Tomcat 8-as vagy újabb verzióján futó alkalmazások.<br/> -Alkalmazáskiszolgáló Ubuntu Linux 16.04/18.04/20.04, Debian 7/8, CentOS 6/7, Red Hat Enterprise Linux 5/6/7. <br/> – A Java 7-es vagy újabb verzióját használó alkalmazások.  <br/><br/> Az eszköz jelenleg nem támogatott <br/><br/> – Több tomcat-példányt futtató alkalmazások kiszolgálói <br/>  


## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

Az előfizetés beállítása után szüksége lesz egy Azure-beli felhasználói fiókra az alábbiakkal:
- Tulajdonosi engedélyek az Azure-előfizetéshez
- Azure Active Directory alkalmazások regisztrálásához szükséges engedélyek

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások** területen válassza az **előfizetések** lehetőséget.

    ![Keresse meg az Azure-előfizetést.](./media/tutorial-discover-vmware/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben Azure Migrate projektet kíván létrehozni.
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési** területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzáadás** gombra.

    ![A hozzáférés ellenőrzését és a szerepkör hozzárendelését megkereső felhasználói fiók keresése.](./media/tutorial-discover-vmware/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a tulajdonosi szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![Megnyitja a szerepkör-hozzárendelés hozzáadása lapot, és hozzárendel egy szerepkört a fiókhoz.](./media/tutorial-discover-vmware/assign-role.png)

7. Az Azure-fióknak szüksége van **az Azure Active Directory alkalmazások regisztrálásához szükséges engedélyekre is.**
8.  A Azure Portal területen navigáljon a  >  **felhasználók**  >  **felhasználói beállításainak** Azure Active Directory.
9.  A **felhasználói beállítások** területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

      ![A felhasználói beállításokban ellenőrizze, hogy a felhasználók regisztrálhatnak-e Active Directory alkalmazásokat.](./media/tutorial-discover-vmware/register-apps.png)

10.  Ha a "Alkalmazásregisztrációk" beállítások "nem" értékre van állítva, kérje meg a bérlőt/globális rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket. Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az **alkalmazás fejlesztői** szerepkörét egy olyan fiókhoz, amely lehetővé teszi Azure Active Directory alkalmazás regisztrálását. [További információ](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Azure Migrate letöltése és telepítése: app tárolókra bontás Tool

1. [Töltse le](https://go.microsoft.com/fwlink/?linkid=2134571) a Azure Migrate: app tárolókra bontás Installer alkalmazást egy Windows rendszerű gépen.
2. Indítsa el a PowerShellt rendszergazdai módban, és módosítsa a PowerShell-könyvtárat a telepítőt tartalmazó mappába.
3. A telepítési parancsfájl futtatása a parancs használatával

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Az alkalmazás tárolókra bontás eszközének elindítása

1. Nyisson meg egy böngészőt bármely olyan gépen, amely képes csatlakozni az App tárolókra bontás eszközt futtató Windows rendszerű géphez, és nyissa meg az eszköz URL-címét: **https://*számítógép neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást az asztalról az alkalmazás parancsikonjának kiválasztásával.

2. Ha olyan figyelmeztetés jelenik meg, amely szerint a kapcsolódás nem magán, kattintson a speciális elemre, és válassza a tovább lehetőséget a webhelyhez. Ez a figyelmeztetés akkor jelenik meg, amikor a webes felület önaláírt TLS/SSL-tanúsítványt használ.
3. A bejelentkezési képernyőn a bejelentkezéshez használja a helyi rendszergazdai fiókot a gépen.
4. Az alkalmazás típusának megadásához válassza ki a **Java Web Apps** alkalmazást a Tomcat-ben a tárolóba helyezése kívánt alkalmazás típusaként.

    ![Alapértelmezett betöltés az App tárolókra bontás eszközhöz.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Az eszköz előfeltételeinek kitöltése
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
6. Az eszköz webalkalmazás > az **Előfeltételek beállítása** lapon hajtsa végre a következő lépéseket:
   - **Kapcsolat**: az eszköz ellenőrzi, hogy a Windows rendszerű számítógép rendelkezik-e internet-hozzáféréssel. Ha a gép proxyt használ:
     - Kattintson a **proxy beállítása** elemre a proxy címének megadásához (az űrlap IP-címe vagy FQDN) és a figyelési port beállításnál.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Csak a HTTP-proxyk használata támogatott.
     - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
   - **Frissítések telepítése**: az eszköz automatikusan megkeresi a legújabb frissítéseket, és telepíti őket. Manuálisan is telepítheti az eszköz legújabb verzióját [innen.](https://go.microsoft.com/fwlink/?linkid=2134571)
   - **Secure Shell (SSH) engedélyezése**: az eszköz tájékoztatja Önt arról, hogy a Secure Shell (SSH) engedélyezve van a Java-webalkalmazások tárolóba való helyezését futtató alkalmazás-kiszolgálókon.


## <a name="login-to-azure"></a>Bejelentkezés az Azure-ba

A **Bejelentkezés** gombra kattintva jelentkezzen be az Azure-fiókjába.

1. Szüksége lesz egy eszköz kódjára az Azure-beli hitelesítéshez. A Bejelentkezés gombra kattintva megnyílik egy modális az eszköz kódjával.
2. Kattintson a **kód másolása & a bejelentkezés** elemre az eszköz kódjának másolásához és egy új böngésző lapon található Azure-beli bejelentkezési kérés megnyitásához. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.

    ![Az eszköz kódját ábrázoló modális.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Az új lapon illessze be az eszköz kódját, és fejezze be a bejelentkezést az Azure-fiókja hitelesítő adataival. A bejelentkezés befejezése után lezárhatja a böngésző fület, és visszatérhet az alkalmazás tárolókra bontás eszköz webes felületéhez.
4. Válassza ki a használni kívánt **Azure-bérlőt** .
5. Itt adhatja meg a használni kívánt **Azure-előfizetést** .

## <a name="discover-java-web-applications"></a>Java-webalkalmazások felderítése

Az App tárolókra bontás Helper eszköz távolról csatlakozik az alkalmazás-kiszolgálókhoz a megadott hitelesítő adatokkal, és megkísérli felderíteni az alkalmazás-kiszolgálókon üzemeltetett Java-webalkalmazásokat (az Apache Tomcat használatával).

1. Itt adhatja meg a Java-webalkalmazást futtató kiszolgáló **IP-címét/teljes tartománynevét és hitelesítő adatait** , amelyet a kiszolgálóhoz való távoli kapcsolódáshoz kell használni az alkalmazás-felderítéshez.
    - A megadott hitelesítő adatoknak az alkalmazáskiszolgáló legfelső szintű fiókjához (Linux) kell tartoznia.
    - Tartományi fiókoknál (a felhasználónak az alkalmazáskiszolgáló rendszergazdájának kell lennie), a felhasználónevet előtagként adja meg a tartománynév *<tartomány \ felhasználónév>* formátumban.
    - Az alkalmazások felderítését egyszerre akár öt kiszolgáló esetén is futtathatja.

2. Az **ellenőrzés gombra kattintva ellenőrizheti** , hogy az alkalmazáskiszolgáló elérhető-e az eszközt futtató gépről, és hogy a hitelesítő adatok érvényesek-e. A sikeres ellenőrzés után az Állapot oszlopban **leképezett** állapot jelenik meg.  

    ![Képernyőkép a kiszolgáló IP-címéről és a hitelesítő adatokról.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. A **Folytatás** gombra kattintva indítsa el az alkalmazás-felderítést a kiválasztott alkalmazás-kiszolgálókon.

4. Az alkalmazás-felderítés sikeres befejezését követően kiválaszthatja a tárolóba helyezése alkalmazások listáját.

    ![Képernyőfelvétel a felderített Java-webalkalmazásokról.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. A jelölőnégyzet használatával válassza ki a tárolóba helyezése alkalmazásokat.
5. **Adja meg a tároló nevét**: adja meg a tároló nevét az egyes kiválasztott alkalmazásokhoz. A tároló nevét meg kell adni <*neve: címke*>, ahol a címke a tároló képéhez használatos. Megadhatja például a tároló nevét *AppName: v1* néven.   

### <a name="parameterize-application-configurations"></a>Parametrizálja alkalmazás-konfigurációk
A konfiguráció Parameterizing elérhetővé teszi a központi telepítési idő paraméterét. Ezzel a beállítással úgy konfigurálhatja ezt a beállítást, hogy az alkalmazás üzembe helyezése nem a tároló rendszerképének egy adott értékének megfelelően legyen kódolva. Ez a beállítás például olyan paraméterek esetében hasznos, mint az adatbázis-kapcsolatok karakterláncai.
1. Az észlelt konfigurációk áttekintéséhez kattintson az **alkalmazások konfigurációi** elemre.
2. Jelölje be a jelölőnégyzetet, hogy parametrizálja az észlelt alkalmazások konfigurációit.
3. Kattintson az **alkalmaz** gombra a parametrizálja konfigurációk kiválasztása után.

   ![Képernyőfelvétel az alkalmazások konfigurációjának paraméterezés ASP.NET-alkalmazásról.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Externalize-fájlrendszer függőségei

 Az alkalmazás által használt egyéb mappákat is hozzáadhat. Adja meg, hogy a tároló rendszerképének részét képezik-e, vagy az Azure-fájlmegosztás állandó kötetein keresztül kell-e külsőleg lenni. Az állandó kötetek használatával a tárolón kívüli állapotot tároló, vagy a fájlrendszeren tárolt egyéb statikus tartalommal rendelkező állapot-nyilvántartó alkalmazások esetében kiválóan használható. [További információ](https://docs.microsoft.com/azure/aks/concepts-storage)

1. Az észlelt alkalmazás-mappák áttekintéséhez kattintson az alkalmazás mappái alatt található **Szerkesztés** elemre. Az észlelt alkalmazás mappái az alkalmazás által igényelt kötelező összetevőkként vannak meghatározva, és a rendszer a tároló képére másolja.

2. Kattintson a **mappák hozzáadása** lehetőségre, és adja meg a hozzáadni kívánt mappa elérési útját.
3. Több mappa ugyanahhoz a kötethez való hozzáadásához adja meg a vesszővel ( `,` ) elválasztott értékeket.
4. Ha azt szeretné, hogy a mappák a tárolón kívül, állandó köteten legyenek tárolva, válassza az **állandó kötet** lehetőséget.
5. Az alkalmazás mappáinak áttekintése után kattintson a **Mentés** gombra.
   ![Képernyőfelvétel az alkalmazások kötetei szolgáltatás kiválasztásáról.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Kattintson a **Folytatás** gombra a tároló rendszerkép-létrehozási fázisának folytatásához.


## <a name="build-container-image"></a>Tárolórendszerkép összeállítása


1. **Azure Container Registry kiválasztása**: a legördülő listából válassza ki az alkalmazáshoz tartozó tároló-lemezképek létrehozásához és tárolásához használni kívánt [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) . Meglévő Azure Container Registry is használhat, vagy létrehozhat egy újat az új beállításjegyzék létrehozása lehetőség használatával.

    ![Képernyőfelvétel az alkalmazás ACR kiválasztásáról.](./media/tutorial-containerize-apps-aks/build-java-app.png)


2. **Tekintse át a Docker**: az egyes kiválasztott alkalmazásokhoz szükséges Docker létrehozása a Build lépés elején történik. A Docker áttekintéséhez kattintson a **felülvizsgálat** gombra. A felülvizsgálati lépésben a Docker is hozzáadhatja a szükséges testreszabásokat, és a fordítási folyamat elindítása előtt mentheti a módosításokat.

3. **Build folyamatának elindítása**: válassza ki azokat az alkalmazásokat, amelyekhez lemezképeket szeretne készíteni, majd kattintson a **Létrehozás** gombra. A Build parancsra kattintva elindul a tároló rendszerképének összeállítása az egyes alkalmazásokhoz. Az eszköz folyamatosan figyeli a Build állapotát, és lehetővé teszi, hogy folytassa a következő lépéssel a Build sikeres befejezését követően.

4. A Build **állapotának nyomon követése**: a Build lépés előrehaladását a status (állapot) oszlopban látható **Build in progress** (állapot) oszlop alatt is figyelheti. A hivatkozás néhány percet vesz igénybe, hogy a rendszer aktiválja a létrehozási folyamatot.  

5. A Build befejezése után kattintson a **tovább** gombra a központi telepítési beállítások megadásához.

    ![Képernyőkép az alkalmazás-tároló rendszerképének létrehozásáról.](./media/tutorial-containerize-apps-aks/build-java-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>A tároló alkalmazás üzembe helyezése az AK-ban

A tároló lemezképének létrehozása után a következő lépés az alkalmazás üzembe helyezése tárolóként az [Azure Kubernetes szolgáltatásban (ak)](https://azure.microsoft.com/services/kubernetes-service/).

1. **Válassza ki az Azure Kubernetes Service-fürtöt**: adja meg azt az AK-fürtöt, amelyre az alkalmazást telepíteni kívánja.

     - A kiválasztott AK-fürtnek linuxos csomópont-készlettel kell rendelkeznie.
     - A fürtöt úgy kell konfigurálni, hogy lehetővé tegye a rendszerképeknek a képek tárolására kiválasztott Azure Container Registryból való húzását.
         - Futtassa az alábbi parancsot az Azure CLI-ben, és csatolja az AK-fürtöt az ACR-hez.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Ha nem rendelkezik AK-fürttel, vagy létre szeretne hozni egy új AK-fürtöt az alkalmazás üzembe helyezéséhez, a létrehozásához kattintson az **új AK-fürt létrehozása** lehetőségre az eszközön.      
          - Az eszköz használatával létrehozott AK-fürt Linux-csomópont-készlettel lesz létrehozva. A fürt úgy lesz konfigurálva, hogy lehetővé tegye a lemezképek lekérését a korábban létrehozott Azure Container Registryról (ha az új beállításjegyzék létrehozása lehetőséget választotta).
     - Az AK-fürt kiválasztása után kattintson a **Folytatás** gombra.

2. **Adja meg az Azure-fájlmegosztást**: Ha további mappákat adott hozzá, és bejelölte az állandó kötet lehetőséget, akkor adja meg azt az Azure-fájlmegosztást, amelyet a Azure Migrate: app tárolókra bontás eszköz használ a telepítési folyamat során. Az eszköz új könyvtárakat hoz létre ebben az Azure-fájlmegosztásban, hogy átmásolja az állandó kötet tárolására konfigurált alkalmazások mappáit. Miután az alkalmazás üzembe helyezése befejeződött, az eszköz törli az Azure-fájlmegosztást a létrehozott könyvtárak törlésével.

     - Ha nem rendelkezik Azure-fájlmegosztás használatával, vagy új Azure-fájlmegosztást szeretne létrehozni, az eszközről az **új Storage-fiók és fájlmegosztás létrehozása** lehetőségre kattintva hozhatja létre az eszközt.  

3. **Alkalmazás központi telepítési konfigurációja**: Miután végrehajtotta a fenti lépéseket, meg kell adnia az alkalmazás telepítési konfigurációját. A **Konfigurálás** elemre kattintva testreszabhatja az alkalmazás központi telepítését. A configure (Konfigurálás) lépésben megadhatja a következő testreszabásokat:
     - **Előtag-karakterlánc**: adjon meg egy előtag-karakterláncot, amelyet az AK-fürtben a tároló alkalmazáshoz létrehozott összes erőforrás nevében használni kíván.
     - **Replikakészlet**: Itt adhatja meg, hogy hány alkalmazás-példány (hüvely) legyen futtatható a tárolókban.
     - **Terheléselosztó típusa**: válassza a *külső* lehetőséget, ha a tároló alkalmazásnak elérhetőnek kell lennie a nyilvános hálózatokból.
     - **Alkalmazás konfigurációja**: bármely paraméterrel rendelkező alkalmazás-konfiguráció esetében adja meg az aktuális telepítéshez használandó értékeket.
     - **Tárterület**: az állandó kötet tárolásához konfigurált bármely alkalmazási mappa esetében határozza meg, hogy a kötetet meg kell-e osztani az alkalmazás példányai között, vagy a tároló minden példányával külön kell-e inicializálni. Alapértelmezés szerint az állandó kötetek összes alkalmazás-mappája megosztottként van konfigurálva.
     - A központi telepítési konfiguráció mentéséhez kattintson az **alkalmaz** gombra.
     - Az alkalmazás üzembe helyezéséhez kattintson a **tovább** gombra.

    ![Képernyőfelvétel a telepítési alkalmazás konfigurálásáról.](./media/tutorial-containerize-apps-aks/deploy-java-app-config.png)

4. **Az alkalmazás üzembe helyezése**: az alkalmazás központi telepítési konfigurációjának mentése után az eszköz létrehozza az Kubernetes üzembe HELYEZÉSi YAML az alkalmazáshoz.
     - A **Szerkesztés** gombra kattintva áttekintheti és testreszabhatja az alkalmazások Kubernetes-telepítési YAML.
     - Válassza ki a telepítendő alkalmazást.
     - Kattintson a **telepítés** elemre a kiválasztott alkalmazások központi telepítésének megkezdéséhez

         ![Képernyőfelvétel az alkalmazások központi telepítésének konfigurálásáról.](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - Az alkalmazás telepítése után a *központi telepítési állapot* oszlopra kattintva nyomon követheti az alkalmazáshoz telepített erőforrásokat.

## <a name="download-generated-artifacts"></a>Generált összetevők letöltése

Minden olyan összetevőt, amelyet az alkalmazás a Docker és a Kubernetes YAML-specifikációs fájlokkal történő létrehozásához és üzembe helyezéséhez használ, a rendszer az eszközt futtató gépen tárolja. Az összetevők a *C:\ProgramData\Microsoft Azure Migrate alkalmazás tárolókra bontás* találhatók.

Mindegyik alkalmazáskiszolgáló egyetlen mappát hoz létre. A tárolókra bontás folyamat során használt összes köztes összetevőt megtekintheti és letöltheti, ehhez a mappához navigálva. Az alkalmazás-kiszolgálónak megfelelő mappa az eszköz minden egyes futtatásakor törlődik az adott kiszolgálóhoz.

## <a name="troubleshoot-issues"></a>Problémák elhárítása

Az eszközzel kapcsolatos problémák elhárításához tekintse meg az App tárolókra bontás eszközt futtató Windows-gépen található naplófájlokat. Az eszköz naplófájljai a *C:\ProgramData\Microsoft Azure Migrate app Containerization\Logs* mappában találhatók.

## <a name="next-steps"></a>Következő lépések

- Containerizing ASP.NET az alkalmazásokat, és üzembe helyezheti őket Windows-tárolókban az AK-ban. [További információ](./tutorial-containerize-aspnet-kubernetes.md)
