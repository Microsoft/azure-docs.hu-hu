---
title: Kapcsolódás az Azure arc használatára képes SQL felügyelt példányhoz
description: Kapcsolódás az Azure arc használatára képes SQL felügyelt példányhoz
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: abd27e15ccf5b421e69e78b2b726d192ffdecacb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92372361"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Kapcsolódás az Azure arc használatára képes SQL felügyelt példányhoz

Ez a cikk azt ismerteti, hogyan kapcsolódhat az Azure arc használatára képes SQL felügyelt példányhoz. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Az Azure arc használatára képes SQL felügyelt példányok megtekintése

Az Azure arc használatára képes SQL felügyelt példány és a külső végpontok megtekintéséhez használja az alábbi parancsot:

```console
azdata arc sql mi list
```

A kimenetnek így kell kinéznie:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Ha AK-t vagy kubeadm-t vagy OpenShift-t használ, a külső IP-címet és a portszámot innen másolhatja, és a kedvenc eszközével csatlakozhat hozzá egy SQL Server-vagy Azure SQL-példányhoz, például Azure Data Studio vagy SQL Server Management Studiohoz.  Ha azonban a gyors üzembe helyezési virtuális gépet használja, az alábbi témakörben talál további információt arról, hogyan csatlakozhat az adott virtuális géphez az Azure-on kívülről. 

> [!NOTE]
> A vállalati szabályzatok letilthatják az IP-címet és a portot, különösen akkor, ha ez a nyilvános felhőben jön létre.

## <a name="connect"></a>Csatlakozás 

Kapcsolat Azure Data Studio, SQL Server Management Studio vagy SQLCMD

Nyissa meg Azure Data Studiot, és kapcsolódjon a példányhoz a fenti külső végpont IP-címével és portszámával. Ha Azure-beli virtuális gépet használ, szüksége lesz a _nyilvános_ IP-címére, amely azonosítható az [Azure-beli virtuális gépek üzembe helyezésével kapcsolatos speciális Megjegyzés](#special-note-about-azure-virtual-machine-deployments)használatával.

Például:

- Kiszolgáló: 52.229.9.30, 30913
- Felhasználónév: SA
- Password (jelszó): a megadott SQL-jelszó kiépítési időpontban

> [!NOTE]
> Azure Data Studio [megtekintheti az SQL felügyelt példány irányítópultját](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

A SQLCMD vagy a Linux vagy a Windows használatával való kapcsolódáshoz a következőhöz hasonló parancsot használhat. Ha a rendszer kéri, adja meg az SQL-jelszót:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Speciális Megjegyzés az Azure-beli virtuális gépek üzembe helyezéséről

Ha Azure-beli virtuális gépet használ, akkor a végpont IP-címe nem fogja megjeleníteni a nyilvános IP-címet. A külső IP-cím megkereséséhez használja a következő parancsot:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Ezután összekapcsolhatja a nyilvános IP-címet a porttal a csatlakozás elvégzéséhez.

Előfordulhat, hogy az SQL-példány portját is fel kell tenni a hálózati biztonsági átjárón (NSG) keresztül. A (NSG) szolgáltatáson keresztüli adatforgalom engedélyezéséhez hozzá kell adnia egy szabályt, amely a következő paranccsal végezhető el.

Egy szabály beállításához ismernie kell a NSG nevét, amely az alábbi parancs használatával deríthető fel:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Ha megkapta a NSG nevét, a következő parancs használatával adhat hozzá tűzfalszabály-szabályt. A példában szereplő értékek egy NSG-szabályt hoznak létre a 30913-es porthoz, és lehetővé teszik a csatlakozást **bármely** forrás IP-címről.  Ez nem az ajánlott biztonsági eljárás!  A hatékonyabb zárolás érdekében megadhat egy -forrás-cím-előtagok formátumú értéket, amely kifejezetten az ügyfél IP-címéhez vagy olyan IP-címtartományhoz tartozik, amely lefedi a csapat vagy a szervezet IP-címeit.

Cserélje le az `--destination-port-ranges` alábbi paraméter értékét a `azdata sql instance list` fenti F parancsból kapott portszámra.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Következő lépések

- [Az SQL felügyelt példány irányítópultjának megtekintése](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Az SQL felügyelt példányának megtekintése a Azure Portalban](view-arc-data-services-inventory-in-azure-portal.md)
