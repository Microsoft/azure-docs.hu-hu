---
title: A szabályzatok megfelelőségének naplózása és kezelése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Policy a beépített szabályzatok használatára Azure Machine Learning annak biztosítására, hogy a munkaterületei megfeleljenek a követelményeknek.
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544366"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Machine Learning naplózása és kezelése Azure Policy használatával

A [Azure Policy](../governance/policy/index.yml) egy irányítási eszköz, amely lehetővé teszi, hogy az Azure-erőforrások megfeleljenek a szabályzatoknak. A Azure Machine Learning használatával a következő házirendeket rendelheti hozzá:

| Szabályzat | Leírás |
| ----- | ----- |
| **Ügyfél által felügyelt kulcs** | Annak naplózása vagy betartatása, hogy a munkaterületeknek ügyfél által felügyelt kulcsot kell-e használniuk. |
| **Privát hivatkozás** | Annak naplózása vagy betartatása, hogy a munkaterületek használnak-e privát végpontot a virtuális hálózattal való kommunikációhoz. |
| **Privát végpont** | Konfigurálja az Azure Virtual Network alhálózatot, amelyben létre kell hozni a magánhálózati végpontot. |
| **saját DNS zóna** | Konfigurálja a magánhálózati DNS-zónát a privát hivatkozáshoz. |
| **Felhasználó által hozzárendelt felügyelt identitás** | Annak naplózása vagy betartatása, hogy a munkaterületek felhasználó által hozzárendelt felügyelt identitást használnak-e. |

