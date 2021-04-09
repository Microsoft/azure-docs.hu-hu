---
title: Cloud Services (klasszikus) szerepkör-konfiguráció XPath-lapja | Microsoft Docs
description: A Cloud Service szerepkör-konfigurációban használható különböző XPath-beállítások a beállítások környezeti változóként való megjelenítéséhez.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 397bc6845dc8d2d8bc44c00c27f6c12037651337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741383"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Szerepkör-konfigurációs beállítások közzététele környezeti változóként XPath-ként

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Cloud Service Worker vagy a web role szolgáltatás definíciós fájljában a futásidejű konfigurációs értékeket környezeti változókként teheti elérhetővé. A következő XPath-értékek támogatottak (amelyek az API-értékeknek felelnek meg).

Ezek az XPath-értékek a [Microsoft. WindowsAzure. ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) könyvtáron keresztül is elérhetők. 

## <a name="app-running-in-emulator"></a>Emulátorban futó alkalmazás
Azt jelzi, hogy az alkalmazás az emulátorban fut.

| Típus | Példa |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/Deployment/@emulated " |
| Code |var x = RoleEnvironment. IsEmulated; |

## <a name="deployment-id"></a>Központi telepítés azonosítója
A példány központi telepítési AZONOSÍTÓjának beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/Deployment/@id " |
| Code |var deploymentId = RoleEnvironment. DeploymentId; |

## <a name="role-id"></a>Szerepkör-azonosító
A példány aktuális szerepkör-AZONOSÍTÓjának beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@id " |
| Code |var azonosító = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Frissítési tartomány
A példány frissítési tartományának beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@updateDomain " |
| Code |var UD = RoleEnvironment. CurrentRoleInstance. UpdateDomain; |

## <a name="fault-domain"></a>Tartalék tartomány
A példány tartalék tartományának beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@faultDomain " |
| Code |var FD = RoleEnvironment. CurrentRoleInstance. FaultDomain; |

## <a name="role-name"></a>Szerepkörnév
Lekéri a példányok szerepkörének nevét.

| Típus | Példa |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@roleName " |
| Code |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurációs beállítás
A megadott konfigurációs beállítás értékének beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting [ @name =" Setting1 "] /@value " |
| Code |var-beállítás = RoleEnvironment. GetConfigurationSettingValue ("Setting1"); |

## <a name="local-storage-path"></a>Helyi tár elérési útja
A példány helyi tárolási útvonalának beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/LocalResources/LocalResource [ @name =" LocalStore1 "] /@path " |
| Code |var localResourcePath = RoleEnvironment. GetLocalResource ("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Helyi tárterület mérete
Lekéri a példány helyi tárterületének méretét.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/LocalResources/LocalResource [ @name =" LocalStore1 "] /@sizeInMB " |
| Code |var localResourceSizeInMB = RoleEnvironment. GetLocalResource ("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Végponti protokoll
A példány végponti protokolljának beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name =" Endpoint1 "] /@protocol " |
| Code |var Prot = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. Protokoll |

## <a name="endpoint-ip"></a>Végpont IP-címe
Lekéri a megadott végpont IP-címét.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name =" Endpoint1 "] /@address " |
| Code |var címe = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. címe |

## <a name="endpoint-port"></a>Végpont portja
A példány végpont-portjának beolvasása.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name =" Endpoint1 "] /@port " |
| Code |var port = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. port; |

## <a name="example"></a>Példa
Itt látható egy példa arra a feldolgozói szerepkörre, amely indítási feladatot hoz létre egy beállított nevű környezeti változóval `TestIsEmulated` az [ @emulated XPath értékhez](#app-running-in-emulator). 

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

## <a name="next-steps"></a>Következő lépések
További információ a [ServiceConfiguration. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlról.

Hozzon létre egy [szervizcsomagot. cspkg](cloud-services-model-and-package.md#servicepackagecspkg) csomagot.

Engedélyezze a [Távoli asztal](cloud-services-role-enable-remote-desktop-new-portal.md) szerepkört.




