---
title: Egyéni parancsfájlok futtatása Linux rendszerű virtuális gépeken az Azure-ban
description: A Linux rendszerű virtuális gépek konfigurációs feladatainak automatizálása az egyéni parancsfájl-bővítmény V2 használatával
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: da7ade4b4724f8d155deb1c109587a311d03375c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931013"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Az egyéni Azure script Extension 2. verziójának használata Linux rendszerű virtuális gépekkel
Az egyéni szkriptek bővítményének 2. verziója letölti és futtatja a parancsfájlokat az Azure Virtual Machines szolgáltatásban. Ez a bővítmény az üzembe helyezés utáni konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs/felügyeleti feladat esetén hasznos. A szkripteket letöltheti az Azure Storage-ból vagy más elérhető Internet-helyről, vagy megadhatja a bővítmény futtatókörnyezetét. 

Az egyéni szkriptek bővítménye Azure Resource Manager-sablonokkal integrálódik. Azt is futtathatja az Azure CLI, a PowerShell vagy az Azure Virtual Machines REST API használatával.

Ez a cikk részletesen ismerteti, hogyan használhatja az egyéni szkriptek bővítményét az Azure CLI-ből, és hogyan futtathatja a bővítményt Azure Resource Manager sablon használatával. Ez a cikk a Linux rendszerekre vonatkozó hibaelhárítási lépéseket is tartalmaz.


Két Linux egyéni parancsfájl-bővítmény létezik:
* 1\. verzió – Microsoft. OSTCExtensions. CustomScriptForLinux
* 2\. verzió – Microsoft. Azure. Extensions. CustomScript

Váltson át új és meglévő központi telepítéseket, hogy az új 2-es verziót használja. Az új verzió a régi kiváltására készült. A migrálásához így elég megváltoztatni a nevet és a verziót, nincs szükség a bővítménykonfiguráció módosítására.


### <a name="operating-system"></a>Operációs rendszer

A Linux rendszerhez készült egyéni szkript a bővítmény támogatott bővítményi operációs rendszerén fut, további információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Parancsfájl helye

A bővítmény használatával az Azure Blob Storage hitelesítő adatait használhatja az Azure Blob Storage eléréséhez. Azt is megteheti, hogy a parancsfájl helye bármilyen lehet, ha a virtuális gép átirányítható erre a végpontra, mint például a GitHub, a belső fájlkiszolgáló stb.

