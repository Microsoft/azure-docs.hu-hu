---
title: Az Azure Spring Cloud hibaelhárítása a Virtual Network szolgáltatásban
description: Hibaelhárítási útmutató az Azure Spring Cloud Virtual Network szolgáltatáshoz.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698229"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Az Azure Spring Cloud hibaelhárítása a Virtual Networks szolgáltatásban

Ez a dokumentum segítséget nyújt az Azure Spring Cloud virtuális hálózatokon való használatakor felmerülő különféle problémák megoldásában.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Probléma merült fel egy Azure Spring Cloud Service-példány létrehozásával

Az Azure Spring Cloud-példány létrehozásához elegendő engedély szükséges ahhoz, hogy a példányt a virtuális hálózathoz lehessen telepíteni.  A Spring Cloud Service-példánynak saját maga is biztosítania kell [Az Azure Spring Cloud Service engedélyt a virtuális hálózat számára](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Ha a Azure Portal használatával állítja be az Azure Spring Cloud Service-példányt, akkor a Azure Portal érvényesíti az engedélyeket.

Az Azure Spring Cloud Service-példány az [Azure CLI](/cli/azure/get-started-with-azure-cli)használatával történő beállításához ellenőrizze a következőket:

- Az előfizetés aktív.
- A helyet az Azure Spring Cloud támogatja.
- A példányhoz tartozó erőforráscsoport már létre van hozva.
- Az erőforrás neve megfelel az elnevezési szabálynak. Csak kisbetűket, számokat és kötőjeleket tartalmazhat. Az első karakternek betűnek kell lennie. Az utolsó karakternek betűnek vagy számnak kell lennie. Az értéknek 2 és 32 karakter közöttinek kell lennie.

Ha az Azure Spring Cloud Service-példányt a Resource Manager-sablonnal szeretné beállítani, tekintse át [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../azure-resource-manager/templates/template-syntax.md)című témakört.

### <a name="common-creation-issues"></a>Gyakori létrehozási problémák

| Hibaüzenet | A hiba kijavítása |
|------|------|
| Az Azure Spring Cloud által létrehozott erőforrásokat a szabályzat nem engedélyezte. | A hálózati erőforrások akkor jönnek létre, amikor üzembe helyezi az Azure Spring Cloudt a saját virtuális hálózatán. Ellenőrizze, hogy van-e [Azure Policy](../governance/policy/overview.md) definiálva a létrehozás blokkolásához. Nem sikerült létrehozni az erőforrásokat a következő hibaüzenetben:. |
| A megadott alhálózatok útválasztási táblákhoz vannak társítva. Kérjük, szüntesse meg őket. | Jelenleg nem támogatott az Azure Spring Cloud üzembe helyezése a meglévő útválasztási táblázatokhoz társított alhálózatban, majd az újbóli próbálkozás. |
| A szükséges forgalom nem allowlisted. | Az [Azure Spring Cloud VNET való futtatásával](spring-cloud-vnet-customer-responsibilities.md) kapcsolatban a szükséges adatforgalom allowlisted érdekében tekintse meg az ügyfél feladatait. |

## <a name="my-application-cant-be-registered"></a>Az alkalmazásom nem regisztrálható

Ez a probléma akkor fordul elő, ha a virtuális hálózat egyéni DNS-beállításokkal van konfigurálva. Ebben az esetben az Azure Spring Cloud által használt magánhálózati DNS-zóna nem hatékony. Adja hozzá a Azure DNS IP-168.63.129.16 a felsőbb rétegbeli DNS-kiszolgálóként az egyéni DNS-kiszolgálón.

## <a name="other-issues"></a>Egyéb problémák

[Gyakori Azure Spring Cloud-problémák elhárítása](./spring-cloud-troubleshoot.md).