---
title: Azure Service Fabric-bővítmény az Eclipse-hez
description: Ismerkedjen meg a Java Azure Service Fabric használatának első lépéseivel az Eclipse használatával és a Service Fabric megadott beépülő modullal.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.custom: devx-track-java
ms.openlocfilehash: b85206f9b1e92607bba7b6f141b700922c129b65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656920"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Az Eclipse Service Fabric beépülő moduljának Java alkalmazásfejlesztése
Az Eclipse a Java-fejlesztők által leggyakrabban használt integrált fejlesztőkörnyezetek (IDE-k) közé tartozik. Ebben a cikkben azt ismertetjük, hogyan állíthatja be az Eclipse fejlesztői környezetet az Azure Service Fabrickel való használathoz. Megtudhatja, hogyan telepítheti a Service Fabric beépülő modult, hogyan hozhat létre Service Fabric-alkalmazást, és hogyan helyezhet üzembe Service Fabric-alkalmazásokat helyi vagy távoli Service Fabric-fürtön az Eclipse-ben. 

> [!NOTE]
> Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a JDK 8 telepítve van a rendszeren, és az Eclipse-ben van kiválasztva.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>A Service Fabric beépülő modul telepítése vagy frissítése az Eclipse-ben
Telepíthet egy Service Fabric beépülő modult az Eclipse-en. A beépülő modul segíthet leegyszerűsíteni a Java-szolgáltatások létrehozásának és üzembe helyezésének folyamatát.

> [!IMPORTANT]
> A Service Fabric beépülő modulhoz Eclipse Neon vagy újabb verzió szükséges. Az ezt a megjegyzést követő útmutatások segítségével ellenőrizheti az Eclipse verzióját. Ha az Eclipse egy korábbi verziója van telepítve, az [Eclipse webhelyéről](https://www.eclipse.org) tölthet le újabb verziót. Nem ajánlott az új verziót az Eclipse meglévő telepítésére telepíteni (azt felülírni). A meglévő verziót eltávolíthatja a telepítő futtatása előtt, vagy másik könyvtárba telepítheti az újabb verziót. 
> 
> Ubuntu rendszeren ajánlott közvetlenül az Eclipse webhelyéről elvégezni a telepítést csomagtelepítő helyett (`apt` vagy `apt-get`). Így biztosan az Eclipse legfrissebb verzióját fogja beszerezni. 

Telepítse az Eclipse Neon vagy újabb verzióját az [Eclipse webhelyéről](https://www.eclipse.org).  A Buildship 2.2.1-es vagy újabb verzióját is telepítheti (a Service Fabric beépülő modul nem kompatibilis a Buildship régebbi verzióival):
-   A telepített összetevők verziójának megtekintéséhez az Eclipse-ben lépjen a **Súgó**  >  **az Eclipse**-  >  **telepítés részleteire**.
-   A Buildship frissítéséért lásd: [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse beépülő modulok a Gradle-hez).
-   Az Eclipse frissítéseinek kereséséhez és telepítéséhez lépjen a **Súgó**  >  **frissítések keresése** elemre.

Telepítse a Service Fabric beépülő modult az Eclipse-ben, lépjen a **Súgó**  >  **új szoftver telepítése** elemre.
1. A **Work with (munka** ) mezőben adja meg a https: \/ /dl.microsoft.com/Eclipse értéket.
2. Kattintson a **Hozzáadás** parancsra.

   ![Az Eclipse Service Fabric beépülő modulja][sf-eclipse-plugin-install]
3. Válassza ki a Service Fabric beépülő modult, majd kattintson a **Next** (Tovább) gombra.
4. Végezze el a telepítés lépéseit, majd fogadja el a Microsoft szoftverlicenc-szerződését.

Ha a Service Fabric beépülő modul már telepítve van, telepítse a legújabb verziót. 
1. Az elérhető frissítések kereséséhez lépjen az   >  **Eclipse**-  >  **telepítés részleteinek** súgójában. 
2. A telepített beépülő modulok listájában válassza ki a Service Fabric elemet, majd kattintson az **Update** (Frissítés) parancsra. A rendszer telepíti az elérhető frissítéseket.
3. A Service Fabric beépülő modul frissítése után frissítse a Gradle-projektet is.  Kattintson a jobb gombbal **build.gradle** elemre, majd válassza a **Refresh** (Frissítés) lehetőséget.

> [!NOTE]
> Ha a Service Fabric beépülő modul telepítése vagy frissítése túl lassú, azt az Eclipse valamelyik beállítása okozhatja. Az Eclipse metaadatokat gyűjt az Eclipse-példányhoz regisztrált frissítési helyek összes módosításáról. Ahhoz, hogy fel tudja gyorsítani a Service Fabric beépülő modul frissítéseinek keresési és telepítési folyamatát, lépjen az **Available Software Sites** (Elérhető szoftverhelyek) területre. Törölje az összes hely jelölőnégyzetét, kivéve a Service Fabric beépülő modul helyére (https: \/ /dl.microsoft.com/Eclipse/Azure/servicefabric) mutató összes helyet.

> [!NOTE]
>Ha az Eclipse nem a várt módon működik a Mac gépén (vagy a futtatáshoz felügyelőként történő bejelentkezést ír elő), keresse meg az **ECLIPSE_INSTALLATION_PATH** mappa **Eclipse.app/Contents/MacOS** almappáját. Indítsa el az Eclipse-et az `./eclipse` futtatásával.

## <a name="create-a-service-fabric-application-in-eclipse"></a>Service Fabric-alkalmazás létrehozása az Eclipse-ben

1.  Az Eclipse-ben lépjen a **fájl**  >  **új** elemre  >  . Válassza a **Service Fabric Project** (Service Fabric-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 1. oldal][create-application/p1]

2.  Adja meg a projekt nevét, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 2. oldal][create-application/p2]

3.  A sablonok listájában válassza a **Service Template** (Szolgáltatássablon) elemet. Válassza ki a szolgáltatássablon típusát (Actor, Stateless, Container, Guest Binary – aktor, állapot nélküli, tároló, vendég bináris), majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 3. oldal][create-application/p3]

