---
title: ILB-beadás létrehozása ARM-mel
description: Megtudhatja, hogyan hozhat létre App Service környezetet belső terheléselosztó (ILB) használatával Azure Resource Manager sablonokkal. Az alkalmazások teljes elkülönítése az internetről.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 27c9198558a730d0af49077d6f5baa6db4789416
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009551"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Belső Load Balancer létrehozása és használata App Service Environment 

A Azure App Service Environment a Azure App Service üzembe helyezése egy Azure-beli virtuális hálózat (VNet) alhálózatában. Az App Service Environment (ASE) üzembe helyezésének két módja van: 

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- Egy virtuális IP-cím vagy belső IP-cím, azaz ILB ASE használatával, amelyet azért neveznek így, mert a belső végpont egy belső terheléselosztó (ILB). 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy ILB ASE környezetet. A bevezetőt a következő témakörben tekintheti át: [app Service környezetek bemutatása][Intro]. A külső ASE létrehozásának folyamatát a [külső ASE létrehozását][MakeExternalASE] ismertető témakor tartalmazza.

## <a name="overview"></a>Áttekintés 

Az ASE környezetet közvetlenül az internethez csatlakozó végponttal vagy a virtuális hálózatán belüli IP-címmel is telepítheti. Ahhoz, hogy egy IP-címet hozzárendeljen egy virtuális hálózati címhez, az ASE környezetet ILB terheléskiegyenlítővel kell üzembe helyezni. Ha a ILB-mel telepíti a bevezetést, meg kell adnia a beadási pont nevét. A Bea benyújtó alkalmazásának tartomány-utótagjában a központjának nevét használjuk.  A ILB-alapú adatszolgáltatáshoz tartozó tartományi utótag a következő: " &lt; &gt; appserviceenvironment.net". Az ILB-előkészítőben létrehozott alkalmazások nem kerülnek be a nyilvános DNS-be. 

A ILB beadásának korábbi verzióiban meg kell adnia egy tartományi utótagot és egy alapértelmezett tanúsítványt a HTTPS-kapcsolatokhoz. A tartomány utótagját a rendszer már nem gyűjti a ILB-ben, és egy alapértelmezett tanúsítványt is már nem gyűjt. Ha most létrehoz egy ILB-beadást, az alapértelmezett tanúsítványt a Microsoft látja el, és a böngésző megbízhatónak tekinti. Továbbra is beállíthatja az egyéni tartományneveket a beadásban lévő alkalmazásokban, és beállíthat tanúsítványokat az egyéni tartománynevek számára. 

A ILB-t a következőképpen teheti meg:

-   A felhőben biztonságosan üzemeltetheti intranetes alkalmazásait, amely helyek közötti vagy ExpressRoute keresztül érhető el.
-   Alkalmazások WAF-eszközzel történő védelemmel való ellátása
-   A nyilvános DNS-kiszolgálókon nem szereplő alkalmazásokat üzemeltethet a felhőben.
-   Internettől elzárt háttéralkalmazásokat hozhat létre, amelyekkel az előtéri alkalmazások biztonságosan integrálódhatnak.

### <a name="disabled-functionality"></a>Letiltott funkciók ###

Néhány dolog, amit nem tehet meg ILB ASE használata esetén:

-   IP-alapú SSL használata.
-   IP-címek hozzárendelése az egyes alkalmazásokhoz.
-   Tanúsítvány vásárlása és használata egy alkalmazáshoz az Azure Portalon keresztül. A tanúsítványokat közvetlenül hitelesítésszolgáltatótól szerezheti be, és azokat használhatja az alkalmazásaihoz. Az Azure Portalon keresztül nem szerezheti be őket.

## <a name="create-an-ilb-ase"></a>ILB ASE létrehozása ##

Az ILB ASE létrehozása:

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **webes**  >  **app Service Environment** lehetőséget.

2. Válassza ki előfizetését.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

4. Adja meg a App Service Environment nevét.

