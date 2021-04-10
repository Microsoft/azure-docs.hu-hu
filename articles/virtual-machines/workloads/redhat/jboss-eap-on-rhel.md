---
title: Gyors útmutató – JBoss Enterprise Application Platform (EAP) üzembe helyezése Red Hat Enterprise Linux (RHEL) Azure-beli virtuális gépekre és virtuálisgép-méretezési csoportokra
description: Vállalati Java-alkalmazások üzembe helyezése a Red Hat JBoss EAP használatával az Azure RHEL-alapú virtuális gépeken és a virtuálisgép-méretezési csoportokban.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: 4027e9c336d938c3877e56b0eb59edea6ac1d992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671978"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Vállalati Java-alkalmazások üzembe helyezése az Azure-on a JBoss EAP használatával Red Hat Enterprise Linux rendszeren

Az ebben a cikkben található Azure rövid útmutatók bemutatják, hogyan helyezhet üzembe a [JBoss Enterprise Application platformot (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) a [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) használatával Azure-beli virtuális gépeken (VM) és virtuálisgép-méretezési csoportokon. A telepítés ellenőrzéséhez egy minta Java-alkalmazást fog használni. 

A JBoss EAP egy nyílt forráskódú alkalmazáskiszolgáló platform. Nagyvállalati szintű biztonságot, méretezhetőséget és teljesítményt nyújt Java-alkalmazásai számára. A RHEL egy nyílt forráskódú operációs rendszer (OS) platform. Lehetővé teszi a meglévő alkalmazások méretezését és az új technológiák kiépítését minden környezetben. 

A JBoss EAP és a RHEL tartalmaz mindent, amire szüksége lehet a vállalati Java-alkalmazások bármilyen környezetben történő létrehozásához, futtatásához, üzembe helyezéséhez és kezeléséhez. A kombináció egy nyílt forráskódú megoldás helyszíni, virtuális környezetekben, valamint magán-, nyilvános vagy hibrid felhőkben.

## <a name="prerequisites"></a>Előfeltételek 

* Aktív előfizetéssel rendelkező Azure-fiók. Azure-előfizetés beszerzéséhez aktiválja az [Azure-krediteket a Visual Studio-előfizetőknek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) , vagy [hozzon létre ingyen egy fiókot](https://azure.microsoft.com/pricing/free-trial).

* JBoss EAP-telepítés. Egy Red Hat-fiókra van szüksége a JBoss EAP számára a Red Hat előfizetés-kezelési (RHSM) jogosultsággal. Ez a jogosultság lehetővé teszi a Red Hat tesztelt és hitelesített JBoss EAP verzió letöltését.  

  Ha nem rendelkezik EAP-jogosultsággal, a Kezdés előtt szerezzen be egy [JBoss EAP próbaverziós előfizetést](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) . Új Red Hat-előfizetés létrehozásához nyissa meg a [Red Hat Customer Portalt](https://access.redhat.com/) , és állítson be egy fiókot.

* Az [Azure CLI](/cli/azure/overview)-vel.

* RHEL beállításai. Válassza az utólagos elszámolású (TB) vagy a saját előfizetés (BYOS) lehetőséget. A BYOS-mel aktiválnia kell a [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold rendszerképet a gyors üzembe helyezési sablon telepítése előtt.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE és Jakarta EE alkalmazás migrálása

### <a name="migrate-to-jboss-eap"></a>Migrálás a JBoss EAP-re
A JBoss EAP 7,2 és a 7,3 a Java Enterprise Edition (Java EE) 8 és a Jakarta EE 8 specifikációjának hitelesített implementációja. A JBoss EAP előre konfigurált beállításokat biztosít olyan funkciókhoz, mint például a magas rendelkezésre állás (HA) fürtözés, az üzenetkezelés és az elosztott gyorsítótárazás. Azt is lehetővé teszi, hogy a felhasználók a JBoss EAP által biztosított különféle API-kkal és szolgáltatásokkal írják be, telepítsenek és futtassanak alkalmazásokat.  

A JBoss EAP-ről további információt a JBoss EAP [7,2 bemutatása](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) vagy a [JBoss EAP 7,3 bemutatása](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)című témakörben talál.

 #### <a name="applications-on-jboss-eap"></a>Alkalmazások a JBoss EAP-ben

* **Webszolgáltatások alkalmazásai**. A webszolgáltatások szabványos módszert biztosítanak a szoftverek közötti együttműködésre. Minden alkalmazás különböző platformokon és keretrendszereken futtatható. Ezek a webszolgáltatások megkönnyítik a belső és heterogén alrendszer kommunikációját. 

  További információért lásd: [webszolgáltatási alkalmazások fejlesztése az eap 7,2-on](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) , vagy [webszolgáltatási alkalmazások fejlesztése az EAP 7,3-on](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **Enterprise Java Bean-(EJB-) alkalmazások**. A EJB 3,2 egy olyan API, amely elosztott, tranzakciós, biztonságos és hordozható Java EE-és Jakarta EE-alkalmazások fejlesztésére szolgál. A EJB a vállalati bab nevű kiszolgálóoldali összetevőket használja egy alkalmazás üzleti logikájának megvalósításához egy olyan leválasztott módon, amely ösztönzi az újrafelhasználást. 

  További információ: EJB- [alkalmazások fejlesztése az eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) -ben vagy [EJB-alkalmazások fejlesztése az EAP 7,3-on](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Alkalmazások hibernálása**. A fejlesztők és a rendszergazdák létrehozhatnak és telepíthetnek Java perzisztencia API-t (KÖZÖSen) és hibernált alkalmazásokat a JBoss EAP használatával. A hibernálási mag egy objektum-összehasonlító leképezési keretrendszer a Java nyelvhez. Keretet biztosít egy objektumorientált tartományi modell hozzárendeléséhez egy kapcsolati adatbázishoz, így az alkalmazások el tudják kerülni a közvetlen interakciót az adatbázissal. 

  A hibernálási entitások kezelője a [közös parlamenti közgyűlés 2,1 specifikációja](https://www.jcp.org/en/jsr/overview)által meghatározott programozási felületek és életciklus-szabályok megvalósítására szolgál. A hibernálási megjegyzésekkel együtt a burkoló egy teljes (és önálló) közös parlamenti megoldást valósít meg a kiforrott hibernálási mag felett. 
  
  A hibernálással kapcsolatos további információkért lásd: [a közös parlamenti közgyűlés az eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) vagy  [a jakartai adatmegőrzéssel kapcsolatban az EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit alkalmazások számára
A [Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) a Java-alkalmazáskiszolgáló áttelepítési eszköze. Ezzel az eszközzel áttelepítheti egy másik alkalmazás-kiszolgálóról a JBoss EAP-re. Az [Eclipse ide](https://www.eclipse.org/ide/), a [Red Hat CodeReady-munkaterületek](https://developers.redhat.com/products/codeready-workspaces/overview), valamint a Java-hoz készült [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) ide beépülő moduljaival működik. 

Az MTA egy ingyenes és nyílt forráskódú eszköz, amely:
* Automatizálja az alkalmazások elemzését.
* Támogatja a tevékenység értékelését.
* Felgyorsítja a kód áttelepítését.
* Támogatja a tárolókra bontás.
* Integrálható az Azure munkaterhelés-szerkesztővel.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>A JBoss EAP migrálása a helyszínről az Azure-ba
A JBoss EAP on RHEL Azure Marketplace-ajánlata kevesebb, mint 20 perc alatt telepíthető és kiépíthető az Azure-beli virtuális gépeken. Ezek az ajánlatok az [Azure Marketplace](https://azuremarketplace.microsoft.com/)-en érhetők el.

Ez az Azure Marketplace-ajánlat az EAP és a RHEL különböző verzióinak különféle kombinációit tartalmazza a követelmények támogatásához. A JBoss EAP mindig BYOS, de a RHEL operációs rendszer esetében a BYOS vagy a TB közül választhat. Az Azure Marketplace ajánlata magában foglalja a RHEL-hez önálló vagy fürtözött virtuális gépekként kínált JBoss EAP tervezési lehetőségeit:

* JBoss EAP 7,2 a RHEL 7,7 virtuális gépen (TB)
* JBoss EAP 7,2 a RHEL 8,0 virtuális gépen (TB)
* JBoss EAP 7,3 a RHEL 8,0 virtuális gépen (TB)
* JBoss EAP 7,2 a RHEL 7,7 virtuális gépen (BYOS)
* JBoss EAP 7,2 a RHEL 8,0 virtuális gépen (BYOS)
* JBoss EAP 7,3 a RHEL 8,0 virtuális gépen (BYOS)

Az Azure Marketplace-ajánlatok mellett a gyors üzembe helyezési sablonokkal is elsajátíthatja az Azure-beli áttelepítést. Ezek a rövid útmutatók előre összeállított Azure Resource Manager (ARM) sablonokat és parancsfájlokat tartalmaznak, amelyekkel a JBoss EAP-t telepítheti a RHEL különböző konfigurációk és verziók kombinációjában. A következőkre lesz szüksége:

* Egy Load Balancer.
* Magánhálózati IP-cím a terheléselosztáshoz és a virtuális gépekhez.
* Egyetlen alhálózattal rendelkező virtuális hálózat.
* Virtuális gép konfigurációja (fürt vagy önálló).
* Egy minta Java-alkalmazás.

A sablonok megoldási architektúrája a következőket tartalmazza:

* JBoss EAP önálló RHEL virtuális gépen.
* A JBoss EAP több RHEL virtuális gépen üzemel.
* A JBoss EAP az Azure virtuálisgép-méretezési csoportokon keresztül üzemel.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Linux munkaterhelés-áttelepítés a JBoss EAP-hez
Az Azure munkaterhelés-szerkesztő leegyszerűsíti a helyszíni Java-alkalmazások számára az Azure-hoz való megvalósíthatósági, kiértékelési és áttelepítési folyamatokat. A munkaterhelés-szerkesztő integrálható a Azure Migrate felderítési eszközzel a JBoss EAP-kiszolgálók azonosításához. Ezután dinamikusan generál egy Ansible-forgatókönyvet a JBoss EAP-kiszolgáló üzembe helyezéséhez. A Red Hat MTA eszközt használja a kiszolgálók más alkalmazás-kiszolgálókról a JBoss EAP-re való áttelepíteni. 

A Migrálás egyszerűsítésének lépései a következők:
1. **Kiértékelés**. Egy Azure-beli virtuális gép vagy egy virtuálisgép-méretezési csoport használatával kiértékelheti a JBoss EAP-fürtöket.
1. **Értékelés**. Alkalmazások és infrastruktúra vizsgálata.
1. **Infrastruktúra-konfiguráció**. Hozzon létre egy munkaterhelés-profilt.
1. **Üzembe helyezés és tesztelés**. A munkaterhelés üzembe helyezése, áttelepítése és tesztelése.
1. **Telepítés utáni konfiguráció**. Integrálhatja az adatgyűjtést, a figyelést, a biztonságot, a biztonsági mentést és egyebeket.

## <a name="server-configuration-choice"></a>Kiszolgáló konfigurációjának választása

A RHEL virtuális gép üzembe helyezéséhez a TB vagy a BYOS is választhatja. Az [Azure Marketplace](https://azuremarketplace.microsoft.com) -ről származó rendszerképek alapértelmezése a TB. Helyezzen üzembe egy BYOS típusú RHEL virtuális gépet, ha rendelkezik saját RHEL operációsrendszer-lemezképpel. A virtuális gép vagy virtuálisgép-méretezési csoport üzembe helyezése előtt győződjön meg arról, hogy a RHSM-fiókja BYOS jogosultsággal rendelkezik a Felhőbeli hozzáférésen keresztül.

A JBoss EAP hatékony felügyeleti képességekkel rendelkezik, valamint funkciókat és API-kat biztosít alkalmazásai számára. Ezek a felügyeleti funkciók attól függően eltérőek, hogy milyen működési módot használ a JBoss EAP elindításához. Ez a RHEL és a Windows Server rendszeren támogatott. A JBoss EAP önálló kiszolgálói üzemmódot biztosít a diszkrét példányok kezeléséhez. Emellett felügyelt tartományi működési módot is kínál a példányok csoportjai egyetlen felügyeleti pontról való kezelésére. 

> [!NOTE]
> A JBoss EAP által felügyelt tartományok nem támogatottak Microsoft Azureban, mert az Azure infrastruktúra-szolgáltatások kezelik a HA funkciót. 

A környezeti változó `EAP_HOME` jelzi a JBoss EAP telepítésének elérési útját. Használja a következő parancsot a JBoss EAP szolgáltatás önálló módban való elindításához:

```
$EAP_HOME/bin/standalone.sh
```
    
Ez az indítási parancsfájl a EAP_HOME/bin/standalone.conf fájlt használja az alapértelmezett beállítások (például a JVM beállítások) beállításához. Testreszabhatja a fájlban található beállításokat. A JBoss EAP alapértelmezés szerint önálló módban indul el a standalone.xml konfigurációs fájllal, de az elindításához másik mód is használható. 

Az elérhető önálló konfigurációs fájlokról és azok használatáról az [önálló kiszolgáló konfigurációs fájljai az eap 7,2-hez](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) vagy az [EAP 7,3 önálló kiszolgálói konfigurációs fájljaihoz](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)című témakörben talál további információt. 

Ha a JBoss EAP-t egy másik konfigurációval szeretné elindítani, használja az `--server-config` argumentumot. Például:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Az összes elérhető indítási parancsfájl összes argumentumának és céljának teljes listáját a következő argumentummal végezheti el: `--help` . További információ: [kiszolgálói futtatókörnyezet argumentumai az eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) vagy a [Server Runtime argumentumai az EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)-ben.
    
A JBoss EAP fürtözött módban is működhet. A JBoss EAP-fürt üzenetkezelése lehetővé teszi a JBoss EAP üzenetküldési kiszolgálók csoportosítását az üzenet-feldolgozási terhelés megosztásához. A fürt minden aktív csomópontja egy aktív JBoss EAP üzenetkezelő kiszolgáló, amely a saját üzeneteit kezeli, és a saját kapcsolatait kezeli. További információ: [fürtök áttekintése az eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) vagy a [ FÜRTÖK áttekintése az EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Támogatási és előfizetési megjegyzések
Ezek a rövid útmutató sablonok a következők: 

- A RHEL operációs rendszer a Red Hat Gold képmodellen keresztül TB vagy BYOS is kínál.
- A JBoss EAP csak BYOS érhető el.

#### <a name="using-rhel-os-with-the-payg-model"></a>A RHEL operációs rendszer használata a TB-modellel

Alapértelmezés szerint ezek a rövid útmutatók az igény szerinti RHEL 7,7 vagy 8,0 TB-rendszerképet használják az Azure Marketplace-en. A TB-lemezképek további óránkénti RHEL előfizetési díjat számítanak fel a normál számítási, hálózati és tárolási költségek mellett. Egy időben a példány regisztrálva van a Red Hat-előfizetésében. Ez azt jelenti, hogy az egyik jogosultságát fogja használni. 

Ez a TB-rendszerkép a "dupla számlázás" kifejezést fogja eredményezni. Ezt a problémát elkerülheti saját RHEL-rendszerképének létrehozásával. További információért olvassa el a Red Hat tudásbázist a [RHEL virtuális gép kiépítése a Microsoft Azurehoz](https://access.redhat.com/articles/uploading-rhel-image-to-azure)című cikket. Vagy aktiválja a [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-rendszerképet.

A TB virtuális gépek díjszabásával kapcsolatos részletekért tekintse meg a [Red Hat Enterprise Linux díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Ha a RHEL-t a TB modellben szeretné használni, szüksége lesz egy Azure-előfizetésre az Azure Marketplace-en vagy az Azure Marketplace-en a [RHEL 8,0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)-os [RHEL 7,7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) -es fizetési móddal. Ezen ajánlatok esetében az Azure-előfizetésben meg kell adni egy fizetési módot.

#### <a name="using-rhel-os-with-the-byos-model"></a>A RHEL operációs rendszer használata a BYOS-modellel

Ahhoz, hogy a RHEL operációs rendszer BYOS használhassa, érvényes Red Hat-előfizetéssel kell rendelkeznie a RHEL operációs rendszer Azure-ban való használatához. Mielőtt telepítené a RHEL operációs rendszert a BYOS-modellel, hajtsa végre a következő előfeltételeket:

1. Győződjön meg arról, hogy rendelkezik a Red Hat-előfizetéséhez csatolt RHEL operációs rendszer-és JBoss EAP-jogosultságokkal.
2. Engedélyezze az Azure-előfizetés AZONOSÍTÓját a RHEL BYOS-lemezképek használatához. A folyamat befejezéséhez kövesse a [Red Hat előfizetés-kezelési dokumentációját](https://access.redhat.com/documentation/red_hat_subscription_management/1/) , amely a következő lépéseket tartalmazza:

   1. Engedélyezze a Microsoft Azure szolgáltatóként a Red Hat Cloud Access irányítópultján.

   1. Adja hozzá az Azure-előfizetési azonosítóit.

   1. Új termékek felhőalapú elérésének engedélyezése Microsoft Azureon.
    
   1. Az Azure-előfizetéshez tartozó Red Hat Gold-lemezképek aktiválása. További információ: [Red Hat Gold images on Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Várjon, amíg a Red Hat Gold images elérhetővé válik az Azure-előfizetésében. Ezek a lemezképek általában három órán belül elérhetők.
    
3. Fogadja el az Azure Marketplace használati feltételeit a RHEL BYOS-lemezképek esetében. Ezt a folyamatot az alábbi Azure CLI-parancsok futtatásával végezheti el. További információ: [RHEL BYOS Gold images in Azure](./byos.md) dokumentáció. Fontos, hogy az Azure CLI legújabb verzióját futtatja.

   1. Nyisson meg egy Azure CLI-munkamenetet, és végezzen hitelesítést az Azure-fiókjával. Segítségért lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

   1. A következő CLI-parancs futtatásával ellenőrizze, hogy a RHEL BYOS-lemezképei elérhetők-e az előfizetésben. Ha itt nem talál eredményt, győződjön meg arról, hogy az Azure-előfizetése aktiválva van a RHEL BYOS-lemezképek esetében.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Futtassa az alábbi parancsot az Azure Marketplace használati feltételeinek elfogadásához a RHEL 7,7 BYOS és a RHEL 8,0 BYOS esetén:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Az előfizetése most már készen áll a RHEL 7,7 vagy a 8,0 BYOS üzembe helyezésére az Azure Virtual Machines szolgáltatásban.

#### <a name="using-jboss-eap-with-the-byos-model"></a>A JBoss EAP használata a BYOS-modellel

A JBoss EAP csak a BYOS-modellen keresztül érhető el az Azure-on. A sablon telepítésekor meg kell adnia a RHSM hitelesítő adatait, valamint a RHSM-készlet AZONOSÍTÓját érvényes EAP-jogosultságokkal. Ha nem rendelkezik EAP-jogosultságokkal, a Kezdés előtt szerezzen be egy [JBoss EAP próbaverziós előfizetést](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) .

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

A sablont a következő módokon helyezheti üzembe:

- **PowerShell**. A sablon üzembe helyezéséhez futtassa a következő parancsokat: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  A Azure PowerShell telepítéséről és konfigurálásáról a [PowerShell dokumentációjában](/powershell/azure/)olvashat bővebben.  

- **Azure CLI**-vel. A sablon üzembe helyezéséhez futtassa a következő parancsokat:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Az Azure CLI telepítésének és konfigurálásának részletes ismertetését lásd: [a CLI telepítése](/cli/azure/install-azure-cli).

- **Azure Portal**. A következő szakaszban leírtak szerint telepítheti a Azure Portalt az Azure-beli Gyorsindítás sablonokkal. A gyors útmutatóban válassza az **üzembe helyezés az Azure** -ban vagy a **Tallózás a githubon** gombot.

## <a name="azure-quickstart-templates"></a>Azure-gyorssablonok

A következő Gyorsindítás sablonok egyikével kezdheti meg a JBoss EAP-t a RHEL, amely megfelel a telepítési célnak:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP on RHEL (különálló virtuális gép)</a>. Ezzel üzembe helyez egy JBoss-EAP nevű webalkalmazást az Azure-ban a RHEL 7,7-on vagy a 8,0-es virtuális gépen futó JBoss EAP 7,2 vagy 7,3 rendszerű számítógépeken.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (fürtözött, több virtuális gép)</a>. Ez egy EAP-Session-Replication nevű webalkalmazást helyez üzembe egy olyan JBoss EAP 7,2 vagy 7,3 fürtön, amely *n* számú RHEL 7,7 vagy 8,0 virtuális gépen fut. A felhasználó dönti el az *n* értéket. A rendszer az összes virtuális gépet hozzáadja a terheléselosztó háttér-készletéhez.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (fürtözött, virtuálisgép-méretezési csoport)</a>. Ez egy EAP-Session-Replication nevű webalkalmazást helyez üzembe egy RHEL 7,7 vagy 8,0 virtuálisgép-méretezési csoportokon futó JBoss EAP 7,2 vagy 7,3 fürtön.

## <a name="resource-links"></a>Erőforrás-hivatkozások

* [Azure Hybrid Benefit](../../windows/hybrid-use-benefit-licensing.md)
* [Java-alkalmazás konfigurálása Azure App Servicehoz](../../../app-service/configure-language-java.md)
* [JBoss EAP az Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP Azure App Service Linux rendszeren](../../../app-service/quickstart-java.md)
* [A JBoss EAP üzembe helyezése Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Következő lépések

* További információ a [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)-ről.
* További információ a [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)-ről.
* További információ a [Red Hat előfizetés-kezelésről](https://access.redhat.com/products/red-hat-subscription-management).
* Ismerje meg az Azure-beli [Red Hat számítási feladatokat](./overview.md).
* A [JBoss EAP üzembe helyezése RHEL virtuális gépen vagy virtuálisgép-méretezési csoporton az Azure piactéren](https://aka.ms/AMP-JBoss-EAP).
* [A JBoss EAP üzembe helyezése egy RHEL virtuális gépen vagy egy virtuálisgép-méretezési csoporton az Azure gyorsindító-sablonokból](https://aka.ms/Quickstart-JBoss-EAP).