A házirendek különböző hatókörökön állíthatók be, például az előfizetés vagy az erőforráscsoport szintjén. További információ: [Azure Policy dokumentáció](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Beépített szabályzatok

A Azure Machine Learning olyan házirendeket biztosít, amelyek a Azure Machine Learningval kapcsolatos gyakori forgatókönyvekhez használhatók. Ezeket a házirend-definíciókat hozzárendelheti meglévő előfizetéséhez, vagy felhasználhatja azokat a saját egyéni definícióinak létrehozásához. A Azure Machine Learning beépített házirendjeinek teljes listáját a [Azure Machine learning beépített szabályzatai](../governance/policy/samples/built-in-policies.md#machine-learning)című témakörben tekintheti meg.

A Azure Machine Learninghöz kapcsolódó beépített szabályzat-definíciók megtekintéséhez kövesse az alábbi lépéseket:

1. Lépjen __Azure Policy__ a [Azure Portal](https://portal.azure.com).
1. Válassza a __definíciók__ lehetőséget.
1. A __Típus mezőben__ válassza a _beépített_ lehetőséget, és a __kategóriánál__ válassza a __Machine learning__ lehetőséget.

Itt kiválaszthatja a szabályzat-definíciókat a megtekintéshez. Egy definíció megtekintése közben a __hozzárendelés__ hivatkozásra kattintva hozzárendelheti a szabályzatot egy adott hatókörhöz, és konfigurálhatja a szabályzat paramétereit. További információ: [szabályzatok társítása – portál](../governance/policy/assign-policy-portal.md).

A házirendeket [Azure PowerShell](../governance/policy/assign-policy-powershell.md), az [Azure CLI](../governance/policy/assign-policy-azurecli.md)és a [sablonok](../governance/policy/assign-policy-template.md)használatával is hozzárendelheti.

## <a name="workspace-encryption-with-customer-managed-key"></a>Munkaterület titkosítása az ügyfél által felügyelt kulccsal

Azt szabályozza, hogy a munkaterületet ügyfél által felügyelt kulccsal kell-e titkosítani, vagy egy Microsoft által felügyelt kulccsal a metrikák és a metaadatok titkosításához. Az ügyfél által felügyelt kulcs használatával kapcsolatos további információkért tekintse meg az adattitkosítási cikk [Azure Cosmos db](concept-data-encryption.md#azure-cosmos-db) szakaszát.

A szabályzat konfigurálásához állítsa be a Effect paramétert a __naplózás__ vagy a __Megtagadás__ értékre. Ha __naplózásra__ van beállítva, az ügyfél által felügyelt kulcs nélkül is létrehozhat egy munkaterületet, és a tevékenység naplójában létrejön egy figyelmeztetési esemény.

Ha a házirend a __Megtagadás__ értékre van állítva, akkor nem hozhat létre munkaterületet, ha az ügyfél által felügyelt kulcsot ad meg. Az ügyfél által felügyelt kulcs nélküli munkaterületek létrehozására tett kísérlet a következőhöz hasonló hibaüzenetet eredményez, `Resource 'clustername' was disallowed by policy` és hibát hoz létre a tevékenység naplójában. Ennek a hibának a részeként a rendszer a szabályzat azonosítóját is visszaadja.

## <a name="workspace-should-use-private-link"></a>A munkaterületnek privát hivatkozást kell használnia

Azt szabályozza, hogy a munkaterület használ-e Azure Private-hivatkozást az Azure Virtual Network való kommunikációhoz. A privát hivatkozás használatával kapcsolatos további információkért lásd: [privát hivatkozás beállítása munkaterülethez](how-to-configure-private-link.md).

A szabályzat konfigurálásához állítsa be a Effect paramétert a __naplózás__ vagy a __Megtagadás__ értékre. Ha a __naplózás__ beállítás be van állítva, létrehozhat egy munkaterületet privát hivatkozás nélkül, és a rendszer figyelmeztetési eseményt hoz létre a tevékenység naplójában.

Ha a házirend a __Megtagadás__ értékre van állítva, akkor nem hozhat létre munkaterületet, hacsak nem használ privát hivatkozást. A munkaterületek privát hivatkozás nélküli létrehozására tett kísérlet hibát eredményez. A hibát a tevékenység naplójában is naplózza a rendszer. A rendszer ennek a hibának a részeként visszaadja a házirend-azonosítót.

## <a name="workspace-should-use-private-endpoint"></a>A munkaterületnek privát végpontot kell használnia

A munkaterületet úgy konfigurálja, hogy egy Azure-Virtual Network megadott alhálózatán belül hozzon létre egy privát végpontot.

A szabályzat konfigurálásához állítsa a Effect paramétert __DeployIfNotExists__ értékre. Állítsa a __privateEndpointSubnetID__ az ALHÁLÓZAT Azure Resource Manager azonosítójára.
## <a name="workspace-should-use-private-dns-zones"></a>A munkaterületnek privát DNS-zónákat kell használnia

A munkaterületet egy privát DNS-zóna használatára konfigurálja, felülbírálva a magánhálózati végpont alapértelmezett DNS-feloldását.

A szabályzat konfigurálásához állítsa a Effect paramétert __DeployIfNotExists__ értékre. Állítsa a __privateDnsZoneId__ a használni kívánt magánhálózati DNS-zóna Azure Resource Manager azonosítójára. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>A munkaterületnek felhasználó által hozzárendelt felügyelt identitást kell használnia

Azt szabályozza, hogy a munkaterületet rendszerhez rendelt felügyelt identitás (alapértelmezett) vagy felhasználó által hozzárendelt felügyelt identitás használatával hozza-e létre. A munkaterület felügyelt identitása a társított erőforrások, például az Azure Storage, a Azure Container Registry, a Azure Key Vault és az Azure Application Insights elérésére szolgál. További információ: [felügyelt identitások használata a Azure Machine learning használatával](how-to-use-managed-identities.md).

A szabályzat konfigurálásához állítsa a Effect paramétert __naplózás__, __Megtagadás__ vagy __Letiltva__ értékre. Ha __naplózásra__ van beállítva, létrehozhat egy munkaterületet a felhasználó által hozzárendelt felügyelt identitás megadása nélkül is. A rendszer hozzárendelt identitást használ, és egy figyelmeztetési eseményt hoz létre a tevékenység naplójában.

Ha a házirend a __Megtagadás__ értékre van állítva, akkor nem hozhat létre munkaterületet, ha a létrehozási folyamat során nem ad meg felhasználó által hozzárendelt identitást. Egy munkaterületet felhasználó által hozzárendelt identitás megadása nélkül próbál meg létrehozni egy hibát. A hibát a tevékenység naplójában is naplózza a rendszer. A rendszer ennek a hibának a részeként visszaadja a házirend-azonosítót.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Policy dokumentációja](../governance/policy/overview.md)
* [Beépített szabályzatok a Azure Machine Learninghoz](policy-reference.md)
* [A biztonsági szabályzatok használata a Azure Security Center](../security-center/tutorial-security-policy.md)