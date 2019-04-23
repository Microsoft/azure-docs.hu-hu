---
title: Cloud Services-szerepkör konfigurációs XPath-Adatlap |} A Microsoft Docs
description: A különböző XPath beállításokat használhatja a cloud service szerepkör config beállítások elérhetővé környezeti változóban.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 53a262af421dd986e6b70af173a6e8b3f7c06f64
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798759"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Az XPath környezeti változóban tegye elérhetővé a szerepkör konfigurációs beállításai
A cloud service feldolgozó vagy a webes szerepkör szolgáltatásdefiníciós fájl környezeti változókként közzéteheti a futásidejű konfigurációs értékeket. Az alábbi XPath-értékeket támogatja (amelyek megfelelnek az API-értéket).

Ezek az értékek XPath keresztül is érhetők el a [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) könyvtár. 

## <a name="app-running-in-emulator"></a>Emulátor-ben futó alkalmazás
Azt jelzi, hogy az alkalmazás futtatása az emulátorral.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Kód |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Üzemelő példány azonosítója
Az üzemelő példány azonosítója a példány kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Kód |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Szerepkör-azonosító
Lekéri a jelenlegi szerepkör-példány azonosítója.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Kód |var azonosítója = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Frissítési tartomány
A frissítési tartomány a példány kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kód |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Tartalék tartomány
A tartalék tartomány példány kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kód |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Szerepkörnév
A példányok szerepkör nevét kéri le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Kód |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurációs beállítás
A megadott konfigurációs beállítás értékét kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Kód |var beállítás RoleEnvironment.GetConfigurationSettingValue("Setting1"); = |

## <a name="local-storage-path"></a>Helyi tároló elérési útja
A helyi elérési útja a példány kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Kód |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Helyi tároló mérete
A helyi tároló a példány méretének kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Kód |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protokoll végpontja
A végpont protokoll-példány kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Kód |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protokoll; |

## <a name="endpoint-ip"></a>Végpont IP
Lekérdezi a megadott végpont IP-címét.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Kód |var cím = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Végponti port
A végponti port példány kérdezi le.

| Typo | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Kód |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Példa
Íme egy példa, amely létrehoz egy indítási feladat egy nevű környezeti változót a feldolgozói szerepkör `TestIsEmulated` beállítása a [ @emulated xpath értékét](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlt.

Hozzon létre egy [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) csomagot.

Engedélyezése [a távoli asztal](cloud-services-role-enable-remote-desktop-new-portal.md) egy adott szerepkör esetében.

