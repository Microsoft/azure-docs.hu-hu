---
title: Gyorsútmutató – WildFly a CentOS-en
description: Java-alkalmazások üzembe helyezése a WildFly a CentOS virtuális gépen
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: 533d2881688598430ca05e964595352edf993dff
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676008"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Gyors útmutató: WildFly a CentOS 8-on

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a CentOS 8 rendszerű virtuális gépek önálló csomópontját a WildFly. Ideális megoldás a nagyvállalati Java-alkalmazások fejlesztéséhez és teszteléséhez az Azure-ban. A rövid útmutató telepítéséhez nem szükséges az alkalmazáskiszolgáló előfizetése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) , vagy [létrehozhat egy fiókot ingyenesen](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Használati eset

A WildFly ideális megoldást kínál a nagyvállalati Java-alkalmazások fejlesztésére és tesztelésére az Azure-ban. A WildFly 18 kiszolgálói konfigurációs profilokban elérhető technológiák listája a [WildFly első lépések útmutatójában](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)érhető el.

A WildFly-t használhatja önálló vagy fürt módban is a használati eset alapján. A kritikus Jakarta EE-alkalmazások magas rendelkezésre állása a WildFly alapján biztosítható a csomópontok fürtjén. Hozzon végre néhány alkalmazás-konfigurációt, majd telepítse az alkalmazást a fürtben. Ha többet szeretne megtudni erről, tekintse meg a [WildFly magas rendelkezésre állási útmutatóját](https://docs.wildfly.org/18/High_Availability_Guide.html).

## <a name="configuration-choice"></a>Konfiguráció választása

A WildFly **különálló kiszolgáló** módban indítható el – az önálló kiszolgálópéldány egy független folyamat, hasonlóan a JBoss Application Serverhez (as) 3, 4, 5 vagy 6 példány. Az önálló példányok a standalone.sh vagy a standalone.bat Launch Scripts használatával indíthatók el. Több önálló példány esetében a felhasználó feladata a többkiszolgálós felügyelet koordinálása a kiszolgálók között.

A WildFly-példányt alternatív konfigurációval is elindíthatja a konfigurációs mappában elérhető konfigurációs fájlok használatával.

Az önálló kiszolgáló konfigurációs fájljai a következők:

- standalone.xml (alapértelmezett) – Ez a konfiguráció a WildFly-példány elindításához használt alapértelmezett fájl. A szolgáltatás a szükséges technológiákkal rendelkező, jakartai webprofil Certified konfigurációt tartalmaz.
   
- standalone-ha.xml-Jakarta EE Web Profile 8 minősített konfiguráció magas rendelkezésre állással (webalkalmazásokra irányuló).
   
- standalone-full.xml-Jakarta EE platform 8 tanúsítvánnyal rendelkező konfiguráció, beleértve a Jakarta EE-alkalmazások üzemeltetéséhez szükséges technológiákat.

- standalone-full-ha.xml-Jakarta EE platform 8 minősített konfiguráció magas rendelkezésre állással a Jakarta EE-alkalmazások üzemeltetéséhez.

Ha egy másik megadott konfigurációval szeretné elindítani az önálló WildFly-kiszolgálót, használja a--Server-config argumentumot a Server-config fájllal.

Ha például a Jakarta EE platform 8 és a fürtözési képességek használatát szeretné használni, használja a következő parancsot:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Ha többet szeretne megtudni a konfigurációkról, tekintse meg a [WildFly első lépések útmutatót](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Licencelési, támogatási és előfizetési megjegyzések

Az Azure CentOS 8 rendszerképe egy utólagos elszámolású (TB) virtuálisgép-rendszerkép, amely nem igényli a felhasználótól a licenc beszerzését. A virtuális gép első indításakor a virtuális gép operációs rendszerének licence automatikusan aktiválódik és óradíjat számol fel. Ez a Microsoft Linux rendszerű, óránkénti virtuális gépek díjszabása. A részletekért kattintson a [Linux virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) lehetőségre. A WildFly ingyenesen letölthető és használható, és nem igényel Red Hat-előfizetést vagy-licencet.

## <a name="how-to-consume"></a>Használat

A sablont a következő három módon telepítheti:

- A PowerShell használata – a sablon üzembe helyezéséhez futtassa a következő parancsokat: (a Azure PowerShell telepítésével és konfigurálásával kapcsolatos információkért tekintse meg [Azure PowerShell](/powershell/azure/) .)

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Az Azure CLI használata – a sablon üzembe helyezéséhez futtassa a következő parancsokat: (az Azure platformfüggetlen Command-Line felületének telepítésével és konfigurálásával kapcsolatos további információkért tekintse meg az [Azure többplatformos parancssorát](/cli/azure/install-azure-cli) ).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Azure Portal használata – a sablon üzembe helyezéséhez kattintson <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">ide</a> , és jelentkezzen be a Azure Portalba.

## <a name="arm-template"></a>ARM-sablon

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18, CentOS 8 (különálló virtuális gép)</a> – ez egy rövid útmutató sablon, amely létrehoz egy önálló csomópontot a WildFly 18.0.1. Final on CentOS 8 virtuális gépen az ERŐFORRÁSCSOPORTHOZ (RG), amely magában foglal egy magánhálózati IP-címet a virtuális gép, a Virtual Network és a diagnosztikai Storage-fiók számára. Emellett üzembe helyez egy JBoss-EAP nevű minta Java-alkalmazást az Azure-on az WildFly-on.

## <a name="resource-links"></a>Erőforrás-hivatkozások

* További információ a [WildFly 18](https://docs.wildfly.org/18/) -ról
* További információ az [Azure-beli Linux-disztribúcióról](../../linux/endorsed-distros.md)
* [Az Azure Java-fejlesztőknek dokumentációja](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Következő lépések

Éles környezetben tekintse meg a Red Hat JBoss EAP Azure Gyorsindítás ARM-sablonokat:

Önálló RHEL virtuális gép minta alkalmazással:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP on RHEL (különálló virtuális gép)</a>

Fürtözött RHEL virtuális gépek minta alkalmazással:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP on RHEL (fürtözött virtuális gépek)</a>

Fürtözött RHEL virtuálisgép-méretezési csoport minta alkalmazással:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP on RHEL (fürtözött virtuálisgép-méretezési csoport)</a>