---
title: Azure SQL felügyelt példány létrehozása az Azure arc-ban
description: Azure SQL felügyelt példány létrehozása az Azure arc-ban
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280400"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Azure SQL felügyelt példány létrehozása az Azure arc-ban

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Bejelentkezés az Azure arc-adatkezelőbe

Példány létrehozása előtt jelentkezzen be az Azure arc-adatkezelőbe, ha még nincs bejelentkezve.

```console
azdata login
```

Ekkor a rendszer kérni fogja a felhasználónevet, a jelszót és a rendszernévtért.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány létrehozása

A felügyelt forSQL elérhető létrehozási beállítások megtekintéséhez használja a következő parancsot:
```console
azdata arc sql mi create --help
```

SQL felügyelt példány létrehozásához használja a következő parancsot:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Példa:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  A névnek rövidebbnek kell lennie 13 karakternél, és meg kell felelnie a [DNS-elnevezési konvencióknak](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  A memória kiosztásának és a virtuális mag kiosztásának megadásakor használja ezt a képletet a létrehozás sikerességének biztosításához – mindegyik 1 virtuális mag legalább 4 GB RAM kapacitást kell biztosítania azon a Kubernetes-csomóponton, ahol az SQL felügyelt példány Pod fut.
>
>  SQL-példányok létrehozásakor az Azure-ban történő kiépítéskor ne használjon a nagybetűket a névben.
>
>  Az elérhető tárolási osztályok listázása a Kubernetes-fürtben `kubectl get storageclass` 


> [!NOTE]
> Ha automatizálni szeretné az SQL-példányok létrehozását, és el szeretné kerülni a rendszergazdai jelszó interaktív megadását, a `AZDATA_USERNAME` parancs futtatása előtt beállíthatja a és a `AZDATA_PASSWORD` környezeti változót a kívánt felhasználónévre és jelszóra `azdata arc sql mi create` .
> 
>  Ha az adatvezérlőt AZDATA_USERNAME és AZDATA_PASSWORD használatával hozta létre ugyanabban a terminál-munkamenetben, akkor a rendszer a AZDATA_USERNAME és a AZDATA_PASSWORD értékeit is felhasználja a felügyelt SQL-példány létrehozásához.

> [!NOTE]
> Az Azure SQL felügyelt példányának létrehozása nem regisztrálja az erőforrásokat az Azure-ban. Az erőforrás regisztrálásának lépései a következő cikkekben találhatók: 
> - [Naplók és metrikák megtekintése a Kibana és a Grafana használatával](monitor-grafana-kibana.md)
> - [Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Példány megtekintése az Azure arc-ban

A példány megtekintéséhez használja a következő parancsot:

```console
azdata arc sql mi list
```

A kimenetnek így kell kinéznie:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Ha AK-t vagy OpenShift-t vagy `kubeadm` -t használ, a külső IP-címet és a portszámot innen másolhatja, és a kedvenc eszközével csatlakozhat hozzá egy SQL Server-vagy Azure SQL-példányhoz, például Azure Data Studio vagy SQL Server Management studiohoz. Ha azonban a gyors üzembe helyezési virtuális gépet használja, a speciális utasításokért tekintse meg a [Kapcsolódás az Azure arc-kompatibilis SQL felügyelt példányhoz](connect-managed-instance.md) című cikket.


## <a name="next-steps"></a>Következő lépések
- [Kapcsolódás az Azure arc használatára képes SQL felügyelt példányhoz](connect-managed-instance.md)
- [Példány regisztrálása az Azure-ban és a példányra vonatkozó mérőszámok és naplók feltöltése](upload-metrics-and-logs-to-azure-monitor.md)
- [Felügyelt Azure SQL-példány üzembe helyezése Azure Data Studio használatával](create-sql-managed-instance-azure-data-studio.md)
