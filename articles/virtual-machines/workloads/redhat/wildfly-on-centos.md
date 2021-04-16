---
title: Gyorsútmutató – WildFly a CentOS-en
description: Java-alkalmazások üzembe helyezése a WildFlyben CentOS virtuális gépen
author: Theresa-Nguyen
ms.author: bicnguy
ms.date: 10/23/2020
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: bad9331a6d9a0c0936b1458e3aa4a84559e4a107
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533565"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Rövid útmutató: WildFly CentOS 8-on

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a CentOS 8 virtuális gép WildFly önálló csomópontját. Ideális nagyvállalati Java-alkalmazások fejlesztéséhez és teszteléséhez az Azure-ban. A rövid útmutató üzembe helyezéséhez nincs szükség alkalmazáskiszolgáló-előfizetésre.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, aktiválhatja [MSDN-előfizetői](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) előnyeit, vagy létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial)

## <a name="use-case"></a>Használati eset

A WildFly ideális nagyvállalati Java-alkalmazások fejlesztéséhez és teszteléséhez az Azure-ban. A WildFly 18 kiszolgálókonfigurációs profilokban elérhető technológiák listája a [WildFly első lépések útmutatójában érhető el.](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)

A WildFly használható önálló vagy fürt üzemmódban is. A WildFly biztosítja a kritikus fontosságú Jakarta EE-alkalmazások magas rendelkezésre állását a csomópontfürtökön. Kis számú alkalmazáskonfiguráció-módosítás, majd az alkalmazás üzembe helyezése a fürtön. További információért tekintse meg a [WildFly magas rendelkezésre állási útmutatóját.](https://docs.wildfly.org/18/High_Availability_Guide.html)

## <a name="configuration-choice"></a>Konfiguráció választása

A WildFly önálló  kiszolgáló módban is elindítható – Az önálló kiszolgálópéldány független folyamat, akárcsak a JBoss-alkalmazáskiszolgáló (AS) 3,4, 5 vagy 6 példánya. Az önálló példányok az indítási szkriptek standalone.sh vagy standalone.bat indíthatóak el. Egynél több önálló példány esetén a felhasználó felelőssége, hogy koordinálja a többkiszolgálós felügyeleti tevékenységeket a kiszolgálók között.

A WildFly-példányt alternatív konfigurációval is elindíthatja a konfigurációs mappában elérhető konfigurációs fájlok használatával.

Az önálló kiszolgáló konfigurációs fájljai a következők:

- standalone.xml (alapértelmezett) – Ez a konfiguráció a WildFly-példány indításhoz használt alapértelmezett fájl. Ez tartalmazza a Jakarta webprofil által hitelesített konfigurációt a szükséges technológiákkal.
   
- standalone-ha.xml – Jakarta EE Web Profile 8 tanúsítvánnyal rendelkező konfiguráció magas rendelkezésre állvánnyal (webalkalmazások számára).
   
- standalone-full.xml – Jakarta EE Platform 8 tanúsítvánnyal rendelkező konfiguráció, beleértve a Jakarta EE-alkalmazások üzemeltetéséhez szükséges összes technológiát.

- standalone-full-ha.xml – Jakarta EE Platform 8 tanúsítvánnyal rendelkező konfiguráció, magas rendelkezésre állvánnyal A Jakarta EE-alkalmazások üzemeltetése érdekében.

Ha az önálló WildFly-kiszolgálót egy másik megadott konfigurációval is elindítani, használja a --server-config argumentumot a server-config fájllal.

Ha például a Jakarta EE Platform 8-at fürtözési képességekkel használja, használja a következő parancsot:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

A konfigurációkról a [WildFly első lépések útmutatójában olvashat bővebben.](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)

## <a name="licensing-support-and-subscription-notes"></a>Licencelési, támogatási és előfizetési megjegyzések

Az Azure CentOS 8-as rendszerkép használat alapján fizetett virtuálisgép-rendszerkép, és nem követeli meg a felhasználótól, hogy licencet szerezzen be. A virtuális gép első indításakor a virtuális gép operációsrendszer-licence automatikusan aktiválódik, és óradíjat számít fel. Ez a Microsoft Linuxra vonatkozó óránkénti virtuálisgép-díjszabásán túl van. A részletekért kattintson a [Linux rendszerű virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) elemre. A WildFly ingyenesen letölthető és használható, és nem igényel Red Hat-előfizetést vagy -licencet.

## <a name="how-to-consume"></a>A fogyasztás

A sablont a következő három módon helyezheti üzembe:

- A PowerShell használata – A sablon üzembe helyezése [](/powershell/azure/) a következő parancsok futtatásával (a Azure PowerShell telepítésével és konfigurálásával kapcsolatos információkért lásd a Azure PowerShell).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Az Azure CLI használata – A sablon üzembe helyezése a következő parancsok futtatásával (az [Azure](/cli/azure/install-azure-cli) platformfüggetlen parancssorban talál további információt az Azure platformfüggetlen Command-Line Interface telepítéséről és konfigurálásról).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Használja Azure Portal – A sablon üzembe helyezéséhez kattintson <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">ide,</a> és jelentkezzen be a Azure Portal.

## <a name="arm-template"></a>ARM-sablon

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 CentOS 8-on (önálló</a> virtuális gép) – Ez egy gyorsindítási sablon, amely egy különálló csomópontot hoz létre a WildFly 18.0.1.Final csomópontból az erőforráscsoportban (RG) lévő CentOS 8 virtuális gépen, amely tartalmazza a virtuális gép, az Virtual Network és a Diagnosztikai tárfiók privát IP-címét. Emellett üzembe helyez egy JBoss-EAP nevű Java-mintaalkalmazást az Azure-ban a WildFly szolgáltatásban.

## <a name="resource-links"></a>Erőforrás-hivatkozások

* További információ a [WildFly 18-ről](https://docs.wildfly.org/18/)
* További információ az [Azure-beli Linux-disztribúciókról](../../linux/endorsed-distros.md)
* [Az Azure Java-fejlesztőknek dokumentációja](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Következő lépések

Éles környezetben tekintse meg a Red Hat JBoss EAP Azure gyors üzembe helyezési ARM-sablonokat:

Önálló RHEL virtuális gép mintaalkalmazással:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP az RHEL-en (önálló virtuális gép)</a>

Fürtözött RHEL virtuális gépek mintaalkalmazással:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP az RHEL-n (fürtözött virtuális gépek)</a>

Fürtözött RHEL virtuálisgép-méretezési csoport mintaalkalmazással:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP on RHEL (fürtözött virtuálisgép-méretezési csoport)</a>
