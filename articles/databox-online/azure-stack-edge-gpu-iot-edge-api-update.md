---
title: Azure Stack Edge január 2021 Update Impact | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy milyen hatással van a IoT Edge szerepkör-kezelés Azure Stack Edge-eszközökön a januári 2021 frissítés telepítése után.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94578739"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>A Azure Stack Edge-eszköz szerepkör-felügyeleti változásainak IoT Edge

Az Azure Stack Edge-eszköz szerepkör-kezeléséhez használja az API, az SDK és a Azure PowerShell frissített verzióját, amely a 2021 januári kiadásra van ütemezve. Azure IoT Edge 

Ez a cikk részletesen ismerteti azokat a módosításokat, amelyeket a legújabb verzió használatakor el kell végeznie.

A januári 2021-es frissítés csak Azure Stack Edge Pro-GPU, Azure Stack Edge Pro R és Azure Stack Edge mini R-eszközök esetén lesz elérhető. A cikkben szereplő információk csak ezekre az eszközökre vonatkoznak.

> [!NOTE]
> Nem kell frissítenie a januári 2021-es verzióra. Ha úgy dönt, hogy továbbra is használja a jelenlegi verzióját, akkor nincs hatással IoT Edge szerepkör-felügyeletre. Ahhoz azonban, hogy kihasználhassa az új funkciókat, és csökkenteni tudja a biztonsági kockázatokat, javasoljuk, hogy telepítse az újabb verziót. 

## <a name="iot-edge-role-management-changes"></a>Szerepkör-kezelés IoT Edge módosítása

Miután telepítette az opcionális január 2021 frissítést az Azure Stack Edge-eszközön, az API, az SDK és a PowerShell-parancsmagok legújabb verzióját kell használnia IoT Edge szerepkör-kezeléshez.

A következő módosítások csak akkor szükségesek, ha a januári 2021 frissítést alkalmazza:

- Ha jelenleg az &nbsp; 2019-08-01-es szerepkör-kezelési API-t használja, frissítsen az API-verzióra, amelyet a 2021-es verzióban fog megjelenni. 
- Ha jelenleg az SDK-s verziójú 1.0.0-n keresztül használja &nbsp; a szerepkör-kezelést, frissítsen a verzióra, amely a 2021-es januári kiadásban jelenik meg.
- Ha szerepkör-kezelést használ a Azure PowerShell-parancsmagokkal (előzetes verzió), például,,, `Get-AzStackEdgeRole` `New-AzStackEdgeRole` `Set-AzStackEdgeRole` vagy `Remove-AzStackEdgeRole` , várjon, amíg az új parancsmagok fel lesznek szabadítva a 2021 februárjában.

## <a name="api-usage"></a>API-használat

Ha jelenleg IoT Edge szerepkör-kezelést hajt végre az API-n keresztül, akkor az új API 2020-12-01-es verzióját kell használnia, amely később közzé lesz téve. Ha a jelenlegi szerepkör-API-t használja, a közelgő eszköz szoftverének telepítését követően át kell térnie a PUT, a GET vagy a DELETE Kubernetes szerepkörre, majd a PUT IoT bővítmény API-ra.

### <a name="for-the-put-method"></a>A PUT metódushoz

#### <a name="the-current-http-request"></a>Az aktuális HTTP-kérelem 

- Az API-hívások a következő URI-n történnek: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 "

- A kérelem törzse a következőképpen néz ki:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>A közelgő HTTP-kérelem 

- A Kubernetes szerepkörhöz tartozó API-hívások a következő URI-n lesznek: 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    A kérelem törzse így fog kinézni:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- A IoT Edge-bővítmény API-hívásai a következő URI-n történnek: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    A kérelem törzse így fog kinézni:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>A GET metódushoz

#### <a name="the-current-http-response"></a>A jelenlegi HTTP-válasz

- Az API-hívások a következő URI-n történnek:

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- A válasz törzse a következőképpen néz ki:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>A közelgő HTTP-válasz

- Az API-hívások a következő URI-n történnek: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- A válasz törzse a következőképpen néz ki: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>A DELETE metódushoz

### <a name="the-current-api-calls"></a>Az aktuális API-hívások

Az API-hívások a következő URI-n történnek: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>A közelgő API-hívások

Az API-hívások a következő URI-n történnek: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK-használat

Ha az SDK-t használja, a januári 2021-es frissítés telepítése után módosítania kell a IoT Edge szerepkör beállításának módját, ahogy az a következő mintában látható. Ezután letöltheti és telepítheti a közelgő NuGet csomagot, hogy az itt látható módon váltson az új SDK-ra.

### <a name="the-current-sdk-sample"></a>Az aktuális SDK-minta

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>Az új SDK-minta

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Parancsmag használata

Ha jelenleg a `Get-AzStackEdgeRole` ,, `New-AzStackEdgeRole` `Set-AzStackEdgeRole` vagy `Remove-AzStackEdgeRole` parancsmagot használja, meg kell várnia a februári 2021-es kiadáshoz tervezett új verziót.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Azure Stack Edge Pro-FPGA-t használok. Befolyásolja a januári 2021-es frissítés a FPGA modellt?**

Nem. A januári 2021-es frissítés csak Azure Stack Edge Pro-FPGA, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkozik. A Azure Stack Edge Pro-FPGA nem érinti ez a frissítés, és nem igényel semmilyen módosítást IoT Edge szerepkör-felügyelethez.

**Miután Frissítettem a Azure Stack Edge Pro-GPU-t az új eszközre a 2021-as verzióban, a meglévő szolgáltatások bármelyike érintett?**

Nem. A konfigurált szolgáltatásokat a januári 2021-es eszköz frissítésének telepítése után nem érinti a rendszer.

**Milyen magas szintű változások vonatkoznak a IoT Edge Management API-ra, az SDK-ra vagy a parancsmagra?**

IoT Edge egy bővítmény a Kubernetes szerepkörben, ami azt jelenti, hogy először meg kell győződnie arról, hogy a Kubernetes konfigurálva van, majd végre kell hajtania a IoT Edge-konfigurációt.


## <a name="next-steps"></a>Következő lépések

- [Útmutató a frissítések alkalmazásához](azure-stack-edge-gpu-install-update.md)