5. Válassza ki a belső virtuális IP-típust.

    ![ASE létrehozása](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> A App Service Environment neve nem lehet hosszabb 37 karakternél.

6. Hálózatkezelés kiválasztása

7. Válasszon ki vagy hozzon létre egy Virtual Network. Ha itt hoz létre új VNet, akkor a rendszer a 192.168.250.0/23 címtartomány alapján határozza meg. Ha a VNet egy másik címtartományból vagy egy másik erőforráscsoporthoz szeretné létrehozni, mint a szolgáltató, használja az Azure Virtual Network létrehozási portált. 

8. Válasszon ki vagy hozzon létre egy üres alhálózatot. Ha ki szeretne választani egy alhálózatot, üresnek kell lennie, és nincs delegálva. Az alhálózat mérete nem módosítható a bekapcsolási szolgáltatás létrehozása után. Mi a `/24` méretet javasoljuk, amely 256 címet tartalmaz, és képes kezelni egy maximális méretű ASE környezetet és tetszőleges skálázási igényeket. 

    ![Bekapcsolási hálózat][1]

7. Válassza **a felülvizsgálat és létrehozás, majd a** **Létrehozás** lehetőséget.


## <a name="create-an-app-in-an-ilb-ase"></a>Alkalmazás létrehozása az ILB ASE környezetben ##

Az ILB ASE környezetben a sima ASE környezettel megegyező módon hozhat létre alkalmazást.

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **webes**  >  **webes alkalmazás** lehetőséget.

1. Adja meg az alkalmazás nevét.

1. Válassza ki az előfizetést.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

1. Válassza ki a közzétételi, futtatókörnyezeti verem és operációs rendszer elemet.

1. Válassza ki azt a helyet, ahol a hely egy meglévő ILB.  Az alkalmazás létrehozása során új, elkülönített App Service terv kiválasztásával is létrehozhatja az alkalmazást. Ha új beadást szeretne létrehozni, válassza ki azt a régiót, amelyben létre kívánja hozni a központot.

1. Válassza ki vagy hozzon létre egy App Service-csomagot. 

1. Válassza a **felülvizsgálat és létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget, ha elkészült.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs-feladatok, a Functions és az ILB ASE 

Az ILB ASE támogatja a Functionst és a WebJobs-feladatokat is, de ahhoz, hogy a portál is működjön velük, rendelkeznie kell hálózati hozzáféréssel egy SCM helyhez.  Ez azt jelenti, hogy a böngészőnek olyan gazdagépen kell lennie, amely csatlakozik a virtuális hálózathoz, vagy azon belül van. Ha a ILB-előkészítés olyan tartománynevet tartalmaz, amely nem a *appserviceenvironment.net* végződik, akkor a böngészővel meg kell bíznia az SCM-hely által használt HTTPS-tanúsítványt.

## <a name="dns-configuration"></a>DNS-konfiguráció 

Ha külső beadást használ, a szolgáltatón keresztül készített alkalmazások regisztrálva vannak Azure DNSban. Az alkalmazások nyilvánosan elérhetővé tételéhez nincs szükség további lépésekre. A ILB bevezetésével a saját DNS-t kell kezelnie. Ezt megteheti a saját DNS-kiszolgálójában vagy Azure DNS privát zónában.

A DNS konfigurálása a saját DNS-kiszolgálójában a ILB-alapú központtal:

1. hozzon létre egy zónát a benyújtó &lt; neve számára &gt; . appserviceenvironment.net
2. hozzon létre egy olyan rekordot az adott zónában, amely * a ILB IP-címére mutat.
3. hozzon létre egy olyan rekordot az adott zónában, amely a @-t a ILB IP-címére mutat.
4. hozzon létre egy zónát a központhoz tartozó &lt; név &gt; . appserviceenvironment.net neve: SCM
5. hozzon létre egy olyan rekordot az SCM-zónában, amely * a ILB IP-címére mutat.

A DNS konfigurálása Azure DNS privát zónában:

1. hozzon létre egy Azure DNS privát zónát, amely &lt; neve &gt; . appserviceenvironment.net
2. hozzon létre egy olyan rekordot az adott zónában, amely * a ILB IP-címére mutat.
3. hozzon létre egy olyan rekordot az adott zónában, amely a @-t a ILB IP-címére mutat.
4. hozzon létre egy olyan rekordot az adott zónában, amely a *. SCM-t a ILB IP-címére mutat.

A szolgáltatói alapértelmezett tartomány utótagjának DNS-beállításai nem korlátozzák, hogy az alkalmazások csak az adott nevek számára legyenek elérhetők. Egyéni tartománynevet az alkalmazások érvényesítése nélkül is beállíthat egy ILB-ben. Ha ezután létre szeretne hozni egy contoso.net nevű zónát, ezt megteheti, és rámutathat a ILB IP-címére. Az Egyéni tartománynév az alkalmazásra vonatkozó kérelmek esetében működik, de nem az SCM-helyhez. Az SCM-hely csak a &lt; AppName &gt; . SCM-ben érhető el. &lt; asename &gt; . appserviceenvironment.net.

A nevű zóna. &lt; &gt;a asename. appserviceenvironment.net globálisan egyedi. A 2019. május előtt az ügyfelek megadhatják a ILB beadásának tartományi utótagját. Ha a. contoso.com-t szeretné használni a tartománynév-utótaghoz, akkor ezt megteheti, és ez magában foglalja az SCM-helyet. A modellel kapcsolatos kihívások is megtalálhatók; az alapértelmezett SSL-tanúsítvány kezelése, az egyszeri bejelentkezés hiánya az SCM-hellyel, valamint a helyettesítő tanúsítvány használatára vonatkozó követelmény. A ILB-ben használt alapértelmezett tanúsítvány-frissítési folyamat megszakadt, és az alkalmazás újraindítását is okozta. Ezeknek a problémáknak a megoldásához a ILB-bevezetési viselkedés úgy módosult, hogy a szolgáltató neve és a Microsoft tulajdonában lévő utótag alapján egy tartományi utótagot használjon. A ILB-beli beváltási viselkedés változása csak a 2019 májusi ILB-ASE érinti. A meglévő ILB-ASE továbbra is kezelni kell a bevezetési és DNS-konfigurációjuk alapértelmezett tanúsítványát.

## <a name="publish-with-an-ilb-ase"></a>Közzététel ILB ASE környezetben

Minden létrehozott alkalmazásnak két végpontja van. Egy ILB-ben az *&lt; alkalmazás neve szerepel &gt; . &lt; ILB &gt;* és az *&lt; alkalmazás neve &gt; . SCM. &lt; ILB &gt;*. 

Az SCM helynév a Kudu konzolhoz irányítja, az Azure Portal **Speciális portál** részére. A Kudu konzol a környezeti változók megtekintését, a lemez vizsgálatát, a konzol használatát és még sok más funkciót kínál. További információ: [Kudu konzol az Azure App Service-ben][Kudu]. 

A GitHubhoz és az Azure DevOpshoz hasonló internetes alapú CI-rendszerek akkor is működnek az ILB ASE környezettel, ha a buildügynök elérhető az internetről, és ugyanazon a hálózaton található, mint az ILB ASE. Tehát az Azure DevOps esetében, ha a buildügynök ugyanazon a VNET-en lett létrehozva, mint az ILB ASE (lehet más alhálózat), képes lesz lekérni a kódot az Azure DevOps-gitről és telepíteni az ILB ASE-re. Ha nem szeretne létrehozni saját buildügynököt, egy lekérési modellt használó CI-rendszert kell használnia helyette, például a Dropboxot.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Ez a tartomány az alkalmazás közzétételi profiljában és az alkalmazás portál paneljén jelenik meg (az **Áttekintés**  >  **Essentials** és a **Properties** is). Ha van egy ILB, amely a (z *&lt; &gt; ) "appserviceenvironment.net*" nevű tartománynév-utótagot és egy *mytest* nevű alkalmazást használ, használja a *mytest. &lt; Bevezetési név &gt; . APPSERVICEENVIRONMENT.net* FTP-hez és *mytest.SCM.contoso.net* webes telepítéshez.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>ILB-beadás konfigurálása WAF-eszközzel ##

A webalkalmazási tűzfal (WAF) és a ILB-elősegítő eszközök összevonásával csak az interneten található alkalmazásokat teheti elérhetővé, és a többiet csak a VNet érheti el. Ez lehetővé teszi, hogy többek között biztonságos többrétegű alkalmazásokat építsen ki.

Ha többet szeretne megtudni arról, hogyan konfigurálhatja a ILB a WAF eszközzel, tekintse meg a [webalkalmazási tűzfal konfigurálása a app Service-környezettel][ASEWAF]című témakört. Ez a cikk leírja, hogyan használhatja együtt a Barracuda virtuális berendezést az ASE környezettel. Másik lehetőségként az Azure Application Gateway is használható. Az Application Gateway az alapvető OWASP-szabályokat használja a mögötte elhelyezett alkalmazások biztonságossá tételére. Az Application Gatewayről további információt az [Azure webalkalmazási tűzfal bevezető][AppGW] cikke tartalmaz.

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASE a május 2019. előtt

Az 2019. május előtt elkészített ILB-ASE a tartomány utótagjának a központilag történő létrehozása során történő beállításához szükséges. Emellett az adott tartományi utótagon alapuló alapértelmezett tanúsítvány feltöltésére is szükség van. Emellett egy régebbi ILB-benyújtó használatával nem végezheti el az egyszeri bejelentkezést a kudu-konzolon a ILB-elősegítő alkalmazásokkal. Ha a DNS-t egy régebbi ILB-alapú központhoz konfigurálja, akkor egy olyan rekordot kell beállítania egy olyan zónában, amely megfelel a tartomány utótagjának. 

## <a name="get-started"></a>Bevezetés ##

* Az ASE használatával kapcsolatos első lépésekről [Az App Service Environment bemutatása][Intro] témakörben olvashat. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
