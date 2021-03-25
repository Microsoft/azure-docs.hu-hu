---
title: Az Azure Service Fabric parancssori felület használatának első lépései
description: Ez a dokumentum ismerteti az Azure Service Fabric parancssori felület használatát. Megtudhatja, hogyan csatlakozhat fürtökhöz, és hogyan kezelheti alkalmazásait.
author: jeffj6123
ms.topic: conceptual
ms.date: 5/19/2020
ms.author: jejarry
ms.openlocfilehash: ff322cb3633616e9004bc8fa25b048b6f1263008
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046334"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric parancssori felület

Az Azure Service Fabric parancssori felület (CLI) egy parancssori eszköz a Service Fabric-entitások használatához és kezeléséhez. A Service Fabric parancssori felület Windows- vagy Linux-fürtökön is használható. A Service Fabric parancssori felület bármilyen platformon fut, amely támogatja a Pythont.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Előfeltételek

A telepítés előtt győződjön meg arról, hogy a környezetben a Python és a pip is telepítve van. További információkért olvassa el a [pip rövid útmutató dokumentációját](https://pip.pypa.io/en/latest/quickstart/) és a hivatalos [Python-telepítési dokumentációt](https://wiki.python.org/moin/BeginnersGuide/Download).

A CLI a 2,7-es és a 3.6-os Python-verziókat támogatja, és a Python 3. x használata javasolt.

### <a name="service-fabric-target-runtime"></a>A Service Fabric cél futtatókörnyezete

A Service Fabric parancssori felület a Service Fabric SDK legfrissebb futtatókörnyezet-verzióját hivatott támogatni. A következő táblázat segítségével határozhatja meg, hogy melyik parancssori felületet telepítse:

| Parancssori felület verziója   | támogatott futtatókörnyezet-verzió |
|---------------|---------------------------|
| Legújabb (~ = 10) | Legújabb (~ = 7,1)            |
| 9.0.0         | 7.1                       |
| 8.0.0         | 6.5                       |
| 7.1.0         | 6.4                       |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6,2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6, 5.7                  |

A parancssori felület telepítendő célverzióját úgy is megadhatja, ha a `pip install` parancsot kiegészíti a `==<version>` kifejezéssel. Például az 1.1.0-s verzió esetén a szintaxis a következő:

```shell
pip install -I sfctl==1.1.0
```

Szükség esetén cserélje a `pip install` parancsot a korábban említett parancsra.

A Service Fabric parancssori felület kiadásaival kapcsolatos további információért tekintse meg a [GitHub-dokumentációt](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>A pip, a Python és a Service Fabric parancssori felület telepítése

Sokféleképpen telepítheti a pipet és a Pythont a platformra. Itt találja a Python 3 és a pip gyors telepítésének néhány lépését a főbb operációs rendszerekhez.

### <a name="windows"></a>Windows

Windows 10, Windows Server 2016 és Windows Server 2012 R2 esetén használja a normál telepítési utasításokat. A Python telepítője alapértelmezés szerint a pipet is telepíti.

1. Látogasson el a hivatalos [Python letöltési oldalra](https://www.python.org/downloads/), és töltse le a Python 3. x legújabb kiadását.

2. Indítsa el a telepítőt.

3. A parancssor alján válassza a **Python 3. x hozzáadása az elérési útra** lehetőséget.

4. Válassza az **Install Now** (Telepítés) lehetőséget, és fejezze be a telepítést.

Most megnyithat egy új parancsablakot, és lekérheti a Python és a pip verzióját.

```shell
python --version
pip --version
```

Ezután futtassa a következő parancsot az Azure Service Fabric CLI (sfctl) telepítéséhez, és tekintse meg a parancssori felület súgójának oldalát:

```shell
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu és a Linux Windows alrendszere

A Service Fabric parancssori felületének telepítéséhez futtassa a következő parancsokat:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Ezután a telepítést a következő paranccsal tesztelheti:

```bash
sfctl -h
```

Ha egy „parancs nem található” hiba jelenik meg, például a következő:

`sfctl: command not found`

Ellenőrizze, hogy a `~/.local/bin` elérhető-e a `$PATH` útvonalon:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .shellrc
```

Ha a Linux Windows alrendszerén végzett telepítés nem megfelelő mappaengedélyek miatt meghiúsul, szükség lehet a telepítés újbóli megkísérlésére emelt szintű engedélyekkel:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Service Fabric előzetes verzió támogatása)

A következő parancsok futtatásával telepítheti Service Fabric CLI-t a Red Hat-on:

```bash
sudo yum install -y python38
sudo yum install python38-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

A telepítés teszteléséhez tekintse meg az **Ubuntu és a Windows alrendszerek Linux** rendszeren című szakaszban említett lépéseket.

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

MacOS rendszeren javasoljuk, hogy a [HomeBrew csomagkezelőt](https://brew.sh) használja. Ha a HomeBrew még nincs telepítve, a következő parancs futtatásával telepítse:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Ezután a terminálból telepítse a legújabb Python 3. x, Pip és a Service Fabric CLI-t a következő parancsok futtatásával:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>A parancssori felület szintaxisa

A parancsok előtagja mindig a következő: `sfctl`. Az összes használható paranccsal kapcsolatos általános információkért használja az `sfctl -h` parancsot. Ha egyetlen paranccsal kapcsolatban van szüksége segítségre, használja az `sfctl <command> -h` parancsot.

A parancsok ismételhető szerkezetűek, és a parancs célja mindig megelőzi a műveletet.

```shell
sfctl <object> <action>
```

Ebben a példában az `<object>` az `<action>` művelet célpontja.

## <a name="select-a-cluster"></a>Fürt kiválasztása

A műveletek végrehajtása előtt ki kell választania egy fürtöt, amelyhez csatlakozni kíván. A következő parancs futtatásával például kiválaszthatja a `testcluster.com` nevű fürtöt, és csatlakozhat hozzá:

> [!WARNING]
> Éles környezetben ne használjon nem védett Service Fabric-fürtöket.

```shell
sfctl cluster select --endpoint http://testcluster.com:19080
```

A fürt végpontját `http` vagy `https` előtaggal kell ellátni. Tartalmaznia kell a HTTP-átjáróhoz tartozó portot. A port és a cím megegyezik a Service Fabric Explorer URL-címével.

A tanúsítvánnyal védett fürtök esetében megadhat egy PEM-kódolású tanúsítványt. Ez a tanúsítvány megadható egyetlen fájlként vagy egy tanúsítványból és egy kulcsból álló párként. Ha ez az önaláírt tanúsítvány nem rendelkezik hitelesítésszolgáltatói aláírással, a `--no-verify` beállítás átadásával kerülheti meg a hitelesítésszolgáltatói ellenőrzést.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

További információ: [Csatlakozás védett Azure Service Fabric-fürthöz](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Alapszintű műveletek

A fürt kapcsolatadatai több Service Fabric CLI-munkamenetben is megmaradnak. Egy Service Fabric-fürt kiválasztása után bármilyen Service Fabric-parancsot futtathat a fürtön.

A Service Fabric-fürt állapotának lekérdezéséhez például használja az alábbi parancsot:

```shell
sfctl cluster health
```

A parancs az alábbi kimenetet eredményezi:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Tippek és hibaelhárítás

Itt talál néhány javaslatot és tippet a gyakori problémák megoldásához.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Tanúsítvány konvertálása PFX formátumról PEM formátumra

A Service Fabric parancssori felület PEM- (.pem kiterjesztésű) fájlok formájában támogatja az ügyféloldali tanúsítványokat. Ha Windows rendszerből származó PFX-fájlokat lát, át kell alakítania a tanúsítványokat PEM formátumba. A PFX-fájlok PEM-fájlokká történő konvertálásához használja a következő parancsot:

```shell
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Ehhez hasonlóan a PEM-fájlok PFX-fájlokká történő konvertálásához a következő parancsot használhatja (itt nem kell jelszót megadnia):

```shell
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

További információt az [OpenSSL-dokumentációban](https://www.openssl.org/docs/) találhat.

### <a name="connection-problems"></a>Csatlakozási problémák

Egyes műveletek az alábbi üzenetet hozhatják létre:

`Failed to establish a new connection`

Ellenőrizze, hogy a megadott fürtvégpont elérhető-e és figyel-e. Továbbá ellenőrizze, hogy a Service Fabric Explorer felhasználói felülete elérhető-e a gazdagépen és a porton. A végpont frissítéséhez használja az `sfctl cluster select` parancsot.

### <a name="detailed-logs"></a>Részletes naplók

A részletes naplók gyakran hasznosak a hibák javításához vagy a problémák jelentéséhez. A `--debug` jelző növeli a kimenet részletességét.

### <a name="command-help-and-syntax"></a>Parancsok súgója és szintaxisa

Ha segítségre van szüksége egy bizonyos paranccsal vagy parancscsoporttal kapcsolatban, használja a `-h` jelzőt.

```shell
sfctl application -h
```

Egy másik példa:

```shell
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>A Service Fabric parancssori felület frissítése 

A Service Fabric parancssori felületének frissítéséhez futtassa a következő parancsokat (az eredeti telepítéskor használt paramétertől függően a `pip` helyett írjon be `pip3`-at):

```shell
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Következő lépések

* [Alkalmazás üzembe helyezése az Azure Service Fabric parancssori felülettel](service-fabric-application-lifecycle-sfctl.md)
* [A Service Fabric használatának első lépései Linuxon](service-fabric-get-started-linux.md)
