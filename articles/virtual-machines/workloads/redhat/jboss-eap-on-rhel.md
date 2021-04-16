---
title: Rövid útmutató – JBoss Enterprise Application Platform (EAP) üzembe helyezése Red Hat Enterprise Linux -on Red Hat Enterprise Linux (RHEL) Azure-beli virtuális gépeken és virtuálisgép-méretezési készletekben
description: Vállalati Java-alkalmazások üzembe helyezése a Red Hat JBoss EAP használatával Azure RHEL virtuális gépeken és virtuálisgép-méretezési készleteken.
author: theresa-nguyen
ms.author: bicnguy
ms.date: 10/30/2020
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: 18cf093adad858f50b2b1fa1c97e38821bd1c949
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538681"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Vállalati Java-alkalmazások üzembe helyezése az Azure-on a JBoss EAP használatával Red Hat Enterprise Linux rendszeren

A cikkben található Azure gyorsindítási sablonok azt mutatják be, hogyan helyezhet üzembe [JBoss Enterprise Application Platformot (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) használatával Azure-beli virtuális gépeken és virtuálisgép-méretezési csoportban. Az üzembe helyezés ellenőrzéséhez egy Java-mintaalkalmazást fog használni. 

A JBoss EAP egy nyílt forráskódú alkalmazáskiszolgáló platform. Nagyvállalati szintű biztonságot, méretezhetőséget és teljesítményt nyújt a Java-alkalmazások számára. Az RHEL egy nyílt forráskódú operációsrendszer-platform. Lehetővé teszi a meglévő alkalmazások skálázását, és új technológiákat vezet be az összes környezetben. 

A JBoss EAP és RHEL mindent tartalmaz, amire szüksége lehet a vállalati Java-alkalmazások bármely környezetben való felépítéséhez, futtatásához, üzembe helyezéséhez és kezeléséhez. Ez egy nyílt forráskódú megoldás helyszíni, virtuális környezetekhez, valamint privát, nyilvános vagy hibrid felhőkhöz.

## <a name="prerequisites"></a>Előfeltételek 

* Aktív előfizetéssel rendelkezik egy Azure-fiók. Azure-előfizetés vásárlásához aktiválja azure-kreditjéből Visual Studio előfizetőknek, [vagy](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial)

* JBoss EAP-telepítés. A JBoss EAP-hez Red Hat-előfizetés-kezelési (RED Hat-) jogosultsággal kell rendelkezik. Ezzel a jogosultsággal letöltheti a Red Hat által tesztelt és minősített JBoss EAP-verziót.  

  Ha nem rendelkezik EAP-jogosultsággal, az első lépések előtt szerezzen be [egy JBoss](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) EAP-próba-előfizetést. Új Red Hat-előfizetés létrehozásához a [Red Hat ügyfélportálon](https://access.redhat.com/) állíthat be egy fiókot.

* Az [Azure CLI.](/cli/azure/overview)

* RHEL-beállítások. Válassza a pay-as-you-go (PAYG) vagy a bring-your-own-subscription (BYOS) (saját előfizetés (BYOS) lehetőséget. A BYOS-sel aktiválnia kell a Red Hat Cloud Access [RHEL](https://access.redhat.com/) Gold rendszerképet, mielőtt üzembe helyezné a gyorsindítási sablont.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE- és Jakarta EE-alkalmazások migrálása

### <a name="migrate-to-jboss-eap"></a>Áttelepítés JBoss EAP-be
A JBoss EAP 7.2 és 7.3 a Java Enterprise kiadás (Java EE) 8 és a Jakarta EE 8 specifikáció hitelesített implementációja. A JBoss EAP előre konfigurált lehetőségeket biztosít az olyan szolgáltatásokhoz, mint a magas rendelkezésre állású (HA) fürtözés, az üzenetkezelés és az elosztott gyorsítótárazás. Emellett lehetővé teszi a felhasználók számára alkalmazások írását, üzembe helyezését és futtatását a JBoss EAP által nyújtott különböző API-k és szolgáltatások használatával.  

A JBoss EAP-vel kapcsolatos további információkért lásd: [Introduction to JBoss EAP 7.2 (A JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) bemutatása) vagy [Introduction to JBoss EAP 7.3 (A JBoss EAP 7.3 bemutatása).](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)

 #### <a name="applications-on-jboss-eap"></a>Alkalmazások a JBoss EAP-n

* **Webszolgáltatás-alkalmazások.** A webszolgáltatások szabványos lehetőséget biztosítanak a szoftveralkalmazások közötti együttműködésre. Minden alkalmazás különböző platformokon és keretrendszerekkel futtatható. Ezek a webszolgáltatások elősegítik a belső és heterogén alrendszerek közötti kommunikációt. 

  További információ: Webszolgáltatás-alkalmazások fejlesztése [az EAP 7.2-ben](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) vagy Webszolgáltatás-alkalmazások fejlesztése az [EAP 7.3-on.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)

* **Enterprise Java Beans- (EJB-) alkalmazások.** Az EJB 3.2 egy elosztott, tranzakciós, biztonságos és hordozható Java EE- és Jakarta EE-alkalmazások fejlesztésére használható API. Az EJB az Enterprise Beans nevű kiszolgálóoldali összetevők használatával leválasztott módon valósítja meg az alkalmazások üzleti logikáját, ami elősegíti az újrafelhasználást. 

  További információ: [EJB-alkalmazások fejlesztése az EAP 7.2-n](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) vagy [EJB-alkalmazások fejlesztése az EAP 7.3-on.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)

* **Hibernált alkalmazások.** A fejlesztők és rendszergazdák Java Persistence API-t (JPA) és Hibernate-alkalmazásokat fejleszthet és helyezhetnek üzembe a JBoss EAP-val. A Hibernate Core egy objektumrelációs leképezési keretrendszer a Java nyelvhez. Keretrendszert biztosít egy objektumorientált tartományi modell relációs adatbázisra való leképezéséhez, így az alkalmazások elkerülik az adatbázissal való közvetlen interakciót. 

  A Hibernate Entity Manager a [JPA 2.1](https://www.jcp.org/en/jsr/overview)specifikációban meghatározott programozási felületeket és életciklus-szabályokat implementálja. A Hibernált jegyzetekkel együtt ez a burkoló egy teljes (és önálló) JPA-megoldást implementál az érett Hibernate Core-on. 
  
  A Hibernáltával kapcsolatos további információkért lásd: [JPA on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) vagy [Jakarta Persistence on EAP 7.3 (JPA az EAP 7.2-es vagy Jakarta-adatmegőrzési eAP 7.3-on).](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[A Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) egy Java-alkalmazáskiszolgálók migrálási eszköze. Ezzel az eszközzel egy másik alkalmazáskiszolgálóról JBoss EAP-t használhat. Az [Eclipse IDE, a](https://www.eclipse.org/ide/) [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview)és a [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) for Java IDE ide beépülő modulokkal működik. 

Az MTA egy ingyenes és nyílt forráskódú eszköz, amely:
* Automatizálja az alkalmazáselemzést.
* Támogatja az erőfeszítés becslését.
* Felgyorsítja a kódáttelepítést.
* Támogatja a tárolókba való betárolózást.
* Integrálható az Azure Workload Builder alkalmazással.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>JBoss EAP áttelepítése a helyszínről az Azure-ba
Az Azure Marketplace JBoss EAP RHEL-re vonatkozó ajánlata kevesebb mint 20 perc alatt telepíti és kiépíti az Azure-beli virtuális gépeket. Ezeket az ajánlatokat a következő [Azure Marketplace.](https://azuremarketplace.microsoft.com/)

Ez Azure Marketplace ajánlat az EAP- és RHEL-verziók különböző kombinációit tartalmazza a követelmények támogatása érdekében. A JBoss EAP mindig BYOS, RHEL operációs rendszerekhez azonban BYOS vagy PAYG közül választhat. A Azure Marketplace ajánlat tartalmazza az RHEL-re vonatkozó JBoss EAP csomagokat önálló vagy fürtözött virtuális gépekként:

* JBoss EAP 7.2 RHEL 7.7 virtuális gépen (PAYG)
* JBoss EAP 7.2 RHEL 8.0 virtuális gépen (PAYG)
* JBoss EAP 7.3 RHEL 8.0 virtuális gépen (PAYG)
* JBoss EAP 7.2 RHEL 7.7 virtuális gépen (BYOS)
* JBoss EAP 7.2 RHEL 8.0 virtuális gépen (BYOS)
* JBoss EAP 7.3 RHEL 8.0 virtuális gépen (BYOS)

A Azure Marketplace mellett a gyorsindítási sablonokkal is elkezdheti az Azure-ba való migrálást. Ezek a rövid útmutatók előre összeállított Azure Resource Manager (ARM) sablonokat és szkripteket tartalmaznak a JBoss EAP RHEL-re való üzembe helyezéséhez különböző konfigurációkban és verziókombinációkban. A következőt fogja önnek:

* Egy terheléselosztási rendszer.
* Magánhálózati IP-cím a terheléselosztáshoz és a virtuális gépekhez.
* Egy virtuális hálózat egyetlen alhálózattal.
* Virtuálisgép-konfiguráció (fürt vagy önálló).
* Java-mintaalkalmazás.

A sablonok megoldásarchitektúrája a következőket tartalmazza:

* JBoss EAP önálló RHEL virtuális gépen.
* Több RHEL virtuális gép között fürtözött JBoss EAP.
* Azure-beli virtuálisgép-méretezési csoportokon keresztül fürtözött JBoss EAP.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Linux számítási feladatok migrálása a JBoss EAP-hez
Az Azure Workload Builder leegyszerűsíti a helyszíni Java-alkalmazások koncepció igazolási, kiértékelési és migrálási folyamatát az Azure-ba. A Workload Builder integrálható a Azure Migrate Discovery eszközzel a JBoss EAP-kiszolgálók azonosításához. Ezután dinamikusan létrehoz egy Ansible-forgatókönyvet a JBoss EAP-kiszolgáló üzembe helyezéséhez. A Red Hat MTA eszközt használja kiszolgálók áttelepítéséhez más alkalmazáskiszolgálókról a JBoss EAP-be. 

A migrálás egyszerűsítésének lépései a következők:
1. **Evaluation (Kiértékelés)**. JBoss EAP-fürtök kiértékelése Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport használatával.
1. **Értékelés**. Alkalmazások és infrastruktúra beolvasása.
1. **Infrastruktúra-konfiguráció.** Hozzon létre egy számításiprofil-profilt.
1. **Üzembe helyezés és tesztelés.** A számítási feladat üzembe helyezése, mi helyezése és tesztelése.
1. **Üzembe helyezés utáni konfiguráció.** Integráció adatokkal, monitorozással, biztonsággal, biztonsági mentéssel stb.

## <a name="server-configuration-choice"></a>Kiszolgáló konfigurációjának választása

Az RHEL virtuális gép üzembe helyezéséhez választhat a PAYG vagy a BYOS közül. A rendszerképek [Azure Marketplace](https://azuremarketplace.microsoft.com) a PAYG értéket. Ha saját RHEL operációsrendszer-rendszerképe van, telepítsen egy BYOS-típusú RHEL virtuális gépet. Mielőtt üzembe helyezné a virtuális gépet vagy a virtuálisgép-méretezési Cloud Access, győződjön meg arról, hogy a SAJÁTOS-fiókja BYOS-jogosultsággal rendelkezik.

A JBoss EAP hatékony felügyeleti képességekkel rendelkezik, valamint funkciókat és API-kat biztosít alkalmazásai számára. Ezek a felügyeleti képességek a JBoss EAP-k indítási módjától függően eltérőek. RHEL és Windows Server rendszeren támogatott. A JBoss EAP különálló kiszolgálói működési módot kínál a különálló példányok kezeléséhez. Felügyelt tartományi működési módot is kínál a példánycsoportok egyetlen vezérlőpontról való kezeléséhez. 

> [!NOTE]
> A JBoss EAP által felügyelt tartományok nem támogatottak a Microsoft Azure mivel az Azure infrastruktúra-szolgáltatások kezelik a ha funkciót. 

A környezeti változó `EAP_HOME` a JBoss EAP telepítésének elérési útját jelöli. A következő paranccsal indítsa el a JBoss EAP szolgáltatást önálló módban:

```
$EAP_HOME/bin/standalone.sh
```
    
Ez az indítási szkript a EAP_HOME/bin/standalone.conf fájlt használja néhány alapértelmezett beállítás, például a JVM-beállítások beállítására. Ebben a fájlban testreszabhatja a beállításokat. A JBoss EAP alapértelmezés szerint a standalone.xml konfigurációs fájlt használja a különálló módban való indításhoz, de más módban is elindíthatja. 

Az elérhető önálló konfigurációs fájlokról és azok használatával kapcsolatos részletekért lásd: Önálló kiszolgáló konfigurációs fájljai [az EAP 7.2-hez](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) vagy Önálló kiszolgálókonfigurációs fájlok az [EAP 7.3-hoz.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) 

A JBoss EAP másik konfigurációval való indításhoz használja a `--server-config` argumentumot. Például:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Az összes elérhető indítási szkript argumentumának és azok céljának teljes felsorolását a argumentum használatával `--help` lehet végrehajtani. További információ: [Server Runtime Arguments on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) (Kiszolgálói futásidejű argumentumok az EAP 7.2-ben) vagy [Server Runtime Arguments on EAP 7.3 (Az EAP 7.3 kiszolgálói futásidejű argumentumai).](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)
    
A JBoss EAP fürt üzemmódban is működik. A JBoss EAP-fürt üzenetkezelése lehetővé teszi a JBoss EAP üzenetkezelési kiszolgálók csoportosítását az üzenetfeldolgozási terhelés megosztásához. A fürt minden aktív csomópontja egy aktív JBoss EAP üzenetkezelési kiszolgáló, amely kezeli a saját üzeneteit, és kezeli a saját kapcsolatait. További információ: Fürtök áttekintése az [EAP 7.2-ben](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) vagy [Fürtök áttekintése az EAP 7.3-ban.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) 

## <a name="support-and-subscription-notes"></a>Támogatási és előfizetési megjegyzések
Ezek a gyorsindítási sablonok a következők: 

- Az RHEL operációs rendszer a Red Hat Gold image modellen keresztül payG vagy BYOS szolgáltatásként érhető el.
- A JBoss EAP-t csak BYOS-ként kínáljuk.

#### <a name="using-rhel-os-with-the-payg-model"></a>Az RHEL operációs rendszer használata a PAYG-modellel

Alapértelmezés szerint ezek a gyorsindítási sablonok az igény szerinti RHEL 7.7-es vagy 8.0-s PAYG rendszerképet használják a Azure Marketplace. A PAYG-rendszerképekre további óránkénti RHEL-előfizetési díjakat kell fizetni a normál számítási, hálózati és tárolási költségeken felül. A példány ugyanakkor regisztrálva van a Red Hat-előfizetésben. Ez azt jelenti, hogy az egyik jogosultságát fogja használni. 

Ez a PAYG-rendszerkép "dupla számlázást" fog vezetni. Ezt a problémát elkerülheti, ha saját RHEL-rendszerképet épít ki. További tudnivalókért olvassa el a Red Hat-tudásbázis [How to provision a RHEL VM for Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Vagy aktiválja a [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold rendszerképet.

A PAYG virtuális gépek díjszabásának részleteiért lásd: [Red Hat Enterprise Linux díjszabása.](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) Ha az RHEL-t a PAYG modellben használja, szüksége lesz egy Azure-előfizetésre, amely az [RHEL 7.7-hez](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) a Azure Marketplace vagy az [RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)esetében a megadott fizetési Azure Marketplace. Ezekhez az ajánlatokhoz meg kell adni egy fizetési módot az Azure-előfizetésben.

#### <a name="using-rhel-os-with-the-byos-model"></a>Az RHEL operációs rendszer használata a BYOS-modellel

A BYOS RHEL operációs rendszerhez való használatához egy érvényes Red Hat-előfizetéssel kell rendelkezik, amely jogosult az RHEL operációs rendszer használatára az Azure-ban. Mielőtt üzembe helyezné az RHEL operációs rendszert a BYOS-modellel, a következő előfeltételeket kell végrehajtania:

1. Győződjön meg arról, hogy RHEL operációs rendszer és JBoss EAP-jogosultságok vannak csatolva a Red Hat-előfizetéséhez.
2. Engedélyezze az Azure-előfizetés azonosítóját RHEL BYOS-rendszerképek használatára. A [folyamat befejezéséhez](https://access.redhat.com/documentation/red_hat_subscription_management/1/) kövesse a Red Hat-előfizetések kezelésével kapcsolatos dokumentációt, amely a következő lépéseket tartalmazza:

   1. Engedélyezze Microsoft Azure szolgáltatás szolgáltatóként való engedélyezését a Red Hat Cloud Access irányítópultján.

   1. Adja hozzá az Azure-előfizetésekhez szükséges szolgáltatásokat.

   1. Új termékek engedélyezése Cloud Access a Microsoft Azure.
    
   1. Aktiválja a Red Hat Gold Images-képeket az Azure-előfizetéséhez. További információ: [Red Hat Gold Images a Microsoft Azure.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access)

   1. Várjon, amíg a Red Hat Gold Images elérhetővé válik az Azure-előfizetésében. Ezek a képek általában a beküldéstől számított három órán belül érhetők el.
    
3. Fogadja el Azure Marketplace RHEL BYOS-rendszerképekre vonatkozó használati feltételeket. Ezt a folyamatot az alábbi Azure CLI-parancsok futtatásával fejezheti be. További információt az [Azure RHEL BYOS Gold Images dokumentációjában](./byos.md) talál. Fontos, hogy az Azure CLI legújabb verzióját futtasa.

   1. Nyisson meg egy Azure CLI-munkamenetet, és hitelesítse magát az Azure-fiókjával. További segítségért lásd: [Bejelentkezés az Azure CLI-val.](/cli/azure/authenticate-azure-cli)

   1. Az alábbi CLI-parancs futtatásával ellenőrizze, hogy az RHEL BYOS-rendszerképek elérhetők-e az előfizetésében. Ha itt nem kap eredményt, győződjön meg arról, hogy az Azure-előfizetése aktiválva van az RHEL BYOS-rendszerképekhez.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. A következő parancs futtatásával fogadja el Azure Marketplace RHEL 7.7 BYOS és RHEL 8.0 BYOS használati feltételeit:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Az előfizetés most már készen áll az RHEL 7.7 vagy 8.0 BYOS Azure-beli virtuális gépeken való üzembe helyezésére.

#### <a name="using-jboss-eap-with-the-byos-model"></a>A JBoss EAP használata a BYOS-modellel

A JBoss EAP csak a BYOS-modellen keresztül érhető el az Azure-ban. A sablon üzembe helyezésekor érvényes EAP-jogosultságokkal kell megadnia a KÓDOTSM-hez szükséges hitelesítő adatokat, valamint a KÓDOTSM-készlet azonosítóját. Ha nem rendelkezik EAP-jogosultságokkal, első lépések előtt szerezzen be [egy JBoss](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) EAP-próba-előfizetést.

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

A sablont a következő módokon helyezheti üzembe:

- **PowerShell**. Telepítse a sablont a következő parancsok futtatásával: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Az alkalmazások telepítésével és konfigurálásával kapcsolatos Azure PowerShell lásd a [PowerShell dokumentációját.](/powershell/azure/)  

- **Azure CLI**. Telepítse a sablont a következő parancsok futtatásával:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Az Azure CLI telepítésével és konfigurálásával kapcsolatos részletekért lásd: [A CLI telepítése.](/cli/azure/install-azure-cli)

- **Azure Portal**. Az üzembe helyezést a Azure Portal a következő szakaszban feljegyzett Azure gyorsindítási sablonok között. A rövid útmutatót követően válassza az Üzembe helyezés az **Azure-ban** vagy **a Tallózás a GitHubon gombot.**

## <a name="azure-quickstart-templates"></a>Azure-gyorssablonok

Első lépésekként használhatja az alábbi gyorsindítási sablonok egyikét a JBoss EAP-hez RHEL környezetben, amely megfelel az üzembe helyezési célnak:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP on RHEL (önálló virtuális gép)</a>. Ez üzembe helyez egy JBoss-EAP nevű webalkalmazást az Azure-ban az RHEL 7.7-es vagy 8.0-s virtuális gépen futó JBoss EAP 7.2-es vagy 7.3-as gépen.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (fürtözött, több virtuális gép)</a>. Ez üzembe helyez egy eap-session-replication nevű webalkalmazást egy N számú RHEL 7.7-es vagy 8.0-s virtuális gépeken futó JBoss EAP 7.2-es vagy 7.3-as fürtön.  A felhasználó dönti el *az n* értéket. Az összes virtuális gép hozzá lesz adva egy terheléselosztás háttérkészlethez.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (fürtözött, virtuálisgép-méretezési csoport)</a>. Ez üzembe helyez egy eap-session-replication nevű webalkalmazást egy RHEL 7.7-es vagy 8.0-s virtuálisgép-méretezési csoportokon futó JBoss EAP 7.2-es vagy 7.3-as fürtön.

## <a name="resource-links"></a>Erőforrás-hivatkozások

* [Azure Hybrid Benefit](../../windows/hybrid-use-benefit-licensing.md)
* [Java-alkalmazás konfigurálása Azure App Service](../../../app-service/configure-language-java.md)
* [JBoss EAP a Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP Linux Azure App Service rendszeren](../../../app-service/quickstart-java.md)
* [JBoss EAP üzembe helyezése Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Következő lépések

* További információ a [JBoss EAP 7.2-ről.](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* További információ a [JBoss EAP 7.3-asról.](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* További információ a [Red Hat-előfizetések kezelésével kapcsolatban.](https://access.redhat.com/products/red-hat-subscription-management)
* További információ az [Azure-beli Red Hat számítási feladatokról.](./overview.md)
* Telepítse [a JBoss EAP-t egy RHEL virtuális](https://aka.ms/AMP-JBoss-EAP)gépen vagy virtuálisgép-méretezési Azure Marketplace.
* JBoss EAP üzembe helyezése RHEL virtuális gépen vagy virtuálisgép-méretezési csoporton [azure gyorsindítási sablonokból.](https://aka.ms/Quickstart-JBoss-EAP)