4.  Adja meg a szolgáltatás nevét és a szolgáltatás részleteit, majd kattintson a **Finish** (Befejezés) gombra.

    ![Service Fabric – Új projekt, 4. oldal][create-application/p4]

5. Az első Service Fabric-projekt létrehozásakor az **Open Associated Perspective** (Társított perspektíva megnyitása) párbeszédpanelen kattintson a **Yes** (Igen) gombra.

    ![Service Fabric – Új projekt, 5. oldal][create-application/p5]

6.  Így néz ki az új projekt:

    ![Service Fabric – Új projekt, 6. oldal][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Service Fabric-alkalmazás létrehozása az Eclipse-ben

1.  Kattintson a jobb gombbal az új Service Fabric-alkalmazásra, majd válassza a **Service Fabric** lehetőséget.

    ![Service Fabric – helyi menü][publish/RightClick]

2. A helyi menüben válassza az alábbi lehetőségek egyikét:
    -   Az alkalmazás tisztítás nélküli kiépítéséhez kattintson a **Build Application** (Alkalmazás buildelése) parancsra.
    -   Az alkalmazás tiszta buildjének kiépítéséhez kattintson a **Rebuild Application** (Alkalmazás újrabuildelése) parancsra.
    -   A kiépített összetevők törléséhez az alkalmazásból kattintson a **Clean Application** (Alkalmazás tisztítása) parancsra.

## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Service Fabric alkalmazás üzembe helyezése a helyi fürtön az Eclipse használatával

Miután létrehozta a Service Fabric alkalmazást, kövesse az alábbi lépéseket a helyi fürtön való üzembe helyezéséhez.

1. Ha még nem indította el a helyi fürtöt, kövesse a helyi fürt [beállítása](./service-fabric-get-started-linux.md#set-up-a-local-cluster) a helyi fürt elindításához című témakör útmutatását, és győződjön meg arról, hogy az fut.
2. Kattintson a jobb gombbal a Service Fabric alkalmazásra, majd válassza a **Service Fabric** lehetőséget.

    ![Service Fabric – helyi menü][publish/RightClick]

3.  A helyi menüben kattintson az **alkalmazás központi telepítése** elemre.
4.  Az üzembe helyezési művelet előrehaladását a konzol ablakban követheti el.
5.  Az alkalmazás futásának ellenőrzéséhez nyissa meg Service Fabric Explorer a helyi fürtön egy böngészőablakban `http://localhost:19080/Explorer` . Bontsa ki az **alkalmazások** csomópontot, és ellenőrizze, hogy fut-e az alkalmazás. 

Az alkalmazásnak az Eclipse-ben a helyi fürtön való hibakereséséről további információt a [Java-szolgáltatás hibakeresése az Eclipse-ben](./service-fabric-debugging-your-application-java.md)című témakörben talál.

Az alkalmazást a helyi fürtön is üzembe helyezheti az **alkalmazás közzététele** paranccsal:

1. Kattintson a jobb gombbal a Service Fabric alkalmazásra, majd válassza a **Service Fabric** lehetőséget.
2. A helyi menüben kattintson az **alkalmazás közzététele..**. elemre.
3. Az **alkalmazás közzététele** ablakban válassza a **célprofilnak publishprofiles/Local.js** elemet a célként megadott profil területen, majd kattintson a **Közzététel** gombra.

    ![Publish (Közzététel) helyi párbeszédpanel](./media/service-fabric-get-started-eclipse/localjson.png)

    Alapértelmezés szerint a közzétételi profil Local.jsbe van állítva a helyi fürtre való közzétételre. A közzétételi profilokban található kapcsolatok és végponti paraméterekkel kapcsolatos további információkért tekintse meg a következő szakaszt.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Service Fabric alkalmazás közzététele az Azure-ban az Eclipse használatával

Az alkalmazás felhőben való közzétételéhez kövesse az alábbi lépéseket:

1. Az alkalmazás Felhőbeli biztonságos fürtön való közzétételéhez egy X. 509 tanúsítványra van szükség, amely a fürttel való kommunikációhoz használható. A tesztelési és fejlesztési környezetekben a használt tanúsítvány gyakran a fürt tanúsítványa. Éles környezetekben a tanúsítványnak olyan ügyféltanúsítványt kell tartalmaznia, amely nem azonos a fürt tanúsítványával. A tanúsítványra és a titkos kulcsra is szükség van. A tanúsítvány (és a kulcs) fájljának PEM formátumúnak kell lennie. Létrehozhat egy olyan PEM-fájlt, amely tartalmazza a tanúsítványt és a titkos kulcsot egy PFX-fájlból a következő OpenSSL paranccsal:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Ha a PFX-fájl jelszava nem védett, használja az `--passin pass:` utolsó paramétert.

2. Nyissa meg a **Cloud.js** fájlt a **célprofilnak publishprofiles** könyvtár alatt. Konfigurálnia kell a fürt végpontját és a fürthöz megfelelő biztonsági hitelesítő adatokat.

   - A `ConnectionIPOrURL` mező a fürt IP-címét vagy URL-címét tartalmazza. Vegye figyelembe, hogy az érték nem tartalmazza az URL-sémát ( `https://` ).
   - Alapértelmezés szerint a `ConnectionPort` mezőnek a `19080` következőnek kell lennie, ha explicit módon nem módosította ezt a portot a fürthöz.
   - A `ClientKey` mezőnek a helyi gépen lévő, PEM formátumú. PEM vagy. Key fájlra kell mutatnia, amely tartalmazza az ügyfél vagy a fürt tanúsítványának titkos kulcsát.
   - A `ClientCert` mezőnek a helyi gépen lévő PEM-formázott. PEM vagy. CRT fájlra kell mutatnia, amely tartalmazza az ügyfél vagy a fürt tanúsítványának adatait. tanúsítvány. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Kattintson a jobb gombbal a Service Fabric alkalmazásra, majd válassza a **Service Fabric** lehetőséget.
3. A helyi menüben kattintson az **alkalmazás közzététele..**. elemre.
3. Az **alkalmazás közzététele** ablakban válassza a **célprofilnak publishprofiles/Cloud.js** elemet a célként megadott profil területen, majd kattintson a **Közzététel** gombra.

    ![A felhő közzétételi párbeszédablaka](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. A közzétételi művelet előrehaladását a konzol ablakban követheti el.
5. Az alkalmazás futásának ellenőrzéséhez nyissa meg Service Fabric Explorer az Azure-fürtön egy böngészőablakban. A fenti példában a következő lenne: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer` . Bontsa ki az **alkalmazások** csomópontot, és ellenőrizze, hogy fut-e az alkalmazás. 

Ha az alkalmazás Reliable Services szolgáltatásokat tartalmaz, a biztonságos Linux-fürtökön konfigurálnia kell egy tanúsítványt, amelyet a szolgáltatások használhatnak Service Fabric futtatókörnyezeti API-k meghívásához. További információ: [Reliable Services alkalmazás konfigurálása Linux-fürtökön való futtatásra](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Ha szeretne egy gyors áttekintést arról, hogyan helyezhet üzembe egy Java-ban írt Service Fabric Reliable Services alkalmazást egy biztonságos Linux-fürtön, tekintse meg a rövid útmutató [: java Reliable Services-alkalmazás üzembe helyezése](./service-fabric-quickstart-java-reliable-services.md)című témakört.

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Service Fabric alkalmazás üzembe helyezése Eclipse futtatási konfigurációk használatával

A Service Fabric-alkalmazások üzembe helyezésének másik módszere, ha Eclipse futtatási konfigurációkat használ.

1. Az Eclipse-ben lépjen a futtatási konfiguráció **futtatása** elemre  >  .
2. A **Gradle Project** (Gradle-projekt) területen válassza a **ServiceFabricDeployer** futtatási konfigurációt.
3. A jobb oldali ablaktáblában az **argumentumok** lapon győződjön meg arról, hogy az IP-, **port**-, **clientCert**-és **clientKey** **-** paraméterek megfelelően vannak beállítva az üzemelő példányhoz. Alapértelmezés szerint a paraméterek a helyi fürtre való központi telepítésre vannak beállítva, az alábbi képernyőképen látható módon. Az alkalmazás az Azure-ban való közzétételéhez módosíthatja a paramétereket, hogy tartalmazza a végpont adatait és az Azure-fürthöz tartozó biztonsági hitelesítő adatokat. További információ: az előző szakasz, a [Service Fabric alkalmazás közzététele az Azure](#publish-your-service-fabric-application-to-azure-with-eclipse)-ban az Eclipse használatával.

    ![A konfiguráció párbeszédpanel helyi futtatása](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Győződjön meg arról, hogy a **Munkakönyvtár** a központilag telepíteni kívánt alkalmazásra mutat. Az alkalmazás módosításához kattintson a **Workspace** (Munkaterület) gombra, és válassza ki a kívánt alkalmazást.
6. Kattintson az **Apply** (Alkalmaz), majd a **Run** (Futtatás) gombra.

Néhány másodpercen belül megtörténik az alkalmazás felépítése és üzembe helyezése. Az üzembe helyezés állapotát a Service Fabric Explorerben követheti nyomon.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric-szolgáltatás hozzáadása a Service Fabric-alkalmazáshoz

Ha Service Fabric-szolgáltatást szeretne hozzáadni egy meglévő Service Fabric-alkalmazáshoz, végezze el a következő lépéseket:

1.  Kattintson a jobb gombbal a projektre, amelyhez hozzá szeretne adni egy szolgáltatást, majd kattintson a **Service Fabric** elemre.

    ![Service Fabric – Szolgáltatás hozzáadása, 1. oldal][add-service/p1]

2.  Kattintson az **Add Service Fabric Service** (Service Fabric-szolgáltatás hozzáadása) parancsra, és végezze el a szolgáltatás hozzáadásának lépéseit.
3.  Válassza ki a projekthez hozzáadni kívánt szolgáltatássablont, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Szolgáltatás hozzáadása, 2. oldal][add-service/p2]

4.  Adja meg a szolgáltatás nevét (és a többi szükséges adatot), majd kattintson az **Add Service** (Szolgáltatás hozzáadása) gombra.  

    ![Service Fabric – Szolgáltatás hozzáadása, 3. oldal][add-service/p3]

5.  A szolgáltatás hozzáadása után a teljes projektstruktúra a következőhöz hasonlóan néz ki:

    ![Service Fabric – Szolgáltatás hozzáadása, 4. oldal][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>A Service Fabric Java-alkalmazás jegyzékverzióinak szerkesztése

Jegyzékverziók szerkesztéséhez kattintson jobb gombbal a projektre, majd a legördülő menüből válassza a **Service Fabric** alatt az **Edit Manifest Versions...** (Jegyzékverziók szerkesztése) pontot. A varázslóval frissítheti az alkalmazásjegyzék, szolgáltatásjegyzék, valamint a **Code** (Kód-), **Config** (Konfigurációs-) és **Data** (Adat-) csomagok verzióit.

Ha bejelöli az **Automatically update application and service versions** (Alkalmazás- és szolgáltatás-verziók automatikus frissítése) lehetőséget, akkor a verziófrissítéskor a jegyzékverziók is automatikusan frissülnek. Ha például előre bejelöli a jelölőnégyzetet, majd 0.0.0-ról 0.0.1-re frissíti a **Code** verzióját és a **Finish** gombra kattint, akkor a szolgáltatásjegyzék és alkalmazásjegyzék verziója is automatikusan 0.0.1-re frissül.

## <a name="upgrade-your-service-fabric-java-application"></a>A Service Fabric Java-alkalmazásának frissítése

Frissítési forgatókönyv esetén tegyük fel, hogy az **App1** projektet hozta létre a Service Fabric beépülő modullal az Eclipse-ben. A projektet a beépülő modullal helyezte üzembe a **fabric:/App1Application** nevű alkalmazás létrehozásához. Az alkalmazás típusa **App1ApplicationType**, és az alkalmazás verziószáma 1,0. A rendelkezésre állás megszakítása nélkül szeretné frissíteni az alkalmazást.

Először végezzen módosítást az alkalmazáson, majd építse újra a módosított szolgáltatást. Frissítse a módosított szolgáltatás jegyzékfájlját (ServiceManifest.xml) a szolgáltatás frissített verzióival (és a megfelelő Code, Config vagy Data értékkel). Módosítsa az alkalmazás jegyzékfájlját is (ApplicationManifest.xml) az alkalmazás frissített verziószámával és a módosított szolgáltatással.  

Ha az Eclipse-szel szeretné frissíteni az alkalmazást, létrehozhat egy duplikált futtatáskonfigurációs profilt, amelyet aztán szükség szerint az alkalmazás frissítésére használhat.

1.  Lépjen a   >  **futtatási konfigurációk** futtatása gombra. A bal oldali ablaktáblában kattintson a **Gradle Project** (Gradle-projekt) bal oldalán található kis nyílra.
2.  Kattintson a jobb gombbal a **ServiceFabricDeployer** elemre, majd válassza a **Duplicate** (Megkettőzés) parancsot. Adjon egy új nevet a konfigurációnak, például **ServiceFabricUpgrader**.
3.  A jobb oldali ablaktáblán, az **Arguments** (Argumentumok) lapon módosítsa a **-Pconfig='deploy'** értéket **-Pconfig='upgrade'** értékre, majd kattintson az **Apply** (Alkalmaz) gombra.

Ez a folyamat olyan futtatási konfigurációs profilt hoz létre és ment el, amellyel bármikor frissítheti az alkalmazást. Az alkalmazás jegyzékfájljából a legújabb frissített alkalmazástípus-verziót is lekéri.

Az alkalmazás frissítése eltarthat néhány percig. Az alkalmazás frissítésének folyamatát a Service Fabric Explorerben követheti nyomon.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>A Mavennel használni kívánt régi Service Fabric Java-alkalmazások migrálása
Nemrégiben áthelyeztük a Service Fabric Java-kódtárakat a Service Fabric Java SDK-ból a Mavenen futó adattárba. Az Eclipse-szel létrehozott új alkalmazások a legfrissebb projekteket hozzák létre (amelyek képesek együttműködni a Mavennel), a meglévő állapotmentes vagy aktor Service Fabric Java-alkalmazások pedig, amelyek korábban a Service Fabric Java SDK-t használták, frissíthetők a Mavenben található Service Fabric Java-függőségek használatára. Kövesse az [itt](service-fabric-migrate-old-javaapp-to-use-maven.md) felsorolt lépéseket, ha biztosítani kívánja, hogy a régebbi alkalmazásaik együttműködjenek a Mavennel.

## <a name="next-steps"></a>Következő lépések

- A Java megbízható szolgáltatásalkalmazás létrehozásának és helyi és Azure-beli üzembe helyezésének gyors lépéseiért lásd: rövid útmutató [: java Reliable Services-alkalmazás üzembe helyezése](./service-fabric-quickstart-java-reliable-services.md).
- A Java-alkalmazások helyi fürtön való hibakeresésével kapcsolatban lásd: [Java-szolgáltatás hibakeresése az Eclipse-ben](./service-fabric-debugging-your-application-java.md).
- A Service Fabric-alkalmazások figyelésének és diagnosztizálásának megismeréséhez tekintse meg [a szolgáltatások figyelése és diagnosztizálása helyi számítógép-fejlesztési beállításban](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)című témakört.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
