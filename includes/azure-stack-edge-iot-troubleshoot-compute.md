---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 34d0d55ba6eb403055be96758b57b7bd0c2ab704
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104988352"
---
A számítási feladatokkal kapcsolatos hibák elhárításához használja a IoT Edge ügynök futásidejű válaszait. Itt látható a lehetséges válaszok listája:

* 200 – OK
* 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
* 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
* 412 – a séma verziója érvénytelen a telepítési konfigurációban.
* 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
* 500 – hiba történt a IoT Edge futtatókörnyezetben.

További információ: [IoT Edge Agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

A következő hiba az Azure Stack Edge Pro-eszköz IoT Edge szolgáltatásával kapcsolatos.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>A számítási modulok ismeretlen állapottal rendelkeznek, és nem használhatók

#### <a name="error-description"></a>Hibaleírás

Az eszközön lévő összes modul ismeretlen állapotot mutat, és nem használható. Az ismeretlen állapot egy újraindítás után szűnik meg.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Javasolt megoldás

Törölje a IoT Edge szolgáltatást, majd telepítse újra a modul (oka) t. További információ: [Remove IoT Edge Service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>A modulok fut, de nem működnek

#### <a name="error-description"></a>Hibaleírás

A modul futtatókörnyezeti állapota futként jelenik meg, de a várt eredmények nem láthatók. 

Ezt az állapotot a modul útválasztási konfigurációjával kapcsolatos olyan probléma okozhatja, amely nem működik, vagy `edgehub` az üzenetek nem az elvárt módon vannak átirányítva. Megtekintheti a `edgehub` naplókat. Ha úgy látja, hogy hibák történtek, például nem sikerül csatlakozni a IoT Hub szolgáltatáshoz, akkor a leggyakoribb ok a kapcsolódási problémák. A kapcsolódási problémák oka az lehet, hogy az alapértelmezett portként használt AMPQ-port IoT Hub szolgáltatás általi kommunikációra van letiltva, vagy a webproxy-kiszolgáló blokkolja ezeket az üzeneteket.

#### <a name="suggested-solution"></a>Javasolt megoldás

Tegye a következőket:
1. A hiba elhárításához nyissa meg az eszköz IoT Hub erőforrását, és válassza ki a peremhálózati eszközét. 
1. Válassza a **modulok beállítása > futtatókörnyezet beállításai lehetőséget**. 
1. Adja hozzá a `Upstream protocol` környezeti változót, és rendelje hozzá a értékét `AMQPWS` . Az ebben az esetben konfigurált üzeneteket a rendszer a 443-es porton keresztül továbbítja WebSockets-kapcsolaton keresztül.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>A modulok futtatása fut, de nincs hozzárendelve IP-cím

#### <a name="error-description"></a>Hibaleírás

A modul futtatókörnyezeti állapota fut, de a tároló alkalmazásnak nincs hozzárendelve IP-címe. 

Ez az állapot azért van, mert a Kubernetes külső szolgáltatás IP-címeihez megadott IP-címek tartománya nem elegendő. Ezt a tartományt ki kell terjesztenie annak biztosítására, hogy minden telepített tároló vagy virtuális gép le legyen fedve.

#### <a name="suggested-solution"></a>Javasolt megoldás

Az eszköz helyi webes FELÜLETén hajtsa végre a következő lépéseket:
1. Lépjen a **számítás** lapra. Válassza ki azt a portot, amelyhez engedélyezte a számítási hálózatot. 
1. Adja meg a **Kubernetes külső szolgáltatási IP**-címeinek statikus, összefüggő IP-tartományát. A szolgáltatáshoz 1 IP-cím szükséges `edgehub` . Emellett minden IoT Edge-modulhoz és minden üzembe helyezni kívánt virtuális géphez egy IP-címmel kell rendelkeznie. 
1. Kattintson az **Alkalmaz** gombra. A módosított IP-címtartomány azonnal érvénybe lép.

További információ: [külső szolgáltatás IP-címeinek módosítása tárolók számára](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#change-external-service-ips-for-containers).

### <a name="configure-static-ips-for-iot-edge-modules"></a>Statikus IP-címek konfigurálása IoT Edge modulokhoz

#### <a name="problem-description"></a>A probléma leírása

A Kubernetes dinamikus IP-címeket rendel hozzá minden IoT Edge modulhoz az Azure Stack Edge Pro GPU-eszközön. A modulok statikus IP-címeinek konfigurálásához metódusra van szükség.

#### <a name="suggested-solution"></a>Javasolt megoldás

Megadhatja a IoT Edge modulok rögzített IP-címeit a K8s – kísérleti szakaszban leírtak szerint: 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Kubernetes-szolgáltatás közzététele fürt IP-szolgáltatásként belső kommunikációhoz

#### <a name="problem-description"></a>A probléma leírása

Alapértelmezés szerint a IoT-szolgáltatás típusa terheléselosztó típusú, és a rendszer külsőleg felé irányuló IP-címeket rendel hozzá. Előfordulhat, hogy nem szeretne külső IP-címet használni az alkalmazáshoz. Előfordulhat, hogy a KUbernetes-fürtön belül ki kell töltenie a hüvelyeket a többi hüvelyhez való hozzáféréshez, és nem külsőleg elérhető terheléselosztó szolgáltatásként. 

#### <a name="suggested-solution"></a>Javasolt megoldás

A létrehozási beállítások a K8s-experimented szakaszban használhatók. A következő szolgáltatási beállításnak a port kötéseivel kell működnie.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```
