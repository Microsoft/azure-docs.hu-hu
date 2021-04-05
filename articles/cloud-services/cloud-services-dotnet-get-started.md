---
title: Ismerkedés az Azure Cloud Services (klasszikus) és a ASP.NET szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre többrétegű alkalmazást az ASP.NET MVC és az Azure használatával. Az alkalmazás felhőszolgáltatásban fut webes és feldolgozói szerepkörben. Entity Framework, SQL Database és Azure Storage üzenetsorokat és blobokat használ.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ae7fd5a7c9bc858cb18473374e7bd5589717eac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742080"
---
# <a name="get-started-with-azure-cloud-services-classic-and-aspnet"></a>Ismerkedés az Azure Cloud Services (klasszikus) és a ASP.NET használatába

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre többrétegű .NET-alkalmazást ASP.NET MVC kezelőfelülettel, illetve hogyan telepítheti azt egy [Azure-felhőszolgáltatásban](cloud-services-choose-me.md). Az alkalmazás az [Azure SQL Database](/previous-versions/azure/ee336279(v=azure.100)) szolgáltatást, az [Azure Blob szolgáltatást](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) és az [Azure Queue szolgáltatást](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) használja. A [Visual Studio projekt letölthető](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) az MSDN kódgalériából.

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja össze és futtathatja az alkalmazást helyileg, hogyan telepítheti az Azure-ban, hogyan futtathatja a felhőben, valamint hogyan építheti fel az alapoktól kezdve. Ha szeretné, kezdheti az alapoktól a felépítést, majd később elvégezheti a tesztelés és a telepítés lépéseit.

## <a name="contoso-ads-application"></a>Contoso Ads alkalmazás
Ez az alkalmazás egy hirdetőtábla. A felhasználók szöveg megadásával és egy kép feltöltésével hoznak létre hirdetéseket. Láthatják a hirdetések miniatűr képekkel ellátott listáját, majd teljes méretben is megtekinthetik a képet, amikor kiválasztanak egy hirdetést a részletek megtekintése céljából.

![A rendszerkép megjeleníti az ad-listát](./media/cloud-services-dotnet-get-started/list.png)

Az alkalmazás [üzenetsor-központú munkasémát](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) használ, hogy áthelyezze a miniatűrök létrehozásának processzorigényes feladatát egy háttérfolyamatra.

