---
title: A Azurite Emulator használata a helyi Azure Storage-fejlesztéshez
description: A Azurite nyílt forráskódú emulátora ingyenes helyi környezetet biztosít az Azure Storage-alkalmazások teszteléséhez.
author: twooley
ms.author: twooley
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: a921de0d976b9c92ca7978feb7caf69484ba9379
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277125"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>A Azurite Emulator használata a helyi Azure Storage-fejlesztéshez

A Azurite nyílt forráskódú emulátora ingyenes helyi környezetet biztosít az Azure Blob-és üzenetsor-tárolási alkalmazások teszteléséhez. Ha elégedett az alkalmazás helyi működésével, váltson egy Azure Storage-fiók használatára a felhőben. Az emulátor platformfüggetlen támogatást biztosít Windows, Linux és macOS platformokon.

A Azurite a jövőbeli Storage Emulator platform. A Azurite felülírja az [Azure Storage-emulátort](storage-use-emulator.md). A Azurite továbbra is frissülni fog az Azure Storage API-k legújabb verzióinak támogatásához.

A Azurite számos különböző módon telepíthetők és futtathatók a helyi rendszeren:

  1. [A Azurite Visual Studio Code bővítmény telepítése és futtatása](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [A Azurite telepítése és futtatása a NPM használatával](#install-and-run-azurite-by-using-npm)
  1. [A Azurite Docker-rendszerkép telepítése és futtatása](#install-and-run-the-azurite-docker-image)
  1. [Azurite klónozása, létrehozása és futtatása a GitHub-adattárból](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>A Azurite Visual Studio Code bővítmény telepítése és futtatása

A Visual Studio Code-ban válassza a **bővítmények** ablaktáblát, és keressen rá a *Azurite* kifejezésre a következő **bővítményekben: piactér**.

![Visual Studio Code Extensions piactér](media/storage-use-azurite/azurite-vs-code-extension.png)

A böngészőben a [Visual Studio Code-bővítmény piaca](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) is elérhető. A **telepítés** gombra kattintva nyissa meg a Visual Studio Code-ot, és lépjen közvetlenül a Azurite bővítmény oldalára.

A bővítmény a Visual Studio Code következő parancsait támogatja. A parancs paletta megnyitásához nyomja le az F1 billentyűt a Visual Studio Code-ban. 

   - **Azurite:** a Azurite Services összes perzisztencia-adatbázisának törlése
   - **Azurite: a blob Service tisztítása** – a blob szolgáltatás tisztítása
   - **Azurite: tiszta üzenetsor** -szolgáltatás – a várólista-szolgáltatás tisztítása
   - **Azurite: Bezárás** – az összes Azurite-szolgáltatás lezárása
   - **Azurite: blob szolgáltatás lezárása** – blob szolgáltatás lezárása
   - **Azurite: Bezárás üzenetsor** -szolgáltatás – Bezárás üzenetsor-szolgáltatás
   - **Azurite: Start** – az összes Azurite-szolgáltatás elindítása
   - **Azurite: a blob** Service elindítása – blob szolgáltatás indítása
   - **Azurite: üzenetsor** -szolgáltatás indítása – üzenetsor-szolgáltatás indítása

A Azurite Visual Studio code-on belüli konfigurálásához válassza a kiterjesztések panelt. Válassza a **kezelés** (fogaskerék) ikont a **Azurite**. Válassza ki a **bővítmény beállításait**.

![Azurites konfigurálása](media/storage-use-azurite/azurite-configure-extension-settings.png)

A következő beállítások támogatottak:

   - **Azurite: blob Host** – a blob Service figyelő végpontja. Az alapértelmezett beállítás a 127.0.0.1.
   - **Azurite: blob port** – a blob Service figyelési port. Az alapértelmezett port a 10000.
   - **Azurite:** tanúsítvány – egy HELYILEG megbízható PEM-vagy pfx-tanúsítványfájl elérési útja a https-mód engedélyezéséhez.
   - **Azurite: hibakeresés** – a hibakeresési napló kimenete a Azurite csatornára. Az alapértelmezett érték: **hamis**.
   - **Azurite: kulcs** – a HELYILEG megbízható PEM-kulcsfájl elérési útja, amely akkor szükséges, ha **Azurite: a tanúsítvány** egy PEM-fájlra mutat.
   - **Azurite: Location** – a munkaterület helyének elérési útja. Az alapértelmezett érték a Visual Studio Code Working mappa.
   - **Azurite:** laza engedélyezése – a nem támogatott fejléceket és paramétereket figyelmen kívül hagyva.
   - **Azurite: OAuth** – opcionális OAuth szint.
   - **Azurite: pwd** – a pfx-fájl jelszava. Kötelező, ha **Azurite: a tanúsítvány** pfx-fájlra mutat.
   - **Azurite: várólista-állomás** – a Queue szolgáltatás figyelő végpontja. Az alapértelmezett beállítás a 127.0.0.1.
   - **Azurite: várólista portja** – a Queue szolgáltatás figyelési port. Az alapértelmezett port a 10001.
   - **Azurite: csendes** -csendes üzemmód letiltja a hozzáférési naplót. Az alapértelmezett érték: **hamis**.
   - **Azurite: az API-verzió ellenőrzésének kihagyása** – a kérelem API-verziójának ellenőrzésének kihagyása. Az alapértelmezett érték: **hamis**.

## <a name="install-and-run-azurite-by-using-npm"></a>A Azurite telepítése és futtatása a NPM használatával

Ehhez a telepítési módszerhez a [Node.js 8,0-es vagy újabb verziójára](https://nodejs.org) van szükség. A Node Package Manager (NPM) a csomagkezelő eszköz, amely minden Node.js telepítéshez tartozik. Node.js telepítése után hajtsa végre a következő `npm` parancsot a Azurite telepítéséhez.

```console
npm install -g azurite
```

A Azurite telepítése után tekintse meg [a Azurite futtatása parancssorból](#run-azurite-from-a-command-line)című témakört.

## <a name="install-and-run-the-azurite-docker-image"></a>A Azurite Docker-rendszerkép telepítése és futtatása

A [DockerHub](https://hub.docker.com/) használatával a [legújabb Azurite-lemezképet](https://hub.docker.com/_/microsoft-azure-storage-azurite) a következő paranccsal kérheti le:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Futtassa a Azurite Docker-rendszerképet**:

A következő parancs futtatja a Azurite Docker-rendszerképet. A `-p 10000:10000` paraméter átirányítja a gazdagépről a 10000-as portra érkező kéréseket a Docker-példányra.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Munkaterület helyének meghatározása**:

A következő példában a paraméter a `-v c:/azurite:/data` *c:/Azurite* adja meg a Azurite megőrzött adathelyként. A Docker-parancs futtatása előtt létre kell hozni a könyvtárat ( *c:/Azurite*).

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Csak a blob szolgáltatás futtatása**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

További információ a Azurite konfigurálásáról az indításkor: [parancssori kapcsolók](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Azurite klónozása, létrehozása és futtatása a GitHub-adattárból

Ez a telepítési módszer megköveteli, hogy a [git](https://git-scm.com/) telepítve legyen. A Azurite-projekt [GitHub-tárházának](https://github.com/azure/azurite) klónozásához használja az alábbi parancsot.

```console
git clone https://github.com/Azure/Azurite.git
```

A forráskód klónozása után futtassa a következő parancsokat a klónozott tárház gyökeréből a Azurite létrehozásához és telepítéséhez.

```console
npm install
npm run build
npm install -g
```

A Azurite telepítése és létrehozása után tekintse meg [a Azurite futtatása parancssorból](#run-azurite-from-a-command-line)című témakört.

## <a name="run-azurite-from-a-command-line"></a>Azurite futtatása parancssorból

> [!NOTE]
> A Azurite nem futtatható a parancssorból, ha csak a Visual Studio Code bővítményt telepítette. Ehelyett használja a Visual Studio Code parancs-palettát. További információkért lásd: [a Azurite Visual Studio Code bővítmény telepítése és futtatása](#install-and-run-the-azurite-visual-studio-code-extension).

A parancssorból történő azonnali kezdéshez hozzon létre egy *c:\azurite* nevű könyvtárat, majd indítsa el a Azurite a következő parancs kiadásával:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Ez a parancs arra utasítja a Azurite, hogy egy adott címtárban ( *c:\azurite*) tárolja az összes adatot. Ha a `--location` beállítás nincs megadva, a rendszer az aktuális munkakönyvtárat fogja használni.

## <a name="command-line-options"></a>Parancssori kapcsolók

Ez a szakasz részletesen ismerteti a Azurite indításakor elérhető parancssori kapcsolókat.

### <a name="help"></a>Help

**Opcionális** – parancssori Segítség kérése a `-h` vagy `--help` kapcsoló használatával.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>BLOB figyelő gazdagép

**Opcionális** – a Azurite alapértelmezés szerint helyi kiszolgálóként fogja figyelni a 127.0.0.1-t. A `--blobHost` kapcsoló használatával állítsa be a kívánt címeket a követelményekre.

Kérelmek elfogadása csak a helyi gépen:

```console
azurite --blobHost 127.0.0.1
```

Távoli kérelmek engedélyezése:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> A távoli kérelmek lehetővé tehetik, hogy a rendszer sebezhető legyen a külső támadásokkal szemben.

### <a name="blob-listening-port-configuration"></a>BLOB figyelési portjának konfigurációja

**Opcionális** – alapértelmezés szerint a Azurite a 10000-as porton fogja figyelni a blob Service. A `--blobPort` kapcsoló használatával határozza meg a szükséges figyelő portot.

> [!NOTE]
> Ha testreszabott portot használ, frissítenie kell a kapcsolódási karakterláncot vagy az Azure Storage-eszközök vagy SDK-k megfelelő konfigurációját.

Blob service figyelési port testreszabása:

```console
azurite --blobPort 8888
```

Az elérhető port automatikus kiválasztásának engedélyezése a rendszer számára:

```console
azurite --blobPort 0
```

A használatban lévő port a Azurite indításakor jelenik meg.

### <a name="queue-listening-host"></a>Üzenetsor-figyelő gazdagép

**Opcionális** – a Azurite alapértelmezés szerint helyi kiszolgálóként fogja figyelni a 127.0.0.1-t. A `--queueHost` kapcsoló használatával állítsa be a kívánt címeket a követelményekre.

Kérelmek elfogadása csak a helyi gépen:

```console
azurite --queueHost 127.0.0.1
```

Távoli kérelmek engedélyezése:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> A távoli kérelmek lehetővé tehetik, hogy a rendszer sebezhető legyen a külső támadásokkal szemben.

### <a name="queue-listening-port-configuration"></a>Várólista-figyelő portjának konfigurációja

**Opcionális** – alapértelmezés szerint a Azurite a 10001-as porton fogja figyelni a Queue szolgáltatás. A `--queuePort` kapcsoló használatával határozza meg a szükséges figyelő portot.

> [!NOTE]
> Ha testreszabott portot használ, frissítenie kell a kapcsolódási karakterláncot vagy az Azure Storage-eszközök vagy SDK-k megfelelő konfigurációját.

Queue szolgáltatás figyelési port testreszabása:

```console
azurite --queuePort 8888
```

Az elérhető port automatikus kiválasztásának engedélyezése a rendszer számára:

```console
azurite --queuePort 0
```

A használatban lévő port a Azurite indításakor jelenik meg.

### <a name="workspace-path"></a>Munkaterület elérési útja

**Opcionális** – a Azurite a helyi lemezre menti az adattárolást a végrehajtás során. A `-l` vagy a `--location` kapcsoló használatával megadnia a munkaterület helyének elérési útját. Alapértelmezés szerint a rendszer az aktuális folyamat munkakönyvtárát fogja használni. Jegyezze fel az "l" kisbetűt.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Hozzáférési napló

**Opcionális** – alapértelmezés szerint a hozzáférési napló a konzol ablakban jelenik meg. Tiltsa le a hozzáférési napló megjelenítését a vagy a `-s` `--silent` kapcsoló használatával.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Hibakeresési napló

Nem **kötelező** – a hibakeresési napló részletes információkat tartalmaz minden kérelemről és a kivételek veremének nyomkövetéséről. A hibakeresési napló engedélyezéséhez adjon meg egy érvényes helyi elérési utat a `-d` vagy `--debug` kapcsoló számára.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Kilazult mód

**Opcionális** – alapértelmezés szerint a Azurite szigorú módot alkalmaz a nem támogatott kérelmek fejlécének és paramétereinek a blokkolására. Tiltsa le a szigorú módot a `-L` vagy a `--loose` kapcsoló használatával. Jegyezze fel az "L" tőkét.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Verzió

Nem **kötelező** – a telepített Azurite verziószámát a vagy kapcsoló használatával jelenítheti meg `-v` `--version` .

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Tanúsítvány konfigurációja (HTTPS)

**Opcionális** – alapértelmezés szerint a AZURITE a HTTP protokollt használja. Engedélyezze a HTTPS-módot egy Privacy Enhanced Mail (. PEM) vagy [személyes információcsere (. pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) tanúsítványfájl elérési útjának megadásával a `--cert` kapcsolóhoz.

Ha `--cert` egy PEM-fájlhoz van megadva, meg kell adnia egy megfelelő `--key` kapcsolót.

```console
azurite --cert path/server.pem --key path/key.pem
```

Ha `--cert` egy pfx-fájlhoz van megadva, meg kell adnia egy megfelelő `--pwd` kapcsolót.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

A PEM-és PFX-fájlok létrehozásával kapcsolatos részletes információkért lásd: [https-beállítás](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>OAuth-konfiguráció

**Opcionális** – engedélyezze a OAuth-hitelesítést a Azurite a `--oauth` kapcsoló használatával.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> A OAuth HTTPS-végpontot igényel. Győződjön meg arról, hogy engedélyezve van-e a HTTPS a kapcsolóval `--cert` együtt `--oauth` .

A Azurite az alapszintű hitelesítést támogatja a `basic` kapcsoló paraméterének megadásával `--oauth` . A Azurite alapszintű hitelesítést végez, például érvényesíti a bejövő tulajdonosi jogkivonatot, ellenőrzi a kiállítót, a célközönséget és a lejáratot. A Azurite nem fogja ellenőriznie a jogkivonat aláírását vagy engedélyeit.

### <a name="skip-api-version-check"></a>API-verzió ellenőrzésének kihagyása

**Opcionális** – a indításakor a Azurite ellenőrzi, hogy érvényes-e a kért API-verzió. A következő parancs kihagyja az API-verzió ellenőrzését:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Eszközök és SDK-k engedélyezése

Az Azure Storage SDK-k vagy eszközök, például a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)bármely hitelesítési stratégia segítségével csatlakozhat a Azurite. Hitelesítés szükséges. A Azurite támogatja az OAuth, a megosztott kulcs és a közös hozzáférésű aláírások (SAS) engedélyezését. A Azurite támogatja a nyilvános tárolók névtelen elérését is.

Ha az Azure SDK-kat használja, indítsa el a Azurite a `--oauth basic and --cert --key/--pwd` lehetőségekkel.

### <a name="well-known-storage-account-and-key"></a>Jól ismert Storage-fiók és-kulcs

A Azurite ugyanazt a jól ismert fiókot és kulcsot fogadja el, amelyet a régi Azure Storage-emulátor használ.

- Fiók neve: `devstoreaccount1`
- Fiók kulcsa: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Egyéni Storage-fiókok és-kulcsok

A Azurite a környezeti változó a következő formátumban való beállításával támogatja az egyéni Storage-fiókok nevét és kulcsait `AZURITE_ACCOUNTS` : `account1:key1[:key2];account2:key1[:key2];...` .

Használjon például egy olyan egyéni Storage-fiókot, amely rendelkezik egy kulccsal:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Vagy használjon több Storage-fiókot két kulccsal:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

A Azurite alapértelmezés szerint percenként frissíti az egyéni fiókok nevét és kulcsait a környezeti változóból. Ezzel a funkcióval dinamikusan elforgathatja a fiók kulcsát, vagy új Storage-fiókokat adhat hozzá a Azurite újraindítása nélkül.

> [!NOTE]
> Az alapértelmezett `devstoreaccount1` Storage-fiók le van tiltva egyéni Storage-fiókok beállításakor.

### <a name="connection-strings"></a>Kapcsolati sztringek

Az alkalmazás Azurite való csatlakoztatásának legegyszerűbb módja egy kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájljában, amely a *UseDevelopmentStorage = True* billentyűparancsra hivatkozik. Íme egy példa a *app.config* fájlban található kapcsolatok karakterláncra:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP-kapcsolatok karakterláncai

A következő kapcsolódási karakterláncokat átadhatja az [Azure SDK](https://aka.ms/azsdk) -k vagy eszközök, például az azure CLI 2,0 vagy a Storage Explorer számára.

A teljes kapcsolatok karakterlánca:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Ha csak a blob szolgáltatáshoz szeretne csatlakozni, a kapcsolati karakterlánc a következő:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Ha csak a várólista-szolgáltatáshoz szeretne csatlakozni, a kapcsolati karakterlánc a következő:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS-kapcsolati karakterláncok

A teljes HTTPS-kapcsolati karakterlánc:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Ha csak a blob-szolgáltatást szeretné használni, a HTTPS-kapcsolati karakterlánc a következő:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Ha csak a várólista-szolgáltatást szeretné használni, a HTTPS-kapcsolati karakterlánc a következő:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Ha az `dotnet dev-certs` önaláírt tanúsítvány létrehozásához használt, használja a következő kapcsolódási karakterláncot.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

A kapcsolódási karakterlánc frissítése [Egyéni Storage-fiókok és-kulcsok](#custom-storage-accounts-and-keys)használata esetén.

További információ: az [Azure Storage-beli kapcsolatok karakterláncának konfigurálása](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>Azure SDK-k

Ha az [Azure SDK](https://aka.ms/azsdk)-k használatával szeretné használni az Azurite-t, használja a OAuth és a https-beállításokat:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Ezután BlobContainerClient, BlobServiceClient vagy BlobClient hozhat létre.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

QueueClient vagy QueueServiceClient is létrehozhat.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Storage Explorer használatával megtekintheti a Azurite tárolt adatfájlokat.

#### <a name="connect-to-azurite-using-http"></a>Kapcsolódás Azurite HTTP használatával

Storage Explorer a következő lépésekkel csatlakozhat a Azurite-hez:

 1. Válassza a **fiókok kezelése** ikont
 1. Válassza **a fiók hozzáadása** lehetőséget.
 1. **Helyi emulátorhoz tartozó csatolás** kiválasztása
 1. Kattintson a **Tovább** gombra.
 1. A **megjelenítendő név** mező szerkesztése tetszőleges névre
 1. Kattintson a **Tovább gombra**
 1. Válassza a **kapcsolat** lehetőséget.

#### <a name="connect-to-azurite-using-https"></a>Kapcsolódás Azurite HTTPS használatával

Alapértelmezés szerint Storage Explorer nem nyit meg olyan HTTPS-végpontot, amely önaláírt tanúsítványt használ. Ha a Azurite-t HTTPS-kapcsolattal futtatja, valószínűleg önaláírt tanúsítványt használ. Storage Explorer az SSL-tanúsítványok **importálása az**  ->  **SSL-tanúsítványok**  ->  **importálása tanúsítványok** használatával párbeszédpanelt.

##### <a name="import-certificate-to-storage-explorer"></a>Tanúsítvány importálása a Storage Explorerba

1. Keresse meg a tanúsítványt a helyi gépen.
1. Az Storage Explorer-ban keresse **meg az**  ->  **SSL-tanúsítványok**  ->  **importálása** és a tanúsítvány importálása című részt.

Ha nem importál tanúsítványt, a következő hibaüzenet jelenik meg:

`unable to verify the first certificate` vagy `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Azurite hozzáadása HTTPS kapcsolati sztring használatával

A következő lépésekkel adhat hozzá HTTPS-Azurite a Storage Explorerhoz:

1. A **váltógomb** kiválasztása
1. **Helyi & csatolásának** kiválasztása
1. Kattintson a jobb gombbal a **Storage-fiókok** elemre, és válassza a **Kapcsolódás az Azure Storage-hoz** lehetőséget.
1. Válassza **a kapcsolatok sztring használata** lehetőséget
1. Kattintson a **Tovább** gombra.
1. Adjon meg egy értéket a **megjelenítendő név** mezőben.
1. Adja meg a jelen dokumentum előző szakaszának [https-kapcsolati karakterláncát](#https-connection-strings) .
1. Kattintson a **Tovább** gombra.
1. Válassza a **kapcsolat** lehetőséget.

## <a name="workspace-structure"></a>Munkaterület szerkezete

A következő fájlok és mappák hozhatók létre a munkaterület helyén a Azurite inicializálásakor.

- `__blobstorage__` -A Azurite blob Service-t tartalmazó könyvtár bináris adatvédelmet tartalmaz
- `__queuestorage__` -A Azurite üzenetsor-kezelő szolgáltatás megőrzött bináris adattartalma
- `__azurite_db_blob__.json` -Azurite blob szolgáltatás metaadatainak fájlja
- `__azurite_db_blob_extent__.json` -Azurite blob szolgáltatási mérték metaadat-fájlja
- `__azurite_db_queue__.json` -Azurite üzenetsor-szolgáltatás metaadatainak fájlja
- `__azurite_db_queue_extent__.json` -Azurite üzenetsor-szolgáltatási mérték metaadat-fájlja

A Azurite törléséhez törölje a fenti fájlokat és mappákat, majd indítsa újra az emulátort.

## <a name="differences-between-azurite-and-azure-storage"></a>A Azurite és az Azure Storage közötti különbségek

A Azurite helyi példánya és a felhőben található Azure Storage-fiók között funkcionális különbségek vannak.

### <a name="endpoint-and-connection-url"></a>Végpont és a kapcsolatok URL-címe

A Azurite szolgáltatási végpontjai eltérnek az Azure Storage-fiókok végpontjaitól. A helyi számítógép nem hajtja végre a tartománynevek feloldását, ezért a Azurite-végpontoknak helyi címnek kell lenniük.

Ha egy Azure Storage-fiókban lévő erőforrást címez, a fiók neve az URI-állomásnév része. A megoldandó erőforrás az URI elérési útjának része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

A következő URI egy Azure Storage-fiókban található blob érvényes címe:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Mivel a helyi számítógép nem hajtja végre a tartománynevek feloldását, a fiók neve az URI elérési útja része az állomásnév helyett. A következő URI-formátumot használja egy erőforráshoz a Azurite-ben:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

A következő címnek lehet a blobhoz való hozzáférése a Azurite-ben:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skálázás és teljesítmény

A Azurite nem támogat nagy számú csatlakoztatott ügyfelet. Nincs teljesítménybeli garancia. A Azurite fejlesztési és tesztelési célokra szolgál.

### <a name="error-handling"></a>Hibakezelés

A Azurite az Azure Storage-hibák kezelésére szolgáló logikával van összhangban, de különbségek vannak. Előfordulhat például, hogy a hibaüzenetek eltérőek, míg a hibakódok igazítása.

### <a name="ra-grs"></a>RA-GRS

A Azurite támogatja az olvasási hozzáférésű geo-redundáns replikálást (RA-GRS). A tárolási erőforrások esetében a fiók nevének hozzáfűzésével nyissa meg a másodlagos helyet `-secondary` . Például a következő címnek lehet a blobokhoz való hozzáférése a Azurite írásvédett másodlagos használatával:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Tábla támogatása

A Azurite lévő táblák támogatása jelenleg fejlesztés alatt áll, és a részvételre nyitott. A legújabb folyamathoz keresse meg a [Azurite v3 tábla](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) projektjét.

A tartós függvények támogatásához táblák szükségesek.

## <a name="azurite-is-open-source"></a>A Azurite nyílt forráskódú

Szívesen fogadjuk a Azurite vonatkozó hozzájárulásokat és javaslatokat. Lépjen a Azurite [GitHub-projekt](https://github.com/Azure/Azurite/projects) oldalra, vagy [GitHub-problémák](https://github.com/Azure/Azurite/issues) a mérföldkövek és munkaelemek számára, amelyeket nyomon követünk a közelgő funkciókkal és hibajavításokkal kapcsolatban. A részletes munkaelemeket is nyomon követheti a GitHubon.

## <a name="next-steps"></a>Következő lépések

- [Használja az Azure Storage emulatort fejlesztési és tesztelési](storage-use-emulator.md) dokumentumaihoz az örökölt Azure Storage-emulátort, amelyet a Azurite felülír.
- Az [Azure Storage-kapcsolódási karakterláncok konfigurálása](storage-configure-connection-string.md) azt ismerteti, hogyan lehet egy érvényes Azure Storage-beli kapcsolódási karakterláncot összeállítani.
