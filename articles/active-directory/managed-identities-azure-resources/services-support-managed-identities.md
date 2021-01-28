---
title: Felügyelt identitásokat támogató Azure-szolgáltatások – Azure AD
description: Az Azure-erőforrások és az Azure AD-hitelesítés felügyelt identitásait támogató szolgáltatások listája
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
ms.openlocfilehash: 0f7c97e5d84b3ee2d40c714625d0158fe60f2c95
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986073"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Felügyelt identitás használatával bármely olyan szolgáltatás hitelesítése végezhető, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. Folyamatban van a felügyelt identitások integrálása az Azure-erőforrások és az Azure AD-hitelesítés között az Azure-ban. Térjen vissza gyakran a frissítésekhez.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

A következő Azure-szolgáltatások támogatják az Azure-erőforrások felügyelt identitásait:


### <a name="azure-api-management"></a>Azure API Management

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |

Tekintse át az alábbi listát az Azure API Management felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Resource Manager-sablon](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | Nem érhető el  | ![Elérhető][check] |

Tekintse át az alábbi listát az Azure-alkalmazások konfigurációjának felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check] |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure App Service (a régiókban, ahol elérhető):

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sablon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-kompatibilis Kubernetes

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Az Azure arc-kompatibilis Kubernetes jelenleg [támogatja a rendszerhez rendelt identitást](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes). A felügyelt szolgáltatás identitásának tanúsítványát minden Azure arc-kompatibilis Kubernetes-ügynök használja az Azure-nal való kommunikációhoz.

### <a name="azure-automanage"></a>Azure Automanage

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

A felügyelt identitás újrakonfigurálásához tekintse meg a következő dokumentumot, ha áthelyezte az előfizetést egy új bérlőhöz:
* [Sérült automanage-fiók javítása](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás [Azure-tervezetekkel](../../governance/blueprints/overview.md)való használatához:

- [Azure Portal – tervezet-hozzárendelés](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – tervezet-hozzárendelés](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


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
| Rendszer által hozzárendelt | Linux: előzetes verzió<br>Windows: nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Linux: előzetes verzió<br>Windows: nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Container Instances (a régiókban, ahol elérhető):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Container Registry feladatokhoz (a régiókban, ahol elérhető):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Data Factory v2 (a régiókban, ahol elérhető):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát az Azure Digital Twins felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portal](~/articles/digital-twins/how-to-enable-managed-identities.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el  | Nem érhető el  | Nem érhető el |

### <a name="azure-firewall-policy"></a>Azure Firewall házirend

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el  | Nem érhető el  | Nem érhető el |

### <a name="azure-functions"></a>Azure Functions

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check]  |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Functions (a régiókban, ahol elérhető):

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sablon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Data Factory v2 (a régiókban, ahol elérhető):

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető abban a régióban, ahol az Azure import export szolgáltatás elérhető | Előnézet | Elérhető | Elérhető |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |


További információ: [felügyelt identitások használata az Azure Kubernetes szolgáltatásban](../../aks/use-managed-identity.md).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem elérhető | ![Elérhető][check] |


Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Logic Apps (a régiókban, ahol elérhető):

- [Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sablon](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |

További információ: [felügyelt identitások használata a Azure Machine learning használatával](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Policy (a régiókban, ahol elérhető):

- [Azure Portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az-policy-assignment-create)
- [Azure Resource Manager-sablonok](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Service Fabric alkalmazások felügyelt identitása](../../service-fabric/concepts-managed-identity.md) minden régióban elérhető.

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | nem érhető el |
| Felhasználó által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el |Nem érhető el |

A következő listában megtekintheti az Azure Service Fabric-alkalmazások felügyelt identitásának konfigurálását az összes régióban:

- [Azure Resource Manager-sablon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |


További információ: az [Azure Spring Cloud Application rendszerhez rendelt felügyelt identitásának engedélyezése](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető abban a régióban, ahol a Azure Stack Edge Service elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Előnézet | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Előnézet | ![Elérhető][check] |

Tekintse át az alábbi listát az Azure Virtual Machine Scale Sets felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Előnézet | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Előnézet | ![Elérhető][check] |

Tekintse át az alábbi listát az Azure Virtual Machines felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDK-k](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM-rendszerkép-készítő

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | [Elérhető a támogatott régiókban](../../virtual-machines/image-builder-overview.md#regions) | Nem érhető el | Nem érhető el | Nem érhető el |

Az Azure virtuálisgép-rendszerkép-készítő felügyelt identitásának konfigurálásával kapcsolatos információkért lásd a [rendszerkép-szerkesztő áttekintését](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Azure SignalR szolgáltatás

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |
| Felhasználó által hozzárendelt | Előnézet | Előnézet | Nem elérhető | Előnézet |

Tekintse át az alábbi listát az Azure Signaler szolgáltatás felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Resource Manager-sablon](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD-hitelesítést támogató Azure-szolgáltatások

A következő szolgáltatások támogatják az Azure AD-hitelesítést, és az Azure-erőforrásokhoz felügyelt identitásokat használó ügyféloldali szolgáltatásokkal lettek tesztelve.

### <a name="azure-resource-manager"></a>Azure Resource Manager

A Azure Resource Manager elérésének konfigurálásához tekintse meg a következő listát:

- [Hozzáférés kiosztása Azure Portal használatával](howto-assign-access-portal.md)
- [Hozzáférés kiosztása a PowerShell használatával](howto-assign-access-powershell.md)
- [Hozzáférés kiosztása az Azure CLI-n keresztül](howto-assign-access-CLI.md)
- [Hozzáférés kiosztása Azure Resource Manager sablonon keresztül](../../role-based-access-control/role-assignments-template.md)

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://management.azure.com/`| ![Elérhető][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Elérhető][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://vault.azure.net`| ![Elérhető][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Elérhető][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://datalake.azure.net/` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-sql"></a>Azure SQL

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://database.windows.net/` | ![Elérhető][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Elérhető][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://eventhubs.azure.net` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://servicebus.azure.net`  | ![Elérhető][check] |
| Azure Government |  | ![Elérhető][check] |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-blobok és-várólisták

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Elérhető][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Elérhető][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://*.asazure.windows.net` | ![Elérhető][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Elérhető][check] |

> [!Note]
> A Microsoft Power BI [támogatja a felügyelt identitásokat](../../stream-analytics/powerbi-output-managed-identity.md)is.


[check]: media/services-support-managed-identities/check.png "Elérhető"