### <a name="internet-connectivity"></a>Internetkapcsolat
Ha külsőleg le kell töltenie egy parancsfájlt, például a GitHubot vagy az Azure Storage-t, akkor további tűzfal/hálózati biztonsági csoport portjait kell megnyitnia. Ha például a parancsfájl az Azure Storage-ban található, az Azure NSG Service-címkék használatával engedélyezheti a hozzáférést a [tároláshoz](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Ha a parancsfájl egy helyi kiszolgálón található, akkor továbbra is szükség lehet további tűzfal/hálózati biztonsági csoport portjainak megnyitására.

### <a name="tips-and-tricks"></a>tippek és trükkök
* A bővítmény meghibásodásai legnagyobb arányban a szkriptben található szintaktikai hibák miatt jelentkeznek. Tesztelje, hogy a szkript fut-e hiba nélkül, és lássa el további naplózással a szkriptet, hogy könnyebb legyen a hiba helyét azonosítani.
* Írjon idempotens szkripteket, hogy ne okozzanak változásokat a rendszerben, ha véletlenül többször is futtatják őket.
* Győződjön meg arról, hogy a parancsfájlok futtatásakor nincs szükség felhasználói bevitelre.
* A szkript futtatásához 90 perc van engedélyezve, ami továbbra is a bővítmény sikertelen kiépítését eredményezi.
* Ne helyezzen újraindítást a parancsfájlba, ezért a rendszer a telepített többi bővítménnyel kapcsolatos problémákat okoz, és az újraindítás után a bővítmény nem fog folytatódni az újraindítás után. 
* Ha olyan szkripttel rendelkezik, amely újraindítást eredményez, telepítse az alkalmazásokat, és futtassa a parancsfájlokat stb. Be kell ütemezni az újraindítást egy cron-feladatokkal, vagy olyan eszközök használatával, mint a DSC, a Chef vagy a Puppet Extensions.
* A bővítmény csak egyszer futtatja a parancsfájlt, ha parancsfájlt szeretne futtatni minden rendszerindításkor, használhatja a [Cloud-init rendszerképet](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) , és használhat [parancsfájlokat rendszerindítási](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modulként. Azt is megteheti, hogy a parancsfájl segítségével létrehoz egy rendszerszintű szolgáltatási egységet.
* Ha egy parancsfájl futását szeretné ütemezni, a bővítmény használatával hozzon létre egy cron-feladatot. 
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha egy futó parancsfájl gyakoribb frissítési állapotát szeretné használni, létre kell hoznia a saját megoldását.
* Az egyéni szkriptek bővítménye nem támogatja natív módon a proxykiszolgálók használatát, azonban használhat olyan fájlátviteli eszközt, amely támogatja a parancsfájlban lévő proxykiszolgálót, például a *curl*-t. 
* Ne feledje, hogy a parancsfájlok vagy parancsok által felhasználható nem alapértelmezett címtárbeli helyeknek van logikája a kezeléséhez.
*  Ha egyéni parancsfájlt helyez üzembe az éles VMSS-példányokon, javasolt a JSON-sablonon keresztül telepíteni, és tárolni a parancsfájl-tárolási fiókot, ahol az SAS-tokent szabályozhatja. 


## <a name="extension-schema"></a>Bővítményséma

Az egyéni szkriptek bővítményének konfigurációja meghatározza a parancsfájl helyét és a futtatandó parancsot. Ezt a konfigurációt a konfigurációs fájlokban tárolhatja, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager sablonban. 

A bizalmas adatokat egy védett konfigurációban tárolhatja, amely titkosítva van, és csak a virtuális gépen végez visszafejtést. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos kódokat, például jelszót tartalmaz.

Ezeket az elemeket bizalmas adatokként kell kezelni, és meg kell adni a bővítmények által védett beállítások konfigurációjában. Az Azure virtuálisgép-bővítmény védett beállítási adatbeállításai titkosítottak, és csak a célként megadott virtuális gépen lettek visszafejtve.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Tulajdonságértékek

| Név | Érték/példa | Adattípus | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | dátum |
| közzétevő | Microsoft. számítás. bővítmények | sztring |
| type | CustomScript | sztring |
| typeHandlerVersion | 2.0 | int |
| fileUris (például) | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (például) | Python MyPythonScript.py \<My-param1 > | sztring |
| szkriptet. | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | sztring |
| skipDos2Unix (például) | false | logikai |
| időbélyeg (például) | 123456789 | 32 bites egész szám |
| storageAccountName (például) | examplestorageacct | sztring |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | sztring |

### <a name="property-value-details"></a>Tulajdonság értékének részletei
* `apiVersion`: a legnaprakészebb apiVersion a [erőforrás-kezelő](https://resources.azure.com/) vagy az Azure CLI használatával a következő paranccsal található meg `az provider list -o json`
* `skipDos2Unix`: (nem kötelező, logikai) kihagyhatja a parancsfájl-alapú dos2unix és-parancsfájlok átalakítását.
* `timestamp` (opcionális, 32 bites egész szám) Ez a mező csak a parancsfájl újbóli futtatásának elindításához használható a mező értékének módosításával.  Bármely egész érték elfogadható; csak az előző értéktől eltérő lehet.
  * `commandToExecute`: (**kötelező** , ha a parancsfájl nincs beállítva, string) a beléptetési pont parancsfájlját a végrehajtáshoz. Ezt a mezőt használja helyette, ha a parancs titkos kódokat, például jelszavakat tartalmaz.
* `script`: (**kötelező** , ha a commandToExecute nincs beállítva, string) a/bin/sh. által végrehajtott Base64 kódolású (és opcionálisan gzip'ed) parancsfájlt
* `fileUris`: (opcionális, karakterlánc-tömb) a letölteni kívánt fájl (ok) URL-címei.
* `storageAccountName`: (nem kötelező, karakterlánc) a Storage-fiók neve. Ha tárolási hitelesítő adatokat ad meg, az összes `fileUris`nak az Azure-Blobok URL-címeinek kell lennie.
* `storageAccountKey`: (nem kötelező, karakterlánc) a Storage-fiók elérési kulcsa


A következő értékek a nyilvános vagy a védett beállításokban állíthatók be, a bővítmény elveti az összes olyan konfigurációt, ahol az alábbi értékek a nyilvános és a védett beállításokban is be vannak állítva.
* `commandToExecute`
* `script`
* `fileUris`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de erősen ajánlott a védett beállítások használata.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el arra a virtuális gépre, amelyen a parancsfájl végre lesz hajtva.  A védett beállítások titkosítása csak az Azure-ban és a virtuális gépen ismert kulcs használatával történik. A rendszer elküldte a beállításokat a virtuális gépre, például ha a beállítások titkosítva vannak, a virtuális gépen titkosítva lesznek mentve. A titkosított értékek visszafejtéséhez használt tanúsítványt a virtuális gépen tárolja a rendszer, és a beállításokat (ha szükséges) a futtatáskor használja.

#### <a name="property-skipdos2unix"></a>Tulajdonság: skipDos2Unix

Az alapértelmezett érték false (hamis), ami azt jelenti **, hogy a dos2unix konverziót** hajt végre.

A CustomScript (Microsoft. OSTCExtensions. CustomScriptForLinux) előző verziója automatikusan átalakítja a DOS-fájlokat a UNIX-fájlokba a `\r\n` `\n`való lefordításával. Ez a fordítás továbbra is létezik, és alapértelmezés szerint be van kapcsolva. Ez a konverzió a fileUris-ből letöltött összes fájlra vagy a parancsfájl-beállításra vonatkozik a következő feltételek bármelyike alapján.

* Ha a bővítmény `.sh`, `.txt`, `.py`vagy `.pl` egyike, akkor a rendszer konvertálja. A parancsfájl-beállítás mindig megfelel ennek a feltételnek, mert feltételezi, hogy egy/bin/sh-vel futtatott parancsfájl, és a virtuális gép script.sh mentve lesz.
* Ha a fájl `#!`tel kezdődik.

A dos2unix-konverzió kihagyható úgy, hogy a skipDos2Unix értéke TRUE (igaz) értékre van állítva.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Tulajdonság: parancsfájl

A CustomScript támogatja a felhasználó által definiált parancsfájlok végrehajtását. A commandToExecute és a fileUris egyetlen beállításba való egyesítésére szolgáló parancsfájl-beállítások. Ahelyett, hogy az Azure Storage-ból vagy a GitHub-címről szeretné letölteni a fájlt, egyszerűen beállíthatja a szkriptet. A szkript használható a commandToExecute és a fileUris cseréjére.

A szkriptnek Base64 kódolással kell **rendelkeznie** .  A parancsfájl **opcionálisan** is gzip'ed. A parancsfájl-beállítás nyilvános vagy védett beállításokban is használható. A parancsfájl-paraméter adatmennyiségének maximális mérete 256 KB. Ha a parancsfájl mérete meghaladja ezt a méretet, a rendszer nem hajtja végre.

Például a következő parancsfájl mentve a fájl-vagy script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A megfelelő CustomScript parancsfájl-beállítást a következő parancs kimenetével lehet létrehozni.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

A parancsfájl opcionálisan gzip'ed a méret csökkentése érdekében (a legtöbb esetben). (A CustomScript automatikusan észleli a gzip tömörítés használatát.)

```sh
cat script | gzip -9 | base64 -w 0
```

A CustomScript a következő algoritmust használja a parancsfájlok végrehajtásához.

 1. a parancsfájl értékének hossza nem haladja meg a 256 KB-ot.
 1. a parancsfájl értékének Base64 dekódolása
 1. a Base64 dekódolású érték gunzip _kísérlete_
 1. a dekódolású (és opcionálisan kibontott) érték írása a lemezre (/var/lib/waagent/Custom-script/#/script.sh)
 1. a szkript végrehajtása _/bin/sh-c/var/lib/waagent/Custom-script/#/script.sh. használatával


## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. Az előző szakaszban részletezett JSON-séma használható Azure Resource Manager sablonban az egyéni parancsfájl-bővítmény futtatásához Azure Resource Manager sablon központi telepítésekor. Az egyéni szkriptek kiterjesztését tartalmazó minta sablon itt található, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Ezek a tulajdonságok nevei megkülönböztetik a kis-és nagybetűket. Az üzembe helyezési problémák elkerüléséhez használja az itt látható neveket.

## <a name="azure-cli"></a>Azure parancssori felület (CLI)
Ha az Azure CLI használatával futtatja az egyéni szkriptek bővítményét, hozzon létre egy konfigurációs fájlt vagy fájlt. Legalább a "commandToExecute" értékkel kell rendelkeznie.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

A parancs beállításait JSON formátumú karakterláncként is megadhatja. Ez lehetővé teszi, hogy a konfiguráció a végrehajtás során legyen meghatározva, külön konfigurációs fájl nélkül.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Példák az Azure parancssori felület használatára

#### <a name="public-configuration-with-script-file"></a>Nyilvános konfiguráció parancsfájl-fájllal

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI-parancs:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Parancsfájl nélküli nyilvános konfiguráció

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-parancs:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Nyilvános és védett konfigurációs fájlok

A parancsfájl URI azonosítójának megadásához egy nyilvános konfigurációs fájlt kell használnia. A futtatni kívánt parancs megadásához egy védett konfigurációs fájlt kell használnia.

Nyilvános konfigurációs fájl:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Védett konfigurációs fájl:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI-parancs:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Hibaelhárítás
Az egyéni szkriptek bővítményének futtatásakor a szkript az alábbi példához hasonló könyvtárba jön létre vagy töltődik le. A parancs kimenete `stdout` és `stderr` fájlokban is ebbe a könyvtárba kerül.

```bash
/var/lib/waagent/custom-script/download/0/
```

A hibakereséshez először ellenőrizze a Linux-ügynök naplóját, győződjön meg arról, hogy a bővítmény futott, majd ellenőrizze a következőt:

```bash
/var/log/waagent.log 
```

Meg kell keresnie a bővítmények végrehajtását, így a következőképpen fog kinézni:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Néhány Megjegyzés:
1. Az engedélyezés az, amikor a parancs elindul.
2. A letöltés az Azure-ból származó CustomScript-bővítmény-csomag letöltésére vonatkozik, nem a fileUris megadott parancsfájl-fájlokra.


Az Azure script bővítmény létrehoz egy naplót, amelyet itt talál:

```bash
/var/log/azure/custom-script/handler.log
```

Meg kell keresnie az egyéni végrehajtást, így a következőképpen fog kinézni:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```
Itt láthatja:
* Az enable parancs indítása ez a napló
* A bővítménynek átadott beállítások
* A kiterjesztés fájljának letöltése és eredménye.
* A futtatandó parancs és az eredmény.

Az egyéni szkriptek bővítményének végrehajtási állapotát az Azure CLI használatával is lekérheti:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A kimenet a következő szöveghez hasonlít:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Következő lépések
A kód, az aktuális problémák és a verziók megtekintéséhez lásd: [Custom-script-Extension-Linux](https://github.com/Azure/custom-script-extension-linux)adattár.

