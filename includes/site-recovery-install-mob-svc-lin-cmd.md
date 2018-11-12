---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019150"
---
1. Másolja a telepítőt egy helyi mappába (például ügynökszámítógépen) a kiszolgálón, amely számára védelmet kíván. A terminálban futtassa a következő parancsokat:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. A mobilitási szolgáltatás telepítéséhez futtassa a következő parancsot:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Miután a telepítés befejeződött, a mobilitási szolgáltatás regisztrálva kell lenniük a konfigurációs kiszolgálón. Futtassa a konfigurációs kiszolgálót regisztrálja a mobilitási szolgáltatás a következő parancsot:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>A mobilitási szolgáltatás telepítőjének parancssor

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Paraméter|Type (Típus)|Leírás|Lehetséges értékek|
|-|-|-|-|
|-r |Kötelező|Megadja, hogy a mobilitási szolgáltatás (MS) telepítve kell lennie, vagy MasterTarget (MT) telepítve kell lennie.|MS </br> MT|
|-d |Választható|Hely, ahol a mobilitási szolgáltatás telepítve van.|/usr/local/ASR|
|-v|Kötelező|Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. </br> </br>- **VMware**: használja ezt az értéket, ha egy virtuális gépen futó mobilitási szolgáltatás telepítése *VMware vSphere ESXi-gazdagépek*, *Hyper-V-gazdagépek*, és *fizikai kiszolgálók*. </br> - **Azure**: használja ezt az értéket, ha az ügynök telepítése az Azure IaaS virtuális gépen.| VMware </br> Azure|
|-q|Választható|Megadja, hogy a telepítő futtatásához csendes módban.| –|


#### <a name="mobility-service-configuration-command-line"></a>A mobilitási szolgáltatás konfigurációs parancssor

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Paraméter|Type (Típus)|Leírás|Lehetséges értékek|
|-|-|-|-|
|-i |Kötelező|IP-címét a konfigurációs kiszolgáló|Bármilyen érvényes IP-cím|
|-P |Kötelező|A rendszer menti a fájlt, amennyiben a kapcsolat hozzáférési kód a fájl teljes elérési útja|Bármely érvényes mappa|
