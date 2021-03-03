---
title: Kubelet-naplók megtekintése az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan tekintheti meg a hibaelhárítási információkat a kubelet-naplókból az Azure Kubernetes Service (ak) csomópontjairól
services: container-service
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 355c665db2627fe04595a8b519b16bd475ebcadf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735148"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Kubelet-naplók lekérése Azure Kubernetes Service- (AKS-) fürtcsomópontokból

Az AK-fürtök működtetésének részeként előfordulhat, hogy át kell tekintenie a naplókat a probléma elhárításához. A Azure Portal beépített funkciói lehetővé teszi a naplók megtekintését az AK-beli [fő összetevőkhöz][aks-master-logs] vagy [tárolóhoz egy AK-fürtben][azure-container-logs]. Esetenként előfordulhat, hogy hibaelhárítási célból a *kubelet* -naplókat egy AK-csomópontból kell lekérnie.

Ez a cikk bemutatja, hogyan `journalctl` tekintheti meg a *kubelet* -naplókat egy AK-csomóponton.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

## <a name="create-an-ssh-connection"></a>SSH-kapcsolatok létrehozása

Először hozzon létre egy SSH-kapcsolatokat azzal a csomóponttal, amelyen meg kell tekinteni a *kubelet* -naplókat. Ezt a műveletet az SSH-ban az [Azure Kubernetes Service (ak)][aks-ssh] fürtcsomópontok dokumentuma részletezi.

## <a name="get-kubelet-logs"></a>Kubelet-naplók beszerzése

Miután csatlakozott a csomóponthoz, futtassa a következő parancsot a *kubelet* -naplók lekéréséhez:

```console
sudo journalctl -u kubelet -o cat
```

> [!NOTE]
> A Windows-csomópontok esetében a naplózási adatai a következőben találhatók, `C:\k` és a *további* paranccsal tekinthetők meg:
> ```
> more C:\k\kubelet.log
> ```

A következő minta kimenet a *kubelet* naplózási adatokat jeleníti meg:

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>Következő lépések

Ha további hibaelhárítási információkra van szüksége a Kubernetes-főkiszolgálón, tekintse meg a következőt: [Kubernetes fő csomópontjának naplói az AK-ban][aks-master-logs]

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: ./view-control-plane-logs.md
[azure-container-logs]: ../azure-monitor/containers/container-insights-overview.md