---
title: A térbeli elemzési tároló telepítése és futtatása – Computer Vision
titleSuffix: Azure Cognitive Services
description: A térbeli elemzési tároló segítségével azonosíthatja a személyeket és a távolságokat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 9989c6ea6b75203d43c37854caef7fdcbc321779
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519027"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>A térbeli elemzési tároló telepítése és futtatása (előzetes verzió)

A térbeli elemzési tároló lehetővé teszi a valós idejű adatfolyam-videók elemzését a személyek közötti térbeli kapcsolatok, a mozgásuk és a fizikai környezetekben található objektumok közötti interakciók megismerése érdekében. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást a </a> Azure Portal standard S1 szintjéhez a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * A térbeli elemzési tároló futtatásához a létrehozott erőforrás kulcsára és végpontra lesz szüksége. Később a kulcsot és a végpontot fogja használni.


### <a name="spatial-analysis-container-requirements"></a>Térbeli elemzési tárolóra vonatkozó követelmények

A térbeli elemzési tároló futtatásához egy [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)-val rendelkező számítási eszközre van szükség. Javasoljuk, hogy a [Azure stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) -t GPU-gyorsítással használja, azonban a tároló minden olyan asztali gépen fut, amely megfelel a minimális követelményeknek. Ezt az eszközt a gazdaszámítógépként fogjuk megtekinteni.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-eszköz](#tab/azure-stack-edge)

Az Azure Stack Edge egy szolgáltatásként nyújtott hardveres megoldás, és egy hálózati adatátviteli képességekkel rendelkező AI-kompatibilis Edge számítástechnikai eszköz. A részletes előkészítési és beállítási utasításokért tekintse meg az [Azure stack Edge dokumentációját](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Asztali gép](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Minimális hardverkövetelmények

* 4 GB RAM-memória
* 4 GB GPU RAM
* 8 magos processzor
* 1 NVIDIA Tesla T4 GPU
* 20 GB HDD terület

#### <a name="recommended-hardware"></a>Ajánlott hardver

* 32 GB RAM-memória
* 16 GB GPU RAM
* 8 magos processzor
* 2 NVIDIA Tesla T4 GPU
* 50 GB SSD-terület

Ebben a cikkben a következő szoftvercsomagok letöltésére és telepítésére lesz szüksége. A gazdagépnek képesnek kell lennie a következők futtatására (útmutatásért lásd alább):

* [NVIDIA grafikus illesztőprogramok](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) és [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Az [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (többfolyamatos szolgáltatás) beállításai.
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) és [NVIDIA – Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) futtatókörnyezet.

#### <a name="azure-vm-with-gpu"></a>[Azure-beli virtuális gép GPU-val](#tab/virtual-machine)
A példánkban egy K80 GPU-val rendelkező [NC sorozatú virtuális gépet](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) fogunk használni.

---

| Követelmény | Leírás |
|--|--|
| Kamera | A térbeli elemzési tároló nem kötődik egy adott kamera-márkához. A kamera eszközének a következőnek kell lennie: a Real-Time Streaming Protocol (RTSP) és a H. 264 kódolás támogatása, a gazdaszámítógép számára elérhetőnek kell lennie, és képesnek kell lennie a folyamatos átvitelre a 15FPS és az 1080p felbontásban |
| Linux operációs rendszer | Az [Ubuntu Desktop 18,04 LTS](http://releases.ubuntu.com/18.04/) -et telepíteni kell a gazdagépre.  |


## <a name="request-approval-to-run-the-container"></a>Kérelem jóváhagyása a tároló futtatásához

Töltse ki és küldje el a [kérelem űrlapját](https://aka.ms/csgate) a tároló futtatásához jóváhagyás kéréséhez.

Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amelyhez a tárolót fogja használni. Az űrlap elküldése után az Azure Cognitive Services csapata áttekinti és e-mailben értesíti Önt a döntésről.

> [!IMPORTANT]
> * Az űrlapon az Azure-előfizetési AZONOSÍTÓhoz társított e-mail-címet kell használnia.
> * A tároló futtatásához használt Computer Vision erőforrást a jóváhagyott Azure-előfizetés azonosítójával kell létrehozni.

A jóváhagyást követően a tárolót a cikk későbbi részében leírtak szerint is futtathatja a Microsoft Container Registry (MCR) szolgáltatásból.

Ha az Azure-előfizetését nem hagyták jóvá, nem fogja tudni futtatni a tárolót.

## <a name="set-up-the-host-computer"></a>A gazdaszámítógép beállítása

Azt javasoljuk, hogy a gazdaszámítógéphez használjon Azure Stack Edge-eszközt. Ha egy **virtuális** gépet használ, kattintson az **asztali gép** lehetőségre.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-eszköz](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Számítás konfigurálása az Azure Stack Edge portálon 
 
A térbeli elemzés a Azure Stack Edge számítási funkcióit használja egy AI-megoldás futtatásához. A számítási funkciók engedélyezéséhez győződjön meg a következőket: 

* [Csatlakoztatta és aktiválta](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) Azure stack Edge-eszközét. 
* Az eszköz eléréséhez a PowerShell 5,0-es vagy újabb verzióját futtató Windows rendszerű ügyfélrendszer van.  
* Kubernetes-fürt üzembe helyezéséhez konfigurálnia kell az Azure Stack Edge-eszközt a [Azure Portal](https://portal.azure.com/) **helyi felhasználói felületén** : 
  1. Engedélyezze a számítási funkciót az Azure Stack Edge-eszközön. A számítás engedélyezéséhez nyissa meg az eszköz webes felületének **számítás** lapját. 
  2. Válassza ki a számításhoz engedélyezni kívánt hálózati adaptert, majd kattintson az **Engedélyezés** gombra. Ezzel létrehoz egy virtuális kapcsolót az eszközön az adott hálózati adapteren.
  3. Hagyja üresen a Kubernetes teszt csomópontjának IP-címeit és a Kubernetes külső szolgáltatások IP-címeit.
  4. Kattintson az **Alkalmaz** gombra. A művelet két percet is igénybe vehet. 

![Számítás konfigurálása](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Peremhálózati számítási szerepkör beállítása és IoT Hub erőforrás létrehozása

A [Azure Portal](https://portal.azure.com/)navigáljon az Azure stack Edge-erőforráshoz. Az **Áttekintés** oldalon vagy a navigációs listában kattintson a peremhálózati számítási első **lépések** gombra. Az  **Edge-számítás konfigurálása**   csempén kattintson a **Konfigurálás** elemre. 

![Hivatkozás](media/spatial-analysis/configure-edge-compute-tile.png)

Az **Edge-számítás konfigurálása**   lapon válasszon ki egy meglévő IoT hub, vagy hozzon létre egy újat. Alapértelmezés szerint a rendszer egy standard (S1) árképzési szintet használ egy IoT Hub erőforrás létrehozásához. Ha ingyenes szintű IoT Hub erőforrást szeretne használni, hozzon létre egyet, majd jelölje ki. A IoT Hub erőforrás ugyanazt az előfizetést és erőforráscsoportot használja, amelyet az Azure Stack Edge-erőforrás használ. 

Kattintson a **Létrehozás** lehetőségre. A IoT Hub erőforrás létrehozása néhány percet is igénybe vehet. A IoT Hub erőforrás létrejötte után a rendszer frissíti az **Edge számítási csempe beállítása** frissítést az új konfiguráció megjelenítéséhez. Annak ellenőrzéséhez, hogy a peremhálózati számítási szerepkör konfigurálva van-e, válassza a **konfiguráció megtekintése** lehetőséget a **számítás konfigurálása**   csempén.

Amikor a peremhálózati eszközön beállította a peremhálózat számítási szerepkört, két eszközt hoz létre: egy IoT eszközt és egy IoT Edge eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. A Azure IoT Edge futtatókörnyezet már fut a IoT Edge eszközön.

> [!NOTE]
> * A IoT Edge-eszközök jelenleg csak a Linux platformot támogatják. Az Azure Stack Edge-eszköz hibaelhárításával kapcsolatos segítségért lásd a [naplózási és hibaelhárítási](spatial-analysis-logging.md) cikket.
> * Ha többet szeretne megtudni arról, hogyan konfigurálható IoT Edge eszköz egy proxykiszolgálón keresztüli kommunikációra, tekintse meg a [IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációhoz](../../iot-edge/how-to-configure-proxy-support.md#azure-portal) című témakört.

###  <a name="enable-mps-on-azure-stack-edge"></a>MPS engedélyezése Azure Stack Edge-ben 

1. Futtasson egy Windows PowerShell-munkamenetet rendszergazdaként. 

2. Győződjön meg arról, hogy a Windows távfelügyeleti szolgáltatása fut az ügyfélen. A PowerShell-terminálon használja a következő parancsot: 
    
    ```powershell
    winrm quickconfig
    ```
    
    Ha a tűzfalra vonatkozó kivételek jelennek meg, ellenőrizze a hálózati kapcsolatok típusát, és tekintse meg a [rendszerfelügyeleti](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) webszolgáltatások dokumentációját.

3. Rendeljen hozzá egy változót az eszköz IP-címéhez. 
    
    ```powershell
    $ip = "<device-IP-address>" 
    ```
    
4. Ha hozzá szeretné adni az eszköz IP-címét az ügyfél megbízható gazdagépek listájához, használja a következő parancsot: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Indítsa el a Windows PowerShell-munkamenetet az eszközön. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Ha a rendszer kéri, adja meg a jelszót. Használja ugyanazt a jelszót, amelyet a helyi webes felhasználói felületen való bejelentkezéshez használ. A helyi webes felhasználói felület alapértelmezett jelszava `Password1` .

Írja be az `Start-HcsGpuMPS` MPS szolgáltatás indítását az eszközön. 

Az Azure Stack Edge-eszköz hibaelhárításával kapcsolatos segítségért lásd: [az Azure stack Edge-eszköz hibaelhárítása](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[Asztali gép](#tab/desktop-machine)

Kövesse ezeket az utasításokat, ha a gazdaszámítógép nem Azure Stack peremhálózati eszköz.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Az NVIDIA CUDA Toolkit és az NVIDIA Graphics-illesztőprogramok telepítése a gazdagépen

A szükséges NVIDIA grafikus illesztőprogramok és a CUDA Toolkit telepítéséhez használja a következő bash-szkriptet.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Indítsa újra a gépet, és futtassa a következő parancsot.

```bash
nvidia-smi
```

A következő kimenetnek kell megjelennie.

![NVIDIA-illesztőprogram kimenete](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>A Docker CE és az NVIDIA-docker2 telepítése a gazdagépen

Telepítse a Docker CE-t a gazdagépen.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Telepítse az *NVIDIA-Docker-2* szoftvercsomagot.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>NVIDIA MPS engedélyezése a gazdaszámítógépen

> [!TIP]
> * Ne telepítse az MPS-t, ha a GPU számítási funkciója kevesebb, mint 7. x (előfeltételként). Lásd a [CUDA kompatibilitását](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) ismertető témakört. 
> * Futtassa az MPS utasításokat a gazdaszámítógépen lévő terminál-ablakban. Nincs a Docker-tároló példányán belül.

A legjobb teljesítmény és kihasználtság érdekében konfigurálja a gazda számítógép GPU (ka) t az [NVIDIA többfolyamatos szolgáltatás (mp)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)számára. Futtassa az MPS utasításokat a gazdaszámítógépen lévő terminál-ablakban.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Azure IoT Edge konfigurálása a gazdaszámítógépen

A térbeli elemzési tároló a gazdagépen való üzembe helyezéséhez hozzon létre egy [Azure IoT hub](../../iot-hub/iot-hub-create-through-portal.md) szolgáltatás egy példányát a standard (S1) vagy az ingyenes (F0) árképzési szint használatával. 

Azure IoT Hub-példány létrehozása az Azure CLI használatával. Szükség esetén cserélje le a paramétereket. Azt is megteheti, hogy az Azure-IoT Hub is létrehozhatja a [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
További információ: [régió támogatása](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) az elérhető régiókban.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Telepítenie kell [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 1.0.9 verzióját. A megfelelő verzió letöltéséhez kövesse az alábbi lépéseket:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Másolja a generált listát.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Telepítse a Microsoft GPG nyilvános kulcsot.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Frissítse a csomagok listáját az eszközön.

```bash
sudo apt-get update
```

A 1.0.9 kiadásának telepítése:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

A következő lépésként regisztrálja a gazdagépet IoT Edge eszközként a IoT Hub-példányban egy [kapcsolatok karakterlánc](../../iot-edge/how-to-register-device.md?view=iotedge-2018-06)használatával.

Az IoT Edge eszközt az Azure-IoT Hubhoz kell kötnie. A korábban létrehozott IoT Edge eszközről kell másolnia a kapcsolódási karakterláncot. Azt is megteheti, hogy az alábbi parancsot futtatja az Azure CLI-ben.

```bash
sudo az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

Nyissa meg a gazdagépet a  `/etc/iotedge/config.yaml` szerkesztéshez. Cserélje le `ADD DEVICE CONNECTION STRING HERE` a karakterláncot a és a közötti értékre. Mentse és zárja be a fájlt. Futtassa ezt a parancsot a IoT Edge szolgáltatás újraindításához a gazdaszámítógépen.

```bash
sudo systemctl restart iotedge
```

Telepítse a térbeli elemzési tárolót IoT-modulként a gazdagépen a [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) vagy az [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows)használatával. Ha a portált használja, állítsa a rendszerkép URI-JÁT a Azure Container Registry helyére. 

Az alábbi lépések segítségével helyezheti üzembe a tárolót az Azure CLI használatával.

#### <a name="azure-vm-with-gpu"></a>[Azure-beli virtuális gép GPU-val](#tab/virtual-machine)

Egy GPU-val rendelkező Azure-beli virtuális gép a térbeli elemzések futtatására is használható. Az alábbi példa egy K80 GPU-val rendelkező [NC sorozatú](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) virtuális gépet fog használni.

#### <a name="create-the-vm"></a>A virtuális gép létrehozása

Nyissa meg a [virtuális gép létrehozása](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) varázslót a Azure Portal.

Adja meg a virtuális gép nevét, és válassza ki a régiót (USA) az USA nyugati régiója 2. Ügyeljen arra, hogy `Availability Options` "nem szükséges infrastruktúra-redundancia" értékű legyen. A virtuális gép megfelelő méretének megtalálásához tekintse meg az alábbi ábrát, amely a teljes konfigurációt és a következő lépést ismerteti. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.png" alt-text="A virtuális gép konfigurációjának részletei." lightbox="media/spatial-analysis/virtual-machine-instance-details.png":::

A virtuális gép méretének megkereséséhez válassza az "összes méret" lehetőséget, majd tekintse meg a "nem prémium szintű Storage virtuálisgép-méretek" listáját alább látható módon.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Virtuális gépek méretei." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Ezután válassza a **NC6** vagy a **NC6_Promo** lehetőséget.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="promóciós kiválasztás" lightbox="media/spatial-analysis/promotional-selection.png":::

Ezután hozza létre a virtuális gépet. A létrehozást követően navigáljon a virtuálisgép-erőforráshoz a Azure Portalban, és válassza ki `Extensions` a bal oldali ablaktáblán. Ekkor megjelenik a bővítmények ablak az összes elérhető bővítménnyel. Válassza `NVIDIA GPU Driver Extension` a létrehozás lehetőséget, majd fejezze be a varázslót.

A bővítmény sikeres alkalmazása után navigáljon a Azure Portal virtuális gép főoldalára, és kattintson a elemre `Connect` . A virtuális gép SSH-n vagy RDP-n keresztül érhető el. Az RDP hasznos lesz, mivel lehetővé teszi a láthatóvá tevő ablak megtekintését (később a magyarázattal). Az RDP-hozzáférés konfigurálásához kövesse [ezeket a lépéseket](../../virtual-machines/linux/use-remote-desktop.md) , és nyisson meg egy távoli asztali kapcsolatot a virtuális géppel.

### <a name="verify-graphics-drivers-are-installed"></a>A grafikus illesztőprogramok telepítésének ellenőrzése

A következő parancs futtatásával ellenőrizze, hogy a grafikus illesztőprogramok telepítése sikeres volt-e. 

```bash
nvidia-smi
```

A következő kimenetnek kell megjelennie.

![NVIDIA-illesztőprogram kimenete](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>A Docker CE és az NVIDIA-docker2 telepítése a virtuális gépen

Futtassa a következő parancsokat egy időben a Docker CE és az NVIDIA-docker2 telepítéséhez a virtuális gépen.

Telepítse a Docker CE-t a gazdagépen.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Telepítse az *NVIDIA-Docker-2* szoftvercsomagot.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Most, hogy beállította és konfigurálta a virtuális gépet, kövesse az alábbi lépéseket a Azure IoT Edge konfigurálásához. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Azure IoT Edge konfigurálása a virtuális gépen

A térbeli elemzési tároló virtuális gépen való üzembe helyezéséhez hozzon létre egy [Azure IoT hub](../../iot-hub/iot-hub-create-through-portal.md) szolgáltatás egy példányát a standard (S1) vagy az ingyenes (F0) díjszabási szint használatával.

Azure IoT Hub-példány létrehozása az Azure CLI használatával. Szükség esetén cserélje le a paramétereket. Azt is megteheti, hogy az Azure-IoT Hub is létrehozhatja a [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
További információ: [régió támogatása](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) az elérhető régiókban.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Telepítenie kell [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 1.0.9 verzióját. A megfelelő verzió letöltéséhez kövesse az alábbi lépéseket:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Másolja a generált listát.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Telepítse a Microsoft GPG nyilvános kulcsot.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Frissítse a csomagok listáját az eszközön.

```bash
sudo apt-get update
```

A 1.0.9 kiadásának telepítése:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Ezután regisztrálja a virtuális gépet IoT Edge eszközként a IoT Hub-példányban egy [kapcsolatok karakterlánc](../../iot-edge/how-to-register-device.md?view=iotedge-2018-06)használatával.

Az IoT Edge eszközt az Azure-IoT Hubhoz kell kötnie. A korábban létrehozott IoT Edge eszközről kell másolnia a kapcsolódási karakterláncot. Azt is megteheti, hogy az alábbi parancsot futtatja az Azure CLI-ben.

```bash
sudo az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

A virtuális gépen nyissa meg a  `/etc/iotedge/config.yaml` szerkesztéshez. Cserélje le `ADD DEVICE CONNECTION STRING HERE` a karakterláncot a és a közötti értékre. Mentse és zárja be a fájlt. Futtassa ezt a parancsot a IoT Edge szolgáltatás újraindításához a virtuális gépen.

```bash
sudo systemctl restart iotedge
```

Telepítse a térbeli elemzési tárolót IoT-modulként a virtuális gépen a [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) vagy az [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows)-ből. Ha a portált használja, állítsa a rendszerkép URI-JÁT a Azure Container Registry helyére. 

Az alábbi lépések segítségével helyezheti üzembe a tárolót az Azure CLI használatával.

---

### <a name="iot-deployment-manifest"></a>IoT üzembe helyezési jegyzéke

Ha több gazdagépen szeretné egyszerűsíteni a tárolók telepítését, létrehozhat egy üzembe helyezési jegyzékfájlt a tároló-létrehozási beállítások és a környezeti változók megadásához. Az [Azure stack Edge-hez](https://go.microsoft.com/fwlink/?linkid=2142179), [más asztali gépekhez](https://go.microsoft.com/fwlink/?linkid=2152270)és a githubon található [GPU-val rendelkező Azure-beli virtuális géphez](https://go.microsoft.com/fwlink/?linkid=2152189) tartozó üzembe helyezési jegyzékfájlra példa látható.

A következő táblázat a IoT Edge modul által használt különféle környezeti változókat mutatja be. Ezeket a fent hivatkozott telepítési jegyzékfájlban is megadhatja a (z) `env` attribútumának használatával `spatialanalysis` :

| Beállítás neve | Érték | Leírás|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Információ Részletes | Naplózási szint, válasszon egyet a két érték közül|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Ne módosítsa|
| ARCHON_PERF_MARKER| hamis| Állítsa igaz értékre a teljesítmény naplózásához, ellenkező esetben hamis értéket kell megadni.| 
| ARCHON_NODES_LOG_LEVEL | Információ Részletes | Naplózási szint, válasszon egyet a két érték közül|
| OMP_WAIT_POLICY | PASSZÍV | Ne módosítsa|
| QT_X11_NO_MITSHM | 1 | Ne módosítsa|
| APIKEY | az API-kulcs| Gyűjtsön ezt az értéket Azure Portalból a Computer Vision-erőforrásból. Az erőforráshoz tartozó **kulcs és végpont** szakaszban találja. |
| SZÁMLÁZÁS | a végpont URI-ja| Gyűjtsön ezt az értéket Azure Portalból a Computer Vision-erőforrásból. Az erőforráshoz tartozó **kulcs és végpont** szakaszban találja.|
| EULA | fogadja el | Ezt az értéket úgy kell beállítani, hogy *fogadja* a tároló futtatását |
| KIJELZŐ | : 1 | Ennek az értéknek meg kell egyeznie a gazdaszámítógép kimenetével `echo $DISPLAY` . Azure Stack Edge-eszközök nem rendelkeznek kijelzővel. Ez a beállítás nem alkalmazható|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Adja hozzá ezt a környezeti változót, ha a GPU **nem** T4 vagy NVIDIA 2080 ti|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Adja hozzá ezt a környezeti változót, ha a GPU **nem** T4 vagy NVIDIA 2080 ti|
| KEY_ENV | Kiegészítő titkosítási kulcs | A környezeti változó hozzáadása, ha a Video_URL egy eltorzított karakterlánc |
| IV_ENV | Inicializálási vektor | A környezeti változó hozzáadása, ha a Video_URL egy eltorzított karakterlánc|

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](#billing).

Miután frissítette az [Azure stack Edge-eszközök](https://go.microsoft.com/fwlink/?linkid=2142179), az [asztali gépek](https://go.microsoft.com/fwlink/?linkid=2152270) vagy az Azure-beli virtuális gép ( [GPU](https://go.microsoft.com/fwlink/?linkid=2152189) -val) üzembe helyezési jegyzékét a saját beállításaival és a műveletek kiválasztásával, az alábbi [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) -paranccsal telepítheti a tárolót a gazdagépen IoT Edge modulként.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Paraméter  |Leírás  |
|---------|---------|
| `--hub-name` | Az Azure-IoT Hub neve. |
| `--content` | A telepítési fájl neve. |
| `--target-condition` | A gazdagéphez tartozó IoT Edge eszköz neve. |
| `-–subscription` | Előfizetés-azonosító vagy-név. |

Ez a parancs elindítja az üzemelő példányt. Az üzembe helyezés állapotának megtekintéséhez navigáljon az Azure IoT Hub példányának oldalára a Azure Portal. Az állapot *417-ként jelenhet meg – az eszköz telepítési konfigurációja nincs beállítva* , amíg az eszköz be nem fejeződik a tároló lemezképének letöltése, és nem indul el.

## <a name="validate-that-the-deployment-is-successful"></a>A telepítés sikerességének ellenőrzése

Több módon is ellenőrizheti, hogy a tároló fut-e. Keresse meg a *futásidejű állapotot* az Azure IoT hub-példányban található térbeli elemzési modul **IoT Edge moduljának beállításainál** a Azure Portal. Ellenőrizze, hogy a *futásidejű állapot* **kívánt értéke** és a **jelentett érték** *fut*-e.

![Példa a központi telepítés ellenőrzésére](./media/spatial-analysis/deployment-verification.png)

Miután a telepítés befejeződött, és a tároló fut, a **gazdaszámítógép** elkezdi az események küldését az Azure IoT hubba. Ha a `.debug` műveletek verzióját használta, megjelenik egy megjelenítő ablak a telepítési jegyzékfájlban konfigurált összes kamerához. Mostantól megadhatja a figyelni kívánt vonalakat és zónákat a központi telepítési jegyzékben, és az utasításokat követve újra üzembe helyezheti azokat. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>A térbeli elemzés által végrehajtott műveletek konfigurálása

[Térbeli elemzési műveletekkel](spatial-analysis-operations.md) konfigurálja a tárolót a csatlakoztatott kamerák használatára, konfigurálja a műveleteket, és így tovább. Minden konfigurált kamera-eszközön a térbeli elemzés műveletei a JSON-üzenetek kimeneti streamjét eredményezik, amelyet az Azure IoT Hub-példánya küld.

## <a name="redeploy-or-delete-the-deployment"></a>Az üzemelő példány újbóli üzembe helyezése vagy törlése

Ha frissítenie kell az üzemelő példányt, meg kell győződnie arról, hogy a korábbi központi telepítések sikeresen telepítve vannak, vagy törölnie kell IoT Edge eszköz központi telepítését, amely nem fejeződött be. Ellenkező esetben ezek a központi telepítések továbbra is folytatódnak, így a rendszer rossz állapotban marad. Használhatja a Azure Portal vagy az [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows)-t.

## <a name="use-the-output-generated-by-the-container"></a>A tároló által generált kimenet használata

Ha el szeretné kezdeni a tároló által generált kimenet felhasználását, tekintse meg a következő cikkeket:

*   Használja az Azure Event hub SDK-t a kiválasztott programozási nyelvhez az Azure IoT Hub-végponthoz való kapcsolódáshoz és az események fogadásához. További információért lásd: [eszközről a felhőbe irányuló üzenetek olvasása a beépített végpontról](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) . 
*   Állítsa be az üzenet-útválasztást az Azure-IoT Hub az események más végpontoknak való elküldéséhez, vagy mentse az eseményeket az Azure-Blob Storageba stb.). További információ: [IoT hub üzenet-útválasztás](../../iot-hub/iot-hub-devguide-messages-d2c.md) . 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Térbeli elemzés futtatása rögzített videofájl-fájllal

Térbeli elemzést is használhat rögzített vagy élő videóval. A rögzített videó térbeli elemzésének használatához próbálkozzon egy videofájl rögzítésével és MP4-fájlként való mentésével. Hozzon létre egy blob Storage-fiókot az Azure-ban, vagy használjon egy meglévőt. Ezután frissítse a blob Storage következő beállításait a Azure Portalban:
    1. A **letiltani** **kívánt biztonságos átvitel** módosítása
    2. A **Blobok nyilvános hozzáférésének engedélyezése** **engedélyezve**

Navigáljon a **tároló** szakaszhoz, vagy hozzon létre egy új tárolót, vagy használjon egy meglévőt. Ezután töltse fel a videofájlokat a tárolóba. Bontsa ki a fájl beállításait a feltöltött fájlhoz, és válassza az **sas előállítása** lehetőséget. Ügyeljen arra, hogy a **lejárati dátum** elég hosszú legyen a tesztelési időszak lefedéséhez. Az **engedélyezett protokollok** beállítása *http* -re (a *https* nem támogatott).

Kattintson a **sas-jogkivonat és URL-cím előállítása** elemre, és másolja a blob sas URL-címét. Cserélje le a `https` - `http` t, és tesztelje az URL-címet egy olyan böngészőben, amely támogatja a videolejátszás használatát.

Cserélje le az `VIDEO_URL` [Azure stack Edge-eszköz](https://go.microsoft.com/fwlink/?linkid=2142179), [asztali számítógép](https://go.microsoft.com/fwlink/?linkid=2152270)vagy Azure-beli [virtuális gép](https://go.microsoft.com/fwlink/?linkid=2152189) üzembe helyezési jegyzékfájljában a GPU-val a létrehozott URL-címet az összes gráfhoz. Állítsa `VIDEO_IS_LIVE` be `false` , majd telepítse újra a térbeli elemzési tárolót a frissített jegyzékfájl használatával. Lásd az alábbi példát.

A térbeli elemzési modul elkezdi a videofájl felhasználását, és folyamatosan automatikusan újrajátszható.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémák merülnek fel a tároló indításakor vagy futtatásakor, tekintse meg a gyakori problémák [telemetria és hibaelhárítási](spatial-analysis-logging.md) lépéseit. Ez a cikk a naplók létrehozásával és gyűjtésével, valamint a rendszerállapot gyűjtésével kapcsolatos információkat is tartalmaz.

## <a name="billing"></a>Számlázás

A térbeli elemzési tároló számlázási adatokat küld az Azure-nak az Azure-fiókja Computer Vision erőforrásának használatával. A térbeli elemzés a nyilvános előzetes verzióban jelenleg ingyenesen használható. 

Az Azure Cognitive Services-tárolók nem rendelkeznek licenccel a mérési/számlázási végponthoz való csatlakozás nélkül. Engedélyeznie kell a tárolókat, hogy mindig a számlázási végponttal kommunikáljanak a számlázási adatokkal. Cognitive Services tárolók nem küldenek ügyféladatokat, például az elemzett videót vagy képet a Microsoftnak.


## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a térbeli elemzési tároló letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A térbeli elemzés a Docker Linux-tárolója.
* A rendszer letölti a tároló lemezképeit a Microsoft Container Registryból.
* A Container images szolgáltatás IoT-modulként fut Azure IoT Edgeban.
* A tároló konfigurálása és üzembe helyezése a gazdagépen.

## <a name="next-steps"></a>Következő lépések

* [Felhasználók üzembe helyezése webes alkalmazásokban](spatial-analysis-web-app.md)
* [Térbeli elemzési műveletek konfigurálása](spatial-analysis-operations.md)
* [Naplózás és hibaelhárítás](spatial-analysis-logging.md)
* [Kamera elhelyezése – útmutató](spatial-analysis-camera-placement.md)
* [A zóna és a vonal elhelyezési útmutatója](spatial-analysis-zone-line-placement.md)