## <a name="alternative-architecture-app-service-and-webjobs"></a>Alternatív architektúra: App Service és webjobs
Ebből az oktatóanyagból megtudhatja, hogyan futtathat előtér- és háttéralkalmazásokat egyaránt az Azure felhőszolgáltatásban. Alternatív megoldásként futtassa az előtér- [Azure app Service](../app-service/index.yml) , és használja a [webjobs](https://go.microsoft.com/fwlink/?LinkId=390226) funkciót a háttérrendszer számára. A WebJobs szolgáltatást alkalmazó oktatóanyagot a [Get Started with the Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) (Ismerkedés az Azure WebJobs SDK-val) című részben találja. További információ a forgatókönyvhöz legjobban illeszkedő szolgáltatások kiválasztásáról: [Azure app Service, Cloud Services és Virtual Machines összehasonlítás](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="what-youll-learn"></a>Ismertetett témák
* A gép alkalmassá tétele az Azure-alapú fejlesztésre az Azure SDK telepítésével.
* Visual Studio felhőszolgáltatás-projekt létrehozása ASP.NET MVC webes és feldolgozói szerepkörök alkalmazásával.
* A Cloud Service-projekt helyi tesztelése az Azure Storage Emulator használatával.
* A felhőprojekt közzététele egy Azure-felhőszolgáltatásban és tesztelése egy Azure-tárfiók használatával.
* Fájlok feltöltése és tárolása az Azure Blob szolgáltatásban.
* Az Azure Queue szolgáltatás használata a rétegek közötti kommunikációhoz.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag feltételezi, hogy tisztában van az [Azure-felhőszolgáltatások alapfogalmaival](cloud-services-choose-me.md), például a *webes szerepkör* és a *feldolgozói szerepkör* terminológiájával.  Továbbá azt is feltételezi, hogy az [ASP.NET MVC](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) és a [Web Forms](https://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) Visual Studióban való használatának módját is ismeri. A mintaalkalmazás az MVC-t használja, de az oktatóanyag nagy része a Web Forms esetében is alkalmazható.

Helyileg Azure-előfizetés nélkül is futtathatja az alkalmazást, de a felhőben való közzétételhez előfizetés szükséges. Ha nincs fiókja, [aktiválhatja az MSDN előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668), vagy [regisztrálhat egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668).

Az oktatóanyag utasításai a következő termékek bármelyikével működnek:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017
* Visual Studio 2019

Ha egyikkel sem rendelkezik, lehet, hogy az Azure SDK telepítésekor a Visual Studio automatikusan telepítve lesz.

## <a name="application-architecture"></a>Alkalmazásarchitektúra
Az alkalmazás SQL-adatbázisban tárolja a hirdetéseket, amihez az Entity Framework Code First megoldást használja a táblák létrehozásához és az adatok eléréséhez. Az egyes hirdetések esetében az adatbázis két URL-címet tárol, egyet a teljes méretű képhez, egyet pedig a miniatűrhöz.

![Ez egy ad-tábla képe](./media/cloud-services-dotnet-get-started/adtable.png)

Amikor egy felhasználó feltölt egy képet, a webes szerepkörrel rendelkező előtér-alkalmazás egy [Azure-blobban](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) tárolja azt, a hirdetés információit pedig az adatbázisban a blobra mutató URL-címmel együtt. Ezzel egy időben üzenetet ír egy Azure-üzenetsorba. A feldolgozói szerepkörrel futó háttérfolyamat rendszeres időközönként lekérdezi az új üzeneteket az üzenetsorról. Amikor egy új üzenet jelenik meg, a feldolgozói szerepkör létrehozza a kép miniatűrjét, és frissíti a miniatűr URL-címét a hirdetés adatbázismezőjében. Az alábbi ábra bemutatja, hogyan működnek együtt az alkalmazás részei.

![Diagram, amely bemutatja, hogyan hatnak az alkalmazás részei.](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>A kész megoldás letöltése és futtatása
1. Töltse le és csomagolja ki a [kész megoldást](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).
2. Indítsa el a Visual Studiót.
3. Válassza a **Fájl** menü **Projekt megnyitása** elemét, keresse meg a letöltött megoldást, majd nyissa meg a megoldásfájlt.
4. Nyomja le a Ctrl+Shift+B billentyűkombinációt a megoldás felépítéséhez.

    Alapértelmezés szerint a Visual Studio automatikusan visszaállítja a NuGet-csomag tartalmát, amelyet a *.zip* fájl nem tartalmazott. Ha a csomagok nem állnak vissza, telepítse őket manuálisan. Ehhez lépjen a **Manage NuGet Packages for Solution** (Megoldás NuGet-csomagjainak kezelése) párbeszédpanelre, és kattintson a **Restore** (Visszaállítás) gombra a jobb felső sarokban.
5. A **Megoldáskezelőben** győződjön meg arról, hogy a **ContosoAdsCloudService** van kiválasztva kiindulási projektként.
6. Ha a Visual Studio 2015-öt használja, módosítsa az SQL Server kapcsolati sztringjét az alkalmazás *Web.config* fájljában a ContosoAdsWeb projekt esetében, illetve a *ServiceConfiguration.Local.cscfg* fájlt a ContosoAdsCloudService projekt esetében. Mindkét esetben módosítsa a „(localdb)\v11.0” elemet a következőre: „(localdb)\MSSQLLocalDB”.
7. Az alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.

    Amikor helyileg futtat egy felhőszolgáltatás-projektet, a Visual Studio automatikusan meghívja az Azure *Compute Emulator* és az Azure *Storage Emulator* eszközöket. A Compute Emulator a számítógép erőforrásait felhasználva szimulálja a webes és a feldolgozói szerepkörök környezeteit. A Storage Emulator egy [SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) adatbázis használatával szimulálja az Azure felhőalapú tárolást.

    Egy felhőszolgáltatás-projekt első futtatásakor nagyjából egy percet vesz igénybe az emulátorok elindítása. Az emulátorok elindulását követően az alapértelmezett böngésző megnyitja az alkalmazás kezdőlapját.

    ![Contoso ADS architektúra 1](./media/cloud-services-dotnet-get-started/home.png)
8. Kattintson a **Create an Ad** (Hirdetés létrehozása) gombra.
9. Adjon meg néhány tesztadatot, és válasszon ki egy feltölteni kívánt *.jpg* formátumú képet, majd kattintson a **Create** (Létrehozás) elemre.

    ![Ábrán a Létrehozás lap látható](./media/cloud-services-dotnet-get-started/create.png)

    Az alkalmazás az Index lapra ugrik, de az új hirdetés miniatűrje nem jelenik meg, mert a feldolgozása még nem történt meg.
10. Várjon egy kicsit, majd frissítse az Index lapot a miniatűr megjelenítéséhez.

     ![Index lap](./media/cloud-services-dotnet-get-started/list.png)
11. Kattintson a hirdetés **Details** (Részletek) elemére a teljes méretű kép megjelenítéséhez.

     ![Részletek lap](./media/cloud-services-dotnet-get-started/details.png)

Az alkalmazást teljes mértékben helyi számítógépén futtatja, a felhőhöz való csatlakozás nélkül. A Storage Emulator az üzenetsor és a blob adatait egy SQL Server Express LocalDB adatbázisban tárolja, az alkalmazás pedig egy másik LocalDB adatbázisban tárolja a hirdetés adatait. Az Entity Framework Code First automatikusan létrehozta a hirdetés-adatbázist, amikor a webalkalmazás első alkalommal próbált hozzáférni ahhoz.

A következő szakaszban konfigurálhatja a megoldást az Azure felhőbeli erőforrások használatára az üzenetsorok, blobok és alkalmazás-adatbázisok esetében a felhőben való futtatásakor. A helyi futtatásra felhőalapú tárolás és adatbázis-erőforrások használata esetén is van lehetőség. Mindössze csak a kapcsolati sztringeket kell beállítani az útmutató alapján.

## <a name="deploy-the-application-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban
Az alkalmazás felhőben való futtatásához az alábbi lépéseket kell végrehajtania:

* Hozzon létre egy Azure-felhőszolgáltatást.
* Hozzon létre egy adatbázist a Azure SQL Databaseban.
* Hozzon létre egy Azure-tárfiókot.
* Konfigurálja a megoldást az adatbázis használatára az Azure-ban való futtatáskor.
* Konfigurálja a megoldást arra, hogy az Azure-ban való futáskor az Azure-tárfiókot használja.
* Telepítse a projektet az Azure-felhőszolgáltatásában.

### <a name="create-an-azure-cloud-service"></a>Azure-felhőszolgáltatás létrehozása
Az Azure-felhőszolgáltatás az a környezet, amelyben az alkalmazás futni fog.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com) a böngészőjében.
2. Kattintson az **Erőforrás létrehozása > Számítás > Felhőszolgáltatás** elemre.

3. A DNS-név beviteli mezőben adja meg a felhőszolgáltatáshoz tartozó URL-előtagot.

    Ennek az URL-nek egyedinek kell lennie.  Ha olyan előtagot választ, amely már használatban van, hibaüzenetet kap.
4. Adja meg a szolgáltatáshoz tartozó új erőforráscsoportot. Kattintson az **Új létrehozása** elemre, majd adja meg a nevet az Erőforráscsoport beviteli mezőjében, például CS_contososadsRG.

5. Válassza ki a régiót, ahol telepíteni szeretné az alkalmazást.

    Ez a mező határozza meg, hogy a felhőszolgáltatása melyik adatközpontban fog üzemelni. Termelési alkalmazások esetében az ügyfeleihez legközelebb eső régiót kellene kiválasztania. A jelen oktatóanyag esetében válassza az Önhöz legközelebbi régiót.
5. Kattintson a **Létrehozás** lehetőségre.

    Az alábbi képen egy CSvccontosoads.cloudapp.net URL-címmel ellátott felhőszolgáltatás létrehozása történik.

    ![A rendszerkép új felhőalapú szolgáltatást jelenít meg](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-a-database-in-azure-sql-database"></a>Adatbázis létrehozása Azure SQL Database
Amikor az alkalmazás a felhőben fut, felhőalapú adatbázist fog használni.

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforrás létrehozása > Adatbázisok > SQL Database** elemre.
2. Az **Adatbázis neve** mezőbe írja be a következőt: *contosoads*.
3. Az **Erőforráscsoport** szakaszban kattintson a **Meglévő használata** elemre, majd válassza ki a felhőszolgáltatáshoz használt erőforráscsoportot.
4. Az alábbi kép alapján kattintson a **Kiszolgáló – kötelező beállítások konfigurálása** és az **Új kiszolgáló létrehozása** elemre.

    ![Az adatbázis-kiszolgálóhoz vezető alagút](./media/cloud-services-dotnet-get-started/newdb.png)

    Amennyiben az előfizetéséhez már tartozik egy kiszolgáló, alternatív megoldásként azt is kiválaszthatja a legördülő listából.
5. A **Kiszolgálónév** mezőben adja meg a *csvccontosodbserver* nevet.

6. Adjon meg egy rendszergazdai **Bejelentkezési nevet** és **Jelszót**.

    Ha az **Új kiszolgáló létrehozása** elemet választotta, nem meglévő nevet és jelszót kell megadnia. Olyan új nevet és jelszót adjon meg, amelyet a későbbiekben az adatbázis eléréséhez fog használni. Ha korábban létrehozott adatbázist választott, akkor a rendszer a már létrehozott rendszergazdai felhasználói fiók jelszavát kéri.
7. Válassza ki ugyanazt a **helyet**, amelyet a felhőszolgáltatás számára is választott.

    Ha a felhőszolgáltatás és az adatbázis különböző adatközpontokban van (különböző régiókban), a késés mértéke megnő, és az adatközponton kívül használt sávszélességért fizetnie kell. Az adatközponton belül használt sávszélesség ingyenes.
8. Jelölje be az **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** jelölőnégyzetet.
9. Kattintson a **Kiválasztás** elemre az új kiszolgáló kijelöléséhez.

    ![Új kiszolgáló](./media/cloud-services-dotnet-get-started/newdbserver.png)
10. Kattintson a **Létrehozás** lehetőségre.

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
Az Azure-tárfiók erőforrásokat biztosít az üzenetsor és a blob adatainak felhőbeli tárolásához.

Egy valós alkalmazás esetében általában külön fiókot hozna létre az alkalmazás adatai és a naplózási adatok, illetve a tesztadatok és a termelési adatok számára is. Ebben az oktatóanyagban csak egy fiókot fog használni.

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforrás létrehozása > Tároló > Tárfiók – blob, fájl, tábla, üzenetsor** elemre.
2. A **Név** mezőbe írjon be egy URL-előtagot.

    Ez az előtag és a mező alatt látható szöveg képezi a tárfiók egyedi URL-címét. Ha a megadott előtag már használatban van, másikat kell választania.
3. Állítsa be az **üzembe helyezési modellt***klasszikus* értékre.

4. A **Replikáció** legördülő listában válassza a **Helyileg redundáns tárolás** elemet.

    Ha a georeplikáció engedélyezve van a tárfiókban, a tárolt tartalom replikálása egy másodlagos adatközpontba történik, amely ezáltal a feladatátvétel helyévé válik, amennyiben az elsődleges helyen jelentős katasztrófa következik be. A georeplikáció további költségeket vonhat maga után. A teszt- és fejlesztői fiókok esetében általában nem érdemes fizetni a georeplikációért. További információ: [Tárfiók létrehozása, kezelése vagy törlése](../storage/common/storage-account-create.md).

5. Az **Erőforráscsoport** szakaszban kattintson a **Meglévő használata** elemre, majd válassza ki a felhőszolgáltatáshoz használt erőforráscsoportot.
6. A **Hely** legördülő listában válassza ugyanazt a régiót, amelyet a felhőszolgáltatás számára is választott.

    Ha a felhőszolgáltatás és a tárfiók különböző adatközpontokban van (különböző régiókban), a késés mértéke megnő, és az adatközponton kívül használt sávszélességért fizetnie kell. Az adatközponton belül használt sávszélesség ingyenes.

    Az Azure-affinitáscsoportok egy olyan mechanizmust biztosítanak, amely minimálisra csökkenti az erőforrások között lévő távolságot az adatközpontban, csökkentve ezáltal a késés mértékét is. A jelen oktatóanyag nem használ affinitáscsoportokat. További információ: [Affinitáscsoportok létrehozása az Azure-ban](/previous-versions/azure/reference/gg715317(v=azure.100)).
7. Kattintson a **Létrehozás** lehetőségre.

    ![Új tárfiók](./media/cloud-services-dotnet-get-started/newstorage.png)

    Az alábbi képen egy `csvccontosoads.core.windows.net` URL-címmel ellátott tárfiók lesz létrehozva.

### <a name="configure-the-solution-to-use-your-database-in-azure-sql-database-when-it-runs-in-azure"></a>A megoldás konfigurálása az adatbázis használatára Azure SQL Database az Azure-ban való futtatáskor

A webes projekt és a feldolgozói szerepkör-projekt mindegyike saját adatbázis-kapcsolati karakterlánccal rendelkezik, és mindegyiknek a Azure SQL Database adatbázisára kell mutatnia, amikor az alkalmazás az Azure-ban fut.

A webes szerepkör esetében [Web.config transzformálása](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations), a feldolgozói szerepkör esetében pedig felhőszolgáltatás környezeti beállítást kell alkalmaznia.

> [!NOTE]
> Ebben és a következő szakaszban a hitelesítő adatokat projektfájlokban fogja tárolni. [Ne tároljon bizalmas adatokat nyilvános forráskódú adattárakban.](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets)
>
>

1. A ContosoAdsWeb projektben nyissa meg a *Web.Release.config* átalakítófájlt az alkalmazás *Web.config* fájljához, törölje a `<connectionStrings>` elemet tartalmazó megjegyzésblokkot, és illessze be a helyére az alábbi kódot.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Hagyja megnyitva a fájlt a szerkesztéshez.
2. Az [Azure Portalon](https://portal.azure.com) kattintson a bal oldali ablaktáblában található **SQL-adatbázisok** elemre, kattintson az oktatóanyaghoz létrehozott adatbázisra, majd kattintson a **Kapcsolati sztringek megjelenítése** elemre.

    ![Kapcsolati sztringek megjelenítése](./media/cloud-services-dotnet-get-started/showcs.png)

    A portál megjeleníti a kapcsolati sztringekat helyőrzővel helyettesített jelszóval.

    ![Kapcsolati sztringek](./media/cloud-services-dotnet-get-started/connstrings.png)
3. A *Web.Release.config* átalakítófájlban törölje a `{connectionstring}` elemet, és illessze be a helyére az Azure Portalról származó ADO.NET kapcsolati sztringet.
4. A *Web.Release.config* átalakítófájlba beillesztett kapcsolati sztringben helyettesítse a `{your_password_here}` elemet az új SQL-adatbázis számára létrehozott jelszóval.
5. Mentse a fájlt.  
6. Jelölje ki és másolja a kapcsolati sztringet (az idézőjelek nélkül) a feldolgozóiszerepkör-projekt konfigurálásának alábbi lépéseiben való használatra.
7. A **Megoldáskezelőben** a felhőszolgáltatás-projekt **Szerepkörök** területén kattintson a jobb gombbal a **ContosoAdsWorker**, majd a **Tulajdonságok** elemre.

    ![Képernyőfelvétel a Tulajdonságok menüpontra.](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. Kattintson a **Beállítások** fülre.
9. Módosítsa a **Szolgáltatás konfigurációja** beállítását a következőre: **Felhő**.
10. Jelölje ki a `ContosoAdsDbConnectionString` beállítás **Érték** mezőjét, majd illessze be az oktatóanyag előző szakaszából másolt kapcsolati sztringet.

     ![A feldolgozói szerepkör adatbázis-kapcsolati sztringje](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Mentse a módosításokat.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>A megoldás konfigurálása az Azure-tárfiók használatára az Azure-ban való futás során
Az Azure-tárfiók kapcsolati sztringjeinek tárolása a webes- és a feldolgozóiszerepkör-projektek esetében egyaránt környezeti beállításokban történik a felhőszolgáltatás-projektben. Az egyes projektek esetén külön beállításokat kell alkalmazni, ha az alkalmazás helyileg vagy a felhőben fut. A felhőkörnyezet beállításait a webes és a feldolgozóiszerepkör-projektek esetében egyaránt frissíteni fogja.

1. A **Megoldáskezelőben** a **ContosoAdsCloudService** projekt **Szerepkörök** területén kattintson a jobb gombbal a **ContosoAdsWeb** elemre, majd kattintson a **Tulajdonságok** lehetőségre.

    ![A rendszerkép megjeleníti a szerepkör tulajdonságait](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. Kattintson a **Beállítások** fülre. A **szolgáltatás konfigurációja** legördülő listában válassza a **felhő** lehetőséget.

    ![Felhő konfigurálása](./media/cloud-services-dotnet-get-started/sccloud.png)
3. Jelölje ki a **StorageConnectionString** bejegyzést, és megjelenik egy három pontot (**...**) ábrázoló gomb a sor jobb oldali végén. Kattintson a három pontot ábrázoló gombra a **Create Storage Account Connection String** (Tárfiók kapcsolati sztringjének létrehozása) párbeszédpanel megnyitásához.

    ![A Kapcsolati sztring létrehozása mező megnyitása](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. A **Create Storage Connection String** (Tárfiók kapcsolati sztringjének létrehozása) párbeszédpanelen kattintson a **Your subscription** (Saját előfizetés) elemre, válassza a korábban létrehozott tárfiókot, majd kattintson az **OK** gombra. Ha még nincs bejelentkezve, a rendszer az Azure-fiókja hitelesítő adatait kéri.

    ![Tárfiók kapcsolati sztringjének létrehozása](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Mentse a módosításokat.
6. A `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` kapcsolati sztring beállításához kövesse ugyanazt az eljárást, mint a `StorageConnectionString` kapcsolati sztring esetében.

    Ez a kapcsolati sztring naplózásra használható.
7. A **ContosoAdsWorker** szerepkör mindkét kapcsolati sztringjének beállításához kövesse ugyanazt az eljárást, mint a **ContosoAdsWeb** szerepkör esetében. Ne felejtse el a **Szolgáltatás konfigurációja** beállítását a következőre módosítani: **Felhő**.

A Visual Studio felhasználói felületén keresztül konfigurált szerepkörnyezeti beállítások a ContosoAdsCloudService projekt alábbi fájljaiban lesznek tárolva:

* *ServiceDefinition.csdef* – Meghatározza a beállításneveket.
* *ServiceConfiguration.Cloud.cscfg* – Értékeket biztosít az alkalmazás felhőben való futtatásához.
* *ServiceConfiguration.Local.cscfg* – Értékeket biztosít az alkalmazás helyi futtatásához.

A ServiceDefinition.csdef például az alábbi definíciókat tartalmazza:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

A *ServiceConfiguration.Cloud.cscfg* fájl pedig azokat az értékeket tartalmazza, amelyeket a beállításokhoz adott meg a Visual Studióban.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

Az `<Instances>` beállítás megadja azon virtuális gépek számát, amelyeken az Azure a feldolgozói szerepkör kódját fogja futtatni. A [További lépések](#next-steps) szakasz hivatkozásokat tartalmaz a felhőszolgáltatás kiterjesztésére vonatkozó további információkra.

### <a name="deploy-the-project-to-azure"></a>A projekt központi telepítése az Azure-ban
1. A **Megoldáskezelőben** kattintson a jobb gombbal a **ContosoAdsCloudService** felhőprojektre, majd válassza a **Közzététel** lehetőséget.

   ![Közzététel menü](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. Az **Publish Azure Application** (Azure-alkalmazás közzététele) varázsló **Bejelentkezés** lépésében kattintson a **Tovább** gombra.

    ![Bejelentkezés lépés](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. A varázsló **Beállítások** lépésében kattintson a **Tovább** gombra.

    ![Beállítások lépés](./media/cloud-services-dotnet-get-started/pubsettings.png)

    A **Speciális** lapon szereplő alapértelmezett beállítások megfelelnek a jelen oktatóanyag céljainak. A Speciális lappal kapcsolatos további információkért lásd: [Publish Azure Application Wizard](/visualstudio/azure/vs-azure-tools-publish-azure-application-wizard) (Azure-alkalmazás közzététele varázsló).
4. Az **Összegzés** lépésben kattintson a **Közzététel** lehetőségre.

    ![Összegzés lépés](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Megnyílik az **Azure tevékenységnapló** ablak a Visual Studióban.
5. Kattintson a jobbra mutató nyíl ikonra a telepítés részleteinek kibontásához.

    A telepítés akár 5 percet vagy több időt is igénybe vehet.

    ![Azure tevékenységnapló ablak](./media/cloud-services-dotnet-get-started/waal.png)
6. Amikor a telepítés befejeződött, kattintson a **Webalkalmazás URL-címe** elemre az alkalmazás elindításához.
7. Most már hirdetések létrehozásával, megtekintésével és szerkesztésével tesztelheti az alkalmazást, ugyanúgy, mint amikor helyileg futtatta.

> [!NOTE]
> Ha befejezte a tesztelést, törölje vagy állítsa le a felhőszolgáltatást. Akkor is keletkezhetnek költségek, ha nem használja a felhőszolgáltatást, mivel virtuálisgép-erőforrások vannak lefoglalva számára. Ha hagyja, hogy továbbra is fusson, bárki, aki rátalál az URL-címére, létrehozhat és megtekinthet hirdetéseket. Az [Azure Portalon](https://portal.azure.com) lépjen a felhőszolgáltatás **Áttekintés** lapjára, és kattintson a lap tetején található **Törlés** gombra. Ha csak ideiglenesen szeretné megakadályozni, hogy mások hozzáférjenek a webhelyhez, kattintson a **Leállítás** gombra. Ebben az esetben továbbra is keletkeznek költségek. Hasonló eljárással törölheti az SQL-adatbázist és a tárfiókot, ha már nincs szüksége rájuk.
>
>

## <a name="create-the-application-from-scratch"></a>Teljesen új alkalmazás létrehozása
Ha még nem töltötte le a [kész alkalmazást](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4), most tegye meg. Fájlokat fog átmásolni a letöltött projektből az új projektbe.

A Contoso Ads alkalmazás létrehozása az alábbi lépésekből áll:

* Hozzon létre egy Visual Studio felhőszolgáltatás-megoldást.
* Frissítse és adja hozzá a NuGet-csomagokat.
* Állítsa be a projekt hivatkozásait.
* Konfigurálja a kapcsolati sztringekat.
* Adja hozzá a kódfájlokat.

A megoldás létrehozása után áttekinti a felhőszolgáltatás-projektekre nézve egyedi kódot, valamint az Azure-blobokat és üzenetsorokat.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Visual Studio felhőszolgáltatás-megoldás létrehozása
1. A Visual Studio **Fájl** menüjében válassza az **Új projekt** lehetőséget.
2. Az **Új projekt** párbeszédpanel bal oldali ablaktáblájában bontsa ki a **Visual C#** elemet, és válassza ki a **Felhő** sablonokat, majd az **Azure Cloud Service** sablont.
3. Adja a ContosoAdsCloudService nevet a projektnek és a megoldásnak, majd kattintson az **OK** gombra.

    ![Új projekt](./media/cloud-services-dotnet-get-started/newproject.png)
4. Az **Új Azure Cloud Service** párbeszédpanelen adjon hozzá egy webes és egy feldolgozói szerepkört. A webes szerepkörnek adja a ContosoAdsWeb, a feldolgozói szerepkörnek pedig a ContosoAdsWorker nevet. (A szerepkörök alapértelmezett nevének módosításához használja a jobb oldali ablaktáblában található ceruza ikont.)

    ![Új Cloud Service-projekt](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. Amikor megjelenik a webes szerepkör **Új ASP.NET-projekt** párbeszédpanele, válassza az MVC-sablont, majd kattintson a **Hitelesítés módosítása** elemre.

    ![Hitelesítés módosítása](./media/cloud-services-dotnet-get-started/chgauth.png)
6. A **Change Authentication** (Hitelesítés módosítása) párbeszédpanelen kattintson a **No Authentication** (Nincs hitelesítés) elemre, majd az **OK** gombra.

    ![Nincs hitelesítés](./media/cloud-services-dotnet-get-started/noauth.png)
7. A **New ASP.NET Project** (Új ASP.NET-projekt) párbeszédpanelen kattintson az **OK** gombra.
8. A **Megoldáskezelőben** kattintson a jobb gombbal a megoldásra (ne a projektek egyikére), és válassza az **Add - New Project** (Hozzáadás – Új projekt) lehetőséget.
9. Az **Add New Project** (Új projekt hozzáadása) párbeszédpanelen válassza a bal oldali ablaktáblában lévő **Visual C#** elem alatt található **Windows** lehetőséget, majd kattintson az **Class Library** (Osztálytár) sablonra.  
10. Adja a *ContosoAdsCommon* nevet a projektnek, majd kattintson az **OK** gombra.

    A webes és a feldolgozói szerepkörből is hivatkoznia kell az Entity Framework-környezetre és az adatmodellre. Alternatív megoldásként definiálhatja az EF-hez kapcsolódó osztályokat a webesszerepkör-projektben, majd hivatkozhat erre a projektre a feldolgozói szerepkörből. Az alternatív megoldás részeként azonban a feldolgozói szerepkör projektje egy olyan webes szerelvényre is hivatkozni fog, amelyre nincs szüksége.

### <a name="update-and-add-nuget-packages"></a>NuGet-csomagok frissítése és hozzáadása
1. Nyissa meg a megoldáshoz tartozó **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanelt.
2. Az ablak tetején válassza az **Updates** (Frissítések) elemet.
3. Keresse meg a *WindowsAzure.Storage* csomagot, és ha szerepel a listában, jelölje ki, majd válassza ki, hogy melyik webes és feldolgozói projektben kívánja frissíteni, és végül kattintson az **Update** (Frissítés) gombra.

    A Storage ügyféloldali kódtár gyakrabban frissül, mint a Visual Studio projektsablonjai, így gyakran tapasztalhatja, hogy egy újonnan létrehozott projektben lévő verziót frissíteni kell.
4. Az ablak tetején válassza a **Browse** (Tallózás) elemet.
5. Keresse meg az *EntityFramework* NuGet-csomagot, majd telepítse mind a három projektben.
6. Keresse meg a *Microsoft.WindowsAzure.ConfigurationManager* NuGet-csomagot, majd telepítse a feldolgozói szerepkör projektben.

### <a name="set-project-references"></a>A projekt hivatkozásainak beállítása
1. A ContosoAdsWeb projektben állítson be egy hivatkozást a ContosoAdsCommon projektre. Kattintson a jobb gombbal a ContosoAdsWeb projektre, majd kattintson a **hivatkozások**  -  **Hozzáadás hivatkozásokra**. A **Hivatkozáskezelő** párbeszédpanelen válassza a bal oldali ablaktáblában lévő **Megoldás – Projektek**, majd a **ContosoAdsCommon** lehetőséget, és végül kattintson az **OK** gombra.
2. A ContosoAdsWorker projektben állítson be egy hivatkozást a ContosoAdsCommon projektre.

    A ContosoAdsCommon tartalmazza az Entity Framework-adatmodellt és a környezeti osztályt, amelyet az elő- és a háttéralkalmazás egyaránt használ.
3. A ContosoAdsWorker projektben állítson be egy hivatkozást a következőre: `System.Drawing`.

    Ezt a szerelvényt a háttéralkalmazás használja a képek miniatűrökké való átalakításához.

### <a name="configure-connection-strings"></a>Kapcsolati sztringek konfigurálása
Ebben a szakaszban Azure Storage- és SQL-kapcsolati sztringeket fog konfigurálni helyi tesztelés céljából. Az oktatóanyag korábbi telepítési utasításai ismertetik a kapcsolati sztringek beállításának módját, amikor az alkalmazás a felhőben fut.

1. A ContosoAdsWeb projektben nyissa meg az alkalmazás Web.config fájlját, és illessze be a következő `connectionStrings` elemet a `configSections` elem után.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    A Visual Studio 2015 vagy újabb használata esetén cserélje le a „v11.0” elemet az „MSSQLLocalDB” elemre.
2. Mentse a módosításokat.
3. A ContosoAdsCloudService projektben a **Szerepkörök** területen kattintson a jobb gombbal a ContosoAdsWeb elemre, majd kattintson a **Tulajdonságok** elemre.

    ![Szerepkör tulajdonságai – rendszerkép](./media/cloud-services-dotnet-get-started/roleproperties.png)

4. A **ContosoAdsWeb [szerepkör]** tulajdonságok ablakában kattintson a **Beállítások** fülre, majd a **beállítás hozzáadása** elemre.

    A **Service Configuration** (Szolgáltatáskonfiguráció) **All Configurations** (Minden konfiguráció) értékét ne módosítsa.
5. Adjon hozzá egy *StorageConnectionString* névvel ellátott beállítást. A **Típus** beállítása legyen *ConnectionString*, az **Érték** beállítása pedig *UseDevelopmentStorage=true*.

    ![Új kapcsolati sztring](./media/cloud-services-dotnet-get-started/scall.png)
6. Mentse a módosításokat.
7. Kövesse ugyanezt az eljárást egy tárolási kapcsolati sztring hozzáadásához a ContosoAdsWeb szerepkör tulajdonságaihoz.
8. A **ContosoAdsWorker [Szerepkör]** tulajdonságai ablakban maradva adjon hozzá egy másik kapcsolati sztringet:

   * Név: ContosoAdsDbConnectionString
   * Típus: Sztring
   * Érték: Illessze be ugyanazt a kapcsolati sztringet, amelyet a webes szerepkör projekt esetében használt. (Az alábbi példa a Visual Studio 2013 kiadásra vonatkozik. (Ne feledje módosítani az Adatforrást, ha ezt a példát a Visual Studio 2015 vagy újabb kiadás használata mellett követi.)

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Kódfájlok hozzáadása
Ebben a szakaszban kódfájlokat fog másolni a letöltött megoldásból az új megoldásba. A következő szakaszok bemutatják és ismertetik a kód legfontosabb részeit.

Ha fájlokat szeretne hozzáadni egy projekthez vagy mappához, kattintson a jobb gombbal a projektre vagy mappára, majd kattintson a  -  **meglévő elem** hozzáadása lehetőségre. Jelölje ki a kívánt fájlokat, majd kattintson az **Add** (Hozzáadás) gombra. Ha a rendszer rákérdez, hogy lecseréli-e a meglévő fájlokat, kattintson a **Yes** (Igen) gombra.

1. A ContosoAdsCommon projektben törölje a *Class1.cs* fájlt, és a helyére illessze be az *Ad.cs* és a *ContosoAdscontext.cs* fájlt a letöltött projektből.
2. A ContosoAdsWeb projektben adja hozzá az alábbi fájlokat a letöltött projektből.

   * *Global. asax. cs*.  
   * A *Views\Shared* mappában: *\_Layout.cshtml*.
   * A *Views\Home* mappában: *Layout.cshtml*.
   * A *Controllers* mappában: *AdController.cs*.
   * A *Views\Ad* mappában (először hozza létre a mappát): öt *.cshtml* fájl.
3. A ContosoAdsWorker projektben adja hozzá a *WorkerRole.cs* fájlt a letöltött projektből.

Most már létrehozhatja és futtathatja az alkalmazást az oktatóanyag korábbi utasításai szerint, amely a helyi adatbázist és a Storage Emulator erőforrásait fogja használni.

Az alábbi szakaszok az Azure-környezetek, -blobok és -üzenetsorok használatával kapcsolatos kódot ismerteti. A jelen oktatóanyag nem tartalmazza az MVC-vezérlők és nézetek szerkezet használatával történő létrehozásának vagy az SQL Server-adatbázisokkal együttműködő Entity Framework-kód megírásának módját, illetve az ASP.NET 4.5-ben való aszinkron programozás alapjait. Ezen témakörökről az alábbi forrásanyagokban talál információt:

* [Bevezetés az MVC 5 használatába](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Bevezetés az EF 6 és az MVC 5 használatába](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Bevezetés az aszinkron programozásba a .NET 4.5 rendszerben](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon – Ad.cs
Az Ad.cs fájl megad egy felsorolást a kategóriákhoz és egy POCO entitásosztályt a hirdetés információihoz.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon – ContosoAdsContext.cs
A ContosoAdsContext osztály megadja, hogy az Ad osztály egy DbSet gyűjteményben lesz használva, amelyet az Entity Framework egy SQL-adatbázisban tárol.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

Az osztály két konstruktorral rendelkezik. Az elsőt a webes projekt használja, és a Web.config fájlban tárolt kapcsolati sztring nevét adja meg. A második konstruktorral adhatja meg a feldolgozói szerepkör projektje által használt tényleges kapcsolati sztringet, mivel a projektben nem található Web.config fájl. Korábban már látta a kapcsolati sztring tárolásának helyét, a későbbiekben pedig láthatja, hogyan kérdezi le a kód a kapcsolati sztringet, amikor elindítja a DbContext osztályt.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb – Global.asax.cs
Az `Application_Start` metódusból meghívott kód létrehoz egy *képek* blobtárolót és egy *képek* üzenetsort, amennyiben még nem léteznek. Ez biztosítja, hogy valahányszor új tárfiókot kezd használni, vagy egy új számítógépen használja a Storage Emulatort, a szükséges blobtároló és üzenetsor automatikusan létrejöjjön.

A kód a *.cscfg*-fájlból származó tárolási kapcsolati sztring használatával fér hozzá a tárfiókhoz.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Ezután hivatkozást kap a *képek* blobtárolóhoz, létrehozza a tárolót, ha az még nem létezik, és beállítja a hozzáférési engedélyeket az új tárolóhoz. Alapértelmezés szerint az új tárolók csak tárfiók-hitelesítő adatokkal rendelkező ügyfeleknek engedélyezik a blobokhoz való hozzáférést. A webhely szempontjából szükséges, hogy a blobok nyilvánosak legyenek, hogy képeket jeleníthessen meg a képblobokra mutató URL-címek használatával.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Egy hasonló kód a *képek* üzenetsor hivatkozását szerzi be, majd létrehoz egy új üzenetsort. Ebben az esetben nincs szükség az engedélyek módosítására.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---_layoutcshtml"></a>ContosoAdsWeb – \_Layout.cshtml
A *_Layout.cshtml* fájl beállítja az alkalmazás nevét a fejlécben és a láblécben, és létrehoz egy „Ads” menübejegyzést.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb – Views\Home\Index.cshtml
A *Views\Home\Index.cshtml* fájl kategóriahivatkozásokat jelenít meg a kezdőlapon. A hivatkozások átadják a `Category` felsorolás egy lekérdezési karakterlánc változóban lévő egész számú értékét az Ads indexlapnak.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb – AdController.cs
A *AdController. cs* fájlban a konstruktor meghívja a `InitializeStorage` metódust olyan Azure Storage ügyféloldali függvénytár-objektumok létrehozásához, amelyek API-t biztosítanak a blobok és a várólisták használatával való munkához.

Ezután a kód lekér egy hivatkozást a *képek* blobtárolóra, ahogy azt korábban a *Global.asax.cs* esetében is láthatta. Mindeközben beállít egy webalkalmazásokhoz használható alapértelmezett [újrapróbálkozási házirendet](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling). Az alapértelmezett exponenciális leállítási újrapróbálkozási szabályzata azt eredményezheti, hogy a webalkalmazás nem válaszol egy percnél hosszabb ideig az átmeneti hibák ismételt újrapróbálkozásakor. Az itt megadott újrapróbálkozási szabályzat minden próbálkozás után három másodpercet vár, legfeljebb három alkalommal.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Egy hasonló kód a *képek* üzenetsorra kér le hivatkozást.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

A vezérlőkód nagy része jellemzően egy DbContext osztályt használó Entity Framework-adatmodellel történő használatra való. Kivétel ez alól a HttpPost `Create` metódus, amely feltölti és blobtárolóba menti a fájlokat. A modellkötő [HttpPostedFileBase](/dotnet/api/system.web.httppostedfilebase) objektumot biztosít a metódus számára.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Ha a felhasználó feltöltésre jelöl ki egy fájlt, a kód feltölti és egy blobba menti azt, majd frissíti a Hirdetés adatbázisrekordot a blobra mutató URL-címmel.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

A feltöltést végző kód az `UploadAndSaveBlobAsync` metódusban található. Létrehoz egy GUID nevet a blob számára, feltölti és menti a fájlt, majd visszaad egy hivatkozást a mentett blobra.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Miután a HttpPost `Create` metódus feltölt egy blobot és frissíti az adatbázist, létrehoz egy üzenetsor-üzenetet, amely tájékoztatja a háttérfolyamatot arról, hogy egy kép készen áll a miniatűrré való átalakításra.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

A HttpPost `Edit` metódus kódja is hasonló, azzal a különbséggel, hogy ha a felhasználó egy új képfájlt jelöl ki, minden létező blobot törölni kell.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

A következő példa bemutatja a kódot, amely a hirdetés törlésekor törli a blobokat.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb – Views\Ad\Index.cshtml és Details.cshtml
Az *Index.cshtml* fájl a hirdetés többi adatát tartalmazó miniatűrt jelenít meg.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

A *Details.cshtml* fájl a teljes méretű képet jeleníti meg.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb – Views\Ad\Create.cshtml és Edit.cshtml
A *Create.cshtml* és az *Edit.cshtml* fájlok megadják az űrlap kódolását, amely lehetővé teszi a vezérlő számára a `HttpPostedFileBase` objektum lekérését.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Egy `<input>` elem jelzi a böngészőnek, hogy biztosítson egy fájlkiválasztási párbeszédpanelt.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker – WorkerRole.cs – OnStart metódus
A feldolgozói szerepkör indulásakor az Azure feldolgozóiszerepkör-környezet meghívja a `WorkerRole` osztályban lévő `OnStart` metódust, az `OnStart` metódus befejezésekor pedig a `Run` metódust.

Az `OnStart` metódus lekéri az adatbázis-kapcsolati sztringet a *.cscfg*-fájlból, és átadja az Entity Framework DbContext osztálynak. Alapértelmezés szerint az SQLClient szolgáltató van használatban, így azt nem kell megadni.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Ezután a metódus lekér egy hivatkozást a tárfiókra, valamint létrehozza a blobtárolót és az üzenetsort, ha azok még nem léteznek. Az ehhez tartozó kód hasonló ahhoz, amelyet a webes szerepkör `Application_Start` metódusában látott.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker – WorkerRole.cs – Run metódus
A `Run` metódus az `OnStart` metódus inicializálási feladatának befejezése után lesz meghívva. A metódus elindít egy végtelen ciklust, amely az üzenetsor új üzeneteit figyeli, és a beérkezésükkor feldolgozza azokat.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Ha a ciklus egyes ismétlései után nem található üzenet, a program egy másodpercre alvó állapotba vált. Ez megakadályozza, hogy a feldolgozói szerepkör túl sok CPU-időt használjon és további tárolási tranzakciós költségeket halmozzon fel. A Microsoft ügyféltanácsadói csapatának van egy története egy fejlesztőről, aki ezt elfelejtette beépíteni, elvégezte az éles környezetbe való telepítést, azután elment nyaralni. Amikor visszatértek, a felügyelete több, mint a vakáció.

Néha előfordul, hogy az üzenetsor egyik üzenetének tartalma feldolgozási hibát okoz. Az ilyet *ártalmas üzenetnek* nevezik, és ha épp naplózott egy hibát, majd újraindította a ciklust, akkor az üzenet feldolgozásával a végtelenségig próbálkozhat.  Ezért a catch blokk tartalmaz egy if utasítást, amely ellenőrzi, hogy az alkalmazás hány alkalommal próbálta feldolgozni az aktuális üzenetet, és amennyiben több mint 5 alkalommal, az üzenet törlődik az üzenetsorból.

`ProcessQueueMessage`akkor lesz meghívva, ha az üzenetsorban található üzenet.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Ez a kód beolvassa az adatbázist a kép URL-címének lekéréséhez, miniatűrré alakítja a képet, a miniatűrt egy blobba menti, frissíti az adatbázist a miniatűr blob URL-címével, és törli az üzenetsorban lévő üzenetet.

> [!NOTE]
> Az egyszerűség kedvéért a `ConvertImageToThumbnailJPG` metódusban lévő kód osztályokat alkalmaz a System.Drawing névtérben. A névtérben lévő osztályok viszont a Windows-űrlapokkal való használatra lettek tervezve. Windows- vagy ASP.NET-szolgáltatásban való használatuk nem támogatott. További információk a képfeldolgozási beállításokról: [Dynamic Image Generation](https://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) (Dinamikus képek létrehozása) és [Deep Inside Image Resizing](https://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na) (A képek átméretezésének részletei).
>
>

## <a name="troubleshooting"></a>Hibaelhárítás
Ha az oktatóanyag utasításainak követése ellenére valami mégsem működne, íme néhány gyakran előforduló hiba és azok megoldása.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
Ha az Azure `RoleEnvironment` -ban futtat egy alkalmazást az Azure-ban, vagy amikor helyileg futtatja az Azure-beli számítási emulátort, akkor az objektumot az Azure nyújtja.  Ha a helyi futtatás során ez a hiba jelenik meg, ellenőrizze, hogy a ContosoAdsCloudService projektet állította-e be kiindulási projektként. Ezzel beállítja az Azure számítási emulátor használatával futtatandó projektet.

Az alkalmazás többek között a *.cscfg*-fájlokban tárolt kapcsolatisztring-értékek lekérésére használja az Azure RoleEnvironmentet, ezért a kivétel egy másik oka egy hiányzó kapcsolati sztring. Győződjön meg arról, hogy a ContosoAdsWeb projekt Felhő- és a Helyi konfigurációiban is létrehozta a StorageConnectionString beállítást, illetve arról is, hogy a ContosoAdsWorker projekt mindkét konfigurációjában létrehozta mindkét kapcsolati sztringet. Ha a **Find All** (Összes keresése) funkció használatával keres a StorageConnectionString kifejezésre a megoldás egészében, 6 fájlban, 9 alkalommal kell megjelennie.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>A felülbírálás nem alkalmazható a(z) xxx portra. Az új port a HTTP protokoll esetében megengedett legalacsonyabb, 8080 érték alatt van
Próbálja módosítani a webes projekt által használt port számát. Kattintson a jobb gombbal a ContosoAdsWeb projektre, majd kattintson a **Properties** (Tulajdonságok) elemre. Kattintson a **Web** lapra, majd módosítsa a port számát a **Projekt URL-címe** beállításban.

A probléma megoldására irányuló alternatív megoldásért tekintse meg a következő szakaszt.

### <a name="other-errors-when-running-locally"></a>A helyi futtatás során felmerülő egyéb hibák
Alapértelmezés szerint az új Cloud Service-projektek az Azure számítási emulátor Express használatával szimulálják az Azure-környezetet. Ez a teljes Compute Emulator egyszerűsített verziója, és bizonyos körülmények között előfordulhat, hogy a teljes emulátor akkor is működik, amikor az Express verzió nem.  

Ha a teljes emulátor használatára szeretné módosítani a projekt beállítását, kattintson a jobb gombbal a ContosoAdsCloudService projektre, majd kattintson a **Tulajdonságok** lehetőségre. A **Tulajdonságok** ablakban kattintson a **Web** lapra, majd kattintson a **Use Full Emulator** (Teljes emulátor használata) választógombra.

Az alkalmazás teljes emulátorral való futtatásához rendszergazdai jogosultságokkal kell megnyitnia a Visual Studiót.

## <a name="next-steps"></a>Következő lépések
A Contoso Ads alkalmazás kialakítása szándékosan egyszerű az első lépéseket ismertető oktatóanyag kedvéért. Nem valósítja meg például a [függőségi beszúrást](https://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) vagy a [működési minták adattárát és egységét](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), nem [használ felületet a naplózáshoz](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), nem használ [EF Code First áttelepítést](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) az adatmodellek kezeléséhez vagy [EF-kapcsolati rugalmasságot](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) az átmeneti hálózati hibák kezeléséhez stb.

Az alábbiakban példákat talál felhőszolgáltatás-alkalmazásokra, amelyek több valós kódolási gyakorlatot mutatnak be az egyszerűbbektől az összetettebbekig:

* [PhluffyFotos](https://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Az elve hasonló a Contoso Ads elvéhez, de több funkciót és valós kódolási gyakorlatot alkalmaz.
* [Azure Cloud Service Multi-Tier Application with Tables, Queues, and Blobs](https://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36) (Többrétegű Azure-felhőszolgáltatás táblákkal, üzenetsorokkal és blobokkal). Ismerteti az Azure Storage-táblákat, valamint a blobokat és üzenetsorokat. Az Azure SDK for .NET egy korábbi verzióján alapul. Az aktuális verzióval való használathoz néhány módosítást kell végrehajtani.

Általános információk a felhőalapú fejlesztésről: [Building Real-World Cloud Apps with Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction) (Valódi felhőalapú alkalmazások létrehozása az Azure használatával).

Bemutató videó az Azure Storage ajánlott eljárásairól és mintáiról: [Microsoft Azure Storage – What's New, Best Practices and Patterns](https://channel9.msdn.com/Events/Build/2014/3-628) (Microsoft Azure Storage – Újdonságok, ajánlott eljárások és minták).

További információkat találhat az alábbi forrásokban:

* [Azure Cloud Services – 1. rész: Bevezetés](https://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Cloud Services kezelése](cloud-services-how-to-manage-portal.md)
* [Azure Storage](../storage/index.yml)
* [Felhőszolgáltató kiválasztása](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
