---
title: Felügyelt identitásokat támogató Azure-szolgáltatások – Azure AD
description: Az Azure-erőforrások felügyelt identitását és az Azure AD-hitelesítést támogató szolgáltatások listája
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: c4cd9140d03bba1f9d95ed64c3628da4fe32ecd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771478"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitását támogató szolgáltatások

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Felügyelt identitás használatával bármilyen, az Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban. Folyamatban van az Azure-erőforrások felügyelt identitásának és az Azure AD-hitelesítésnek az Azure-ban való integrálása. Gyakran ellenőrizze, hogy vannak-e frissítések.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

A következő Azure-szolgáltatások támogatják az Azure-erőforrások felügyelt identitását:


### <a name="azure-api-management"></a>Azure API Management

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |

Tekintse meg az alábbi listát az Azure-beli virtuális API Management (az elérhető régiókban) felügyelt identitásának konfigurálásához:

- [Azure Resource Manager-sablon](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | Nem érhető el  | ![Elérhető][check] |

Tekintse meg az alábbi listát a felügyelt identitás konfigurálás Azure App Configuration (ahol elérhetők a régiókban):

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check] |

Tekintse meg az alábbi listát a felügyelt identitás konfigurálásához Azure App Service (ahol elérhető régiókban):

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sablon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-kompatibilis Kubernetes

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Azure Arc engedélyezett Kubernetes jelenleg a rendszer [által hozzárendelt identitást támogatja.](../../azure-arc/kubernetes/quickstart-connect-cluster.md) A felügyeltszolgáltatás-identitás tanúsítványát az összes Azure Arc Kubernetes-ügynök használja az Azure-ral való kommunikációhoz.

### <a name="azure-arc-enabled-servers"></a>Azure Arc-kompatibilis kiszolgálók

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Minden Azure Arc engedélyezett kiszolgáló rendszer által hozzárendelt identitással. Nem tilthatja le vagy módosíthatja a rendszer által hozzárendelt identitást egy Azure Arc kiszolgálón. A felügyelt identitások engedélyezett kiszolgálókon való felhasználását az alábbi forrásokban Azure Arc meg:

