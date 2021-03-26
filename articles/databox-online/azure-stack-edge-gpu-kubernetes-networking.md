---
title: Az Kubernetes hálózatkezelés ismertetése Azure Stack Edge Pro-eszközön | Microsoft Docs
description: Ismerteti, hogyan működik a Kubernetes hálózatkezelés Azure Stack Edge Pro-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 139eeaa83629f3ef51f8eda414622f7da54dfab4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560250"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes hálózatkezelés a Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A Azure Stack Edge Pro-eszközön a számítási szerepkör konfigurálásakor létrejön egy Kubernetes-fürt. A Kubernetes-fürt létrehozása után a tároló alkalmazások a Kubernetes-fürtön helyezhetők üzembe a hüvelyben. A Kubernetes-fürtben a hüvelyek esetében különböző módokat használhat a hálózatkezeléshez. 

Ez a cikk egy Kubernetes-fürt hálózatkezelését mutatja be általánosságban, különösen az Azure Stack Edge Pro-eszköz kontextusában. 

## <a name="networking-requirements"></a>Hálózati követelmények

Íme egy példa egy tipikus kétrétegű alkalmazásra, amely a Kubernetes-fürtre van telepítve.

- Az alkalmazásnak van egy webkiszolgáló-kezelőfelülete és egy adatbázis-alkalmazása a háttérben. 
- Minden Pod hozzá van rendelve egy IP-cím, de ezek az IP-címek megváltozhatnak a pod újraindításakor és feladatátvétele esetén. 
- Minden alkalmazás több hüvelyből áll, és az összes Pod-replikán a forgalom terheléselosztása szükséges. 

![Kubernetes hálózati követelmények](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

A fenti forgatókönyv a következő hálózati követelményeket eredményezi:

 - A külső elérésű alkalmazást a Kubernetes-fürtön kívüli alkalmazásnak kell elérnie egy névvel vagy IP-címmel. 
 - A Kubernetes-fürtön belüli alkalmazások, például az előtér-és a háttér-hüvelyek itt kommunikálni tudnak egymással.

A fenti igények megoldásához bevezetünk egy Kubernetes szolgáltatást. 


## <a name="networking-services"></a>Hálózati szolgáltatások

A Kubernetes-szolgáltatások két típusa létezik: 

- **Fürt IP-szolgáltatása** – úgy gondolja, hogy az alkalmazás hüvelyéhez állandó végpontot biztosít. Az ezekhez a szolgáltatásokhoz társított pod nem érhető el a Kubernetes-fürtön kívülről. Az ezekkel a szolgáltatásokkal használt IP-cím a magánhálózaton található címterület alapján érhető el. 
- **TERHELÉSELOSZTÓ IP** -címe – például a fürt IP-szolgáltatása, de a társított IP-cím a külső hálózatról származik, és a Kubernetes-fürtön kívülről is elérhető.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Kubernetes hálózatkezelés Azure Stack Edge Pro-ban

A tarka, a Metallb és a Core DNS az összes olyan összetevő, amely a hálózatkezelésre van telepítve a Azure Stack Edge Pro-ban. 

- A **tarka** IP-címet rendel hozzá egy magánhálózati IP-tartományhoz, és konfigurálja a hálózatokat ezekhez a hüvelyekhez, így az egyik csomóponton található pod egy másik csomóponton tud kommunikálni a pod-vel. 
- A **Metallb** egy fürtön futó Pod gépen fut, és IP-címet rendel a Load Balancer típusú szolgáltatásokhoz. A terheléselosztó IP-címei a helyi felhasználói felületen keresztül megadott szolgáltatási IP-címtartomány közül vannak kiválasztva. 
- **Alapszintű DNS** – ez a bővítmény KONFIGURÁLJA a DNS-rekordok leképezési szolgáltatásának nevét a fürt IP-címére.

A Kubernetes-csomópontok és a külső szolgáltatások IP-címei az eszköz helyi felhasználói felületének **számítási hálózat** lapján érhetők el.

![Kubernetes IP-cím hozzárendelése helyi felhasználói felületen](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

Az IP-cím hozzárendelése a következőhöz:

- **Kubernetes csomópont IP**-címei: ez az IP-címtartomány a Kubernetes fő-és munkavégző csomópontjaihoz használatos. Ezek az IP-címek akkor használatosak, amikor a Kubernetes-csomópontok kommunikálnak egymással.

- **Kubernetes külső szolgáltatás IP**-címei: ez az IP-címtartomány a Kubernetes-fürtön kívül elérhető külső szolgáltatásokhoz (más néven a Load Balancer szolgáltatásokhoz) használatos.  


## <a name="next-steps"></a>Következő lépések

A Kubernetes hálózatkezelés konfigurálásához a Azure Stack Edge Pro-ban lásd:

- [Állapot nélküli alkalmazások kiküldése a Azure stack Edge Pro-on IoT Edge használatával](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Állapot nélküli alkalmazások kiküldése a Azure stack Edge Pro-n keresztül a kuebctl-on keresztül](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).