---
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301444"
---
1. Másolja a telepítőt egy helyi mappába (például ügynökszámítógépen) a kiszolgálón, amely számára védelmet kíván. A terminálban futtassa a következő parancsokat:
     ```
     cd /tmp;
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

|Paraméter|Typo|Leírás|Lehetséges értékek|
|-|-|-|-|
|-r |Kötelező|Megadja, hogy a mobilitási szolgáltatás (MS) telepítve kell lennie, vagy MasterTarget (MT) telepítve kell lennie.|MS <br /> MT|
|-d |Optional|Hely, ahol a mobilitási szolgáltatás telepítve van.|/usr/local/ASR|
|-v|Kötelező|Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. <br /> <br />- **VMware**: Használja ezt az értéket, ha egy virtuális gépen futó mobilitási szolgáltatás telepítése *VMware vSphere ESXi-gazdagépek*, *Hyper-V-gazdagépek*, és *fizikai kiszolgálók*. <br /> - **Azure**: Akkor használja ezt az értéket, ha ügynököt telepít egy Azure IaaS virtuális gépen.| VMware <br /> Azure|
|-q|Optional|Megadja, hogy a telepítő futtatásához csendes módban.| –|

#### <a name="mobility-service-configuration-command-line"></a>A mobilitási szolgáltatás konfigurációs parancssor

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Paraméter|Typo|Leírás|Lehetséges értékek|
|-|-|-|-|
|-i |Kötelező|IP-címét a konfigurációs kiszolgáló|Bármilyen érvényes IP-cím|
|-P |Kötelező|A rendszer menti a fájlt, amennyiben a kapcsolat hozzáférési kód a fájl teljes elérési útja|Bármely érvényes mappa|
<!--Update_Description: wording update-->
<!--ms.date: 03/05/2018-->