- [Hitelesítés Azure-erőforrásokon Arc-kompatibilis kiszolgálókkal](../../azure-arc/servers/managed-identity-authentication.md)
- [Felügyelt identitás használata Arc-kompatibilis kiszolgálókkal](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Ha az előfizetését új bérlőre költözte át, a felügyelt identitás újrakonfigurálása érdekében tekintse meg a következő dokumentumot:
* [Hibás automatikus automatizálható fiók javítása](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi listát, ha felügyelt identitást használ a [Azure Blueprints:](../../governance/blueprints/overview.md)

- [Azure Portal – terv hozzárendelése](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – terv hozzárendelése](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |


### <a name="azure-communication-services"></a>Azure Communication Services

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |


### <a name="azure-container-instances"></a>Azure Container Instances

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Linux: előzetes verzió<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Linux: előzetes verzió<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Az alábbi listában konfigurálhatja a felügyelt identitást a Azure Container Instances (ahol elérhető régiókban):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |

Az alábbi listában konfigurálhatja a felügyelt identitást a Azure Container Registry-feladatok számára (ahol elérhető a régió):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Adatkezelő

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

A felügyelt identitás v2-hez (ahol elérhető Azure Data Factory régióban való konfigurálásához tekintse meg a következő listát:

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sdk](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg a következő listát a felügyelt identitás konfigurálásához Azure Digital Twins (ahol elérhető régiókban):

- [Azure Portal](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el  | Nem érhető el  | Nem érhető el |

### <a name="azure-firewall-policy"></a>Azure Firewall szabályzat

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el  | Nem érhető el  | Nem érhető el |

### <a name="azure-functions"></a>Azure Functions

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check]  |

Tekintse meg az alábbi listát a felügyelt identitás konfigurálásához Azure Functions (az elérhető régiókban):

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sablon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg a következő listát a felügyelt identitás konfigurálásához Azure IoT Hub (az elérhető régiókban):

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Abban a régióban érhető el, ahol az Azure Import Export szolgáltatás elérhető | Előnézet | Elérhető | Elérhető |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |


További információ: [Felügyelt identitások használata](../../aks/use-managed-identity.md)a Azure Kubernetes Service.

### <a name="azure-log-analytics-cluster"></a>Azure Log Analytics-fürt

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |

További információ: Az [identitás működése a Azure Monitor](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |


Az alábbi listában konfigurálhatja a felügyelt identitást a Azure Logic Apps számára (ahol elérhető a régió):

- [Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sablon](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |

További információ: [Felügyelt identitások használata a Azure Machine Learning.](../../machine-learning/how-to-use-managed-identities.md)

### <a name="azure-policy"></a>Azure Policy

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Az alábbi listában konfigurálhatja a felügyelt identitásokat a Azure Policy (ahol elérhetők a régiókban):

- [Azure Portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Azure Resource Manager-sablonok](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Felügyelt identitás a Service Fabric Alkalmazások](../../service-fabric/concepts-managed-identity.md) minden régióban elérhető.

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | nem érhető el |
| Felhasználó által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el |Nem érhető el |

Az alábbi listában konfigurálhatja a felügyelt identitást az Azure Service Fabric minden régióban található alkalmazásokhoz:

- [Azure Resource Manager-sablon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |


További információ: [How to enable system-assigned managed identity for Azure Spring Cloud application](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)(Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure Spring Cloud alkalmazáshoz).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Abban a régióban érhető el, Azure Stack Edge szolgáltatás elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |

Tekintse meg az alábbi listát az Azure-beli virtuális Virtual Machine Scale Sets (az elérhető régiókban) felügyelt identitásának konfigurálásához:

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |

Tekintse meg az alábbi listát az Azure-beli virtuális Virtual Machines (ahol elérhető régiókban) felügyelt identitásának konfigurálásához:

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDK-k](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | [Elérhető a támogatott régiókban](../../virtual-machines/image-builder-overview.md#regions) | Nem érhető el | Nem érhető el | Nem érhető el |

Ha meg szeretne ismerkedni a felügyelt identitás konfigurálásával a Azure VM Image Builder (az elérhető régiókban), tekintse meg a Image Builder [áttekintését.](../../virtual-machines/image-builder-overview.md#permissions)
### <a name="azure-signalr-service"></a>Azure SignalR Service

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |
| Felhasználó által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |

Az alábbi listában konfigurálhatja a felügyelt identitást a Azure SignalR Service (ahol elérhetők a régiókban):

- [Azure Resource Manager-sablon](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Elérhető abban a régióban, ahol Azure Resource Mover szolgáltatás elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

A következő dokumentumban a következő Azure Resource Mover:

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Az Azure AD-hitelesítést támogató Azure-szolgáltatások

A következő szolgáltatások támogatják az Azure AD-hitelesítést, és olyan ügyfélszolgáltatásokkal tesztelték őket, amelyek felügyelt identitásokat használnak az Azure-erőforrásokhoz.

### <a name="azure-resource-manager"></a>Azure Resource Manager

A következő listában konfigurálhatja a hozzáférését a Azure Resource Manager:

- [Hozzáférés hozzárendelése Azure Portal](howto-assign-access-portal.md)
- [Hozzáférés hozzárendelése a PowerShell használatával](howto-assign-access-powershell.md)
- [Hozzáférés hozzárendelése az Azure CLI-n keresztül](howto-assign-access-CLI.md)
- [Hozzáférés hozzárendelése Azure Resource Manager sablonnal](../../role-based-access-control/role-assignments-template.md)

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Elérhető][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Elérhető][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Elérhető][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Elérhető][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-sql"></a>Azure SQL

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Elérhető][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Elérhető][check] |

### <a name="azure-data-explorer"></a>Azure Adatkezelő

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Elérhető][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://<account>.<region>.kusto.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Elérhető][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Elérhető][check] |
| Azure Government |  | ![Elérhető][check] |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-blobok és -üzenetsorok

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Elérhető][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Elérhető][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Elérhető][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Elérhető][check] |

> [!Note]
> A Microsoft Power BI [a felügyelt identitásokat is támogatja.](../../stream-analytics/powerbi-output-managed-identity.md)


[check]: media/services-support-managed-identities/check.png "Elérhető"
