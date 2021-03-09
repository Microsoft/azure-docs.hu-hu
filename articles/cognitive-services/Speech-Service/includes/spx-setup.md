---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c939c1d061f4e01a00bc2b886056e42f0b059ad5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511123"
---
## <a name="download-and-install"></a>Letöltés és telepítés

#### <a name="windows-install"></a>[Windows-telepítés](#tab/windowsinstall)

Kövesse az alábbi lépéseket a Speech CLI Windows rendszeren történő telepítéséhez:

1. Windows rendszeren a platformhoz a [Microsoft Visual C++ terjeszthető változata szükséges a Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) -hez. Ha először telepíti az elsőt, újraindításra lehet szükség.
1. Telepítse a [.net Core 3,1 SDK](/dotnet/core/install/windows)-t.
2. Telepítse a Speech CLI-t a NuGet használatával a következő parancs megadásával:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0
   ```
`spx`A SPEECH parancssori felület súgójának megjelenítéséhez írja be a következőt:.

> [!NOTE]
> A NuGet alternatívájaként letöltheti és kicsomagolhatja a Speech CLI zip- [archívumot](https://aka.ms/speech/spx-zips.zip), megkeresheti és kinyerheti a platformot a `spx-zips` címtárból, és hozzáadhatja a `spx` **rendszerútvonal** -változó elérési útját.


### <a name="font-limitations"></a>Betűkészletre vonatkozó korlátozások

Windows rendszeren a Speech CLI csak a parancssorban elérhető betűkészleteket jeleníti meg a helyi számítógépen.
A [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) a Speech CLI által interaktív módon létrehozott összes betűtípust támogatja.

Ha fájlba, például egy Jegyzettömbbe vagy egy webböngészőbe (például a Microsoft Edge) küldi el a kimenetet, az összes betűtípust is megjelenítheti.

#### <a name="linux-install"></a>[Linux-telepítés](#tab/linuxinstall)

A következő Linux-disztribúciók támogatottak x64-architektúrák esetén a Speech CLI használatával:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04/18.04/20.04

> [!NOTE]
> A Speech SDK támogatja a további architektúrákat (nem a Speech CLI-t). További információ: [Tudnivalók a SPEECH SDK-ról](../speech-sdk.md).

A következő lépésekkel telepítheti a Speech CLI-t Linux rendszeren egy x64-es PROCESSZORon:

1. Telepítse a [.net Core 3,1 SDK](/dotnet/core/install/linux)-t.
2. Telepítse a Speech CLI-t a NuGet használatával a következő parancs megadásával:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

`spx`A SPEECH parancssori felület súgójának megjelenítéséhez írja be a következőt:.

> [!NOTE]
> A NuGet alternatívájaként letöltheti a bináris fájlokat a [zip Archive](https://aka.ms/speech/spx-zips.zip)-ben, kinyerheti `spx-netcore-30-linux-x64.zip` azt egy új `~/spx` könyvtárba, begépelheti a `sudo chmod +r+x spx` bináris fájlt, és hozzáadhatja az elérési utat a `~/spx` path rendszerváltozóhoz.


#### <a name="docker-install-windows-linux-macos"></a>[Docker-telepítés (Windows, Linux, macOS)](#tab/dockerinstall)

A következő lépésekkel telepítheti a Speech CLI-t egy Docker-tárolóba:

1. Ha még nincs telepítve, <a href="https://www.docker.com/get-started" target="_blank">telepítse a Docker Desktopot</a> a platformra.
2. Írja be a következő parancsot egy új parancssorba vagy terminálba:
   ```console   
   docker pull msftspeech/spx
   ```
3. Írja be ezt a parancsot. A Speech CLI-vel kapcsolatos súgóban talál:
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Könyvtár csatlakoztatása a tárolóhoz

A Speech CLI-eszköz fájlokként menti a konfigurációs beállításokat, és minden parancs végrehajtásakor betölti ezeket a fájlokat (kivéve a Súgó parancsokat).
Ha a Speech CLI-t egy Docker-tárolón belül használja, csatlakoztatnia kell egy helyi könyvtárat a tárolóból, így az eszköz képes tárolni vagy megkeresni a konfigurációs beállításokat, és így az eszköz képes olvasni vagy írni a parancshoz szükséges fájlokat, például hangfájlokat a beszédhez.

Windows rendszeren írja be ezt a parancsot egy helyi címtár-beszédfelismerési parancssori felület létrehozásához a tárolón belül:

`mkdir c:\spx-data`

Vagy Linux vagy macOS rendszeren írja be ezt a parancsot egy terminálba egy könyvtár létrehozásához, és tekintse meg az abszolút elérési útját:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Az abszolút elérési utat fogja használni a Speech CLI hívásakor.

### <a name="run-speech-cli-in-the-container"></a>A Speech CLI futtatása a tárolóban

Ez a dokumentáció a `spx` nem Docker-telepítésekben használt SPEECH CLI-parancsot mutatja be.
Amikor a `spx` parancsot egy Docker-tárolóban hívja meg, csatlakoztatnia kell egy könyvtárat a tárolóban a fájlrendszerhez, ahol a SPEECH CLI képes tárolni és megtalálni a konfigurációs értékeket, valamint a fájlok olvasását és írását.

Windows rendszeren a parancsok a következőképpen fognak kezdődni:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

Linux vagy macOS rendszeren a parancsok az alábbi példához hasonlóan jelennek meg. Cserélje le a `ABSOLUTE_PATH` értéket a csatlakoztatott könyvtár abszolút elérési útjára. Ezt az elérési utat az `pwd` előző szakaszban szereplő parancs adta vissza. 

Ha a kulcs és a régió beállítása előtt futtatja ezt a parancsot, hibaüzenet jelenik meg a kulcs és a régió beállításakor:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

A `spx` tárolóban telepített parancs használatához mindig adja meg a fent látható teljes parancsot, majd a kérés paramétereit.
Windows rendszeren például a következő parancs állítja be a kulcsot:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config --set @key SUBSCRIPTION-KEY
```

A parancssori eszközzel való nagyobb körű interakcióhoz egy BelépésiPont paraméter hozzáadásával elindíthat egy tárolót interaktív bash-rendszerhéj használatával.
Windows rendszeren írja be ezt a parancsot egy olyan tároló elindításához, amely egy interaktív parancssori felületet tesz elérhetővé, ahol több parancsot is megadhat `spx` :
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> A számítógép mikrofonját nem használhatja, ha a Speech CLI-t a Docker-tárolón belül futtatja. A helyi csatlakoztatott címtárban azonban elolvashatja és mentheti a hangfájlokat. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Előfizetés konfigurációjának létrehozása

A beszédfelismerési parancssori felület használatának megkezdéséhez meg kell adnia a beszédfelismerési előfizetési kulcsot és a régió azonosítóját. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../overview.md#try-the-speech-service-for-free)szükséges lépések követésével.
Miután megtörtént az előfizetési kulcs és a régió azonosítója (pl. `eastus`, `westus` ) futtassa a következő parancsokat.

```console
spx config --set @key SUBSCRIPTION-KEY
spx config --set @region REGION
```

Az előfizetés-hitelesítés mostantól a jövőbeli SPX-kérelmekhez van tárolva. Ha el kell távolítania a tárolt értékek valamelyikét, futtassa a vagy a parancsot `spx config @region --clear` `spx config @key --clear` .
