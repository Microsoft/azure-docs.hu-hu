---
title: Azure Storage Explorer – hibaelhárítási útmutató |} A Microsoft Docs
description: Hibakeresési technikák az Azure Storage Explorer – áttekintés
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: fd34ab7cd899549962663e8cee8ee2121c39c49e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840389"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Az Azure Storage Explorer hibaelhárítási útmutatója

A Microsoft Azure Storage Explorer egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken. Az alkalmazás üzemeltetett Azure, az országos felhők és az Azure Stack tárfiókok csatlakozhat.

Ez az útmutató összefoglalja a Storage Explorer leggyakoribb problémáinak megoldásait.

## <a name="role-based-access-control-permission-issues"></a>Szerepköralapú hozzáférés-vezérlési engedélyekkel kapcsolatos problémák

[Szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) Azure-erőforrások részletes hozzáférés-vezérlést biztosít be jogosultságkészletek kombinálásával _szerepkörök_. Az alábbiakban néhány javaslatot beolvasni az RBAC használata a Storage Explorerben követheti.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Hogyan készüljek megtekintheti az erőforrások a Storage Explorerben?

RBAC használatával tárolási erőforrások elérésével kapcsolatos problémák merültek fel, ha lehet, mert még nem a megfelelő szerepkört rendeltek. A következő szakaszok ismertetik a Storage Explorer jelenleg a tárolási erőforrások eléréséhez szükséges engedélyekkel.

Lépjen kapcsolatba az Azure-fiókja rendszergazda, ha biztos benne, hogy rendelkezik a megfelelő szerepkörök vagy engedélyek.

#### <a name="read-listget-storage-accounts"></a>Olvasás: Tárfiók(ok) lista/lekérése

Storage-fiókok listája engedéllyel kell rendelkeznie. Ez az engedély kaphat, ha a "Olvasó" szerepkörrel.

#### <a name="list-storage-account-keys"></a>Tárfiókkulcsok listája

Storage Explorer használatával is fiókkulcsok-kérések hitelesítéséhez. Hozzáférhet a kulcsokhoz, nagyobb teljesítményű szerepkörökhöz, például a "Közreműködő" szerepkör.

> [!NOTE]
> Hozzáférési kulcsok korlátlan engedélyeket bárki tárolja őket. Ezért általában nem ajánlott azokat a fiókot használó felhasználók kell átadni. Ha vissza kell vonnia a tárelérési kulcsok, újból létrehozhatja őket a [az Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Adatok szerepkörök

Legalább egy szerepkört, amely engedélyezi a hozzáférést adatokat olvasni az erőforrásokat kell hozzárendelni. Például ha kell listában vagy tölthet le blobokat, szüksége lesz legalább a "Storage-Blobadatok olvasója" szerepkör.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Miért kell a felügyeleti réteg szerepkör megtekintéséhez az erőforrások a Storage Explorerben?

Az Azure Storage van két hozzáférési réteget: _felügyeleti_ és _adatok_. Előfizetések és tárfiókok a felügyeleti réteg keresztül érhetők el. Tárolók, blobok és egyéb erőforrásokat az adatok réteg keresztül érhetők el. Például, ha a tárfiókok listájának lekérése az Azure-ból, egy kérelmet küld a felügyeleti végponthoz. Ha szeretne egy fiókot a blob-tárolók listája, a megfelelő szolgáltatási végpont egy kérelmet küld.

RBAC-szerepkörök felügyeleti vagy adatok réteg hozzáférési engedélyeket is tartalmazhat. Az "Olvasó", például szerepkörök, csak olvasási hozzáféréssel felügyeleti réteg erőforrásokat.

Szigorú értelemben véve az "Olvasó" szerepkör nem adatok réteg engedélyeket biztosít, és nem szükséges az adatok réteg eléréséhez.

Storage Explorer egyszerűen csatlakozhat az Azure-erőforrások, az Ön számára szükséges információkat gyűjt az erőforrások eléréséhez. Például a blobtárolók megjelenítéséhez Storage Explorer egy lista tárolók kérést küld a blob service-végpont. Hogy a végpont beszerzéséhez a Storage Explorer keres az előfizetések listáját, és storage-fiókok fér hozzá. Azonban az előfizetések és tárfiókok megkereséséhez Storage Explorer is hozzá kell férnie a felügyeleti réteg.

Ha nem rendelkezik a szerepkör minden olyan felügyeleti réteg engedélyek megadását, a Storage Explorer nem olvasható be a adatrétege való kapcsolódáshoz szükséges adatokat.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Mi történik, ha nem jelenik meg a felügyeleti réteg engedélyeket kell a saját rendszergazdai?

Még nem egy RBAC kapcsolatos megoldást most. Áthidaló megoldásként kérheti egy SAS URI-t [az erőforrás csatolása](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hiba: Önaláírt tanúsítványt a tanúsítványlánc (és ehhez hasonló hibaüzenetek)

Tanúsítvánnyal kapcsolatos hiba okozza a két alábbi helyzetek bármelyike:

1. Az alkalmazás egy "transzparens proxy", ami azt jelenti, hogy a kiszolgáló (például a vállalati kiszolgálónak) elfogja a HTTPS-forgalmat, visszafejti azt, és majd újra titkosítja egy önaláírt tanúsítvány használatával keresztül csatlakozik.
2. Olyan alkalmazás, amely egy önaláírt SSL-tanúsítvány van injektálásra a fogadott HTTPS üzenetek futtat. Tanúsítványok beszúrása alkalmazások magában foglalja a víruskereső és a hálózati forgalom ellenőrzési szoftvereket.

Storage Explorer egy önaláírt vagy nem megbízható tanúsítvánnyal látja, amikor azt már tudja, a fogadott HTTPS-üzenetet módosították-e. Ha az önaláírt tanúsítvány egy példányát, utasíthatja Storage Explorer megbízzon benne a következő lépések végrehajtásával:

1. Szerezzen be egy Base-64 kódolású X.509 (.cer) a tanúsítvány másolatát
2. Kattintson a **szerkesztése** > **SSL-tanúsítványok** > **tanúsítványok importálása**, majd a Fájlkereső segítségével keresse meg, válassza ki, nyissa meg a .cer fájlt

A probléma is előfordulhat, hogy több tanúsítványt (gyökér és köztes) eredménye. A hiba, hogy mindkét tanúsítvány hozzá kell adni.

Ha nincs tisztában a tanúsítvány forrását, megpróbálhatja ezeket a lépéseket, és keresse meg:

1. Telepítse az Open SSL-t.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (bármelyik egyszerűsített verzió elegendőnek kell lennie)
    * Mac és Linux: elvileg eleve mellékelve van az operációs rendszer
2. Futtassa az Open SSL-t.
    * Windows: Nyissa meg a telepítési könyvtárát, kattintson a **/bin/** , majd kattintson duplán **openssl.exe**.
    * Mac és Linux: futtassa **openssl** parancsot egy terminálról.
3. Hajtsa végre az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha biztos abban, hogy mely tanúsítványok önaláírt, keressen bárhol a tulajdonos `("s:")` és kiállító `("i:")` azonos.
5. Ha talált önaláírt tanúsítványokat, másolja ki a is beleértve **---BEGIN CERTIFICATE---** való **---END CERTIFICATE---** egy új .cer fájlba.
6. Nyissa meg a Storage Explorerben, kattintson a **szerkesztése** > **SSL-tanúsítványok** > **tanúsítványok importálása**, majd a Fájlkereső segítségével keresse meg, válassza ki, és Nyissa meg a létrehozott .cer fájlokat.

Ha nem talál önaláírt tanúsítványokat használ a fenti lépéseket, a visszajelzési eszközzel, további segítségért lépjen kapcsolatba velünk. Azt is beállíthatja a parancssorból, a Storage Explorer elindításához a `--ignore-certificate-errors` jelzőt. Ez a jelző indításakor Storage Explorer tanúsítvánnyal kapcsolatos hiba figyelmen kívül.

## <a name="sign-in-issues"></a>Bejelentkezési problémák

### <a name="blank-sign-in-dialog"></a>Üres bejelentkezési párbeszédpanel

Üres bejelentkezési párbeszédpanelek leggyakrabban okozzák ADFS kéri a Storage Explorer egy átirányítási, amely nem támogatja a Electron végrehajtásához. A probléma megkerüléséhez megpróbálhatja eszköz kód Flow használni a bejelentkezéshez. Ehhez kövesse az alábbi lépéseket:

1. Menü: Előnézet -> "Eszköz kód bejelentkezés használata".
2. Nyissa meg a Csatlakozás párbeszédpanel (akár a Plug and ikonra a bal oldali függőleges vonal vagy a "Fiók hozzáadása" fiók panelen keresztül).
3. Válassza ki, milyen jelentkezzen be a kívánt környezet.
4. Kattintson a "Bejelentkezés" gombra.
5. Kövesse a következő panelen megjelenő utasításokat.

Ha azt segítségre van szüksége a fiók használható, mert az alapértelmezett böngészőben már bejelentkezett egy másik fiókkal szeretne bejelentkezni, választhatja:

1. Manuálisan másolja a hivatkozást és a kód egy privát a böngésző-munkamenetet.
2. Manuálisan másolja a hivatkozást és a kód egy másik böngészőben.

### <a name="reauthentication-loop-or-upn-change"></a>Hurok újrahitelesítést vagy egyszerű felhasználónév módosítása

Ha a hurok újrahitelesítést, vagy módosította az egyik a fiók egyszerű Felhasználóneve, próbálja meg a következőket:

1. Távolítsa el az összes fiókot, és zárja be a Storage Explorerben
2. Törölje a. IdentityService mappájában található a gépen. A Windows, a mappa a következő helyen található `C:\users\<username>\AppData\Local`. A mappa gyökerénél legyen a felhasználói címtár annak Macre és Linuxra készült.
3. Ha Ön Mac vagy Linux rendszeren, is szüksége lesz a Microsoft.Developer.IdentityService tétel törlését az operációs rendszer keystore. A kulcstár Mac, a "Kulcslánc Gnome" alkalmazás, amely. A Linux rendszerre az alkalmazás általában neve "Kulcstár", de lehet, hogy a név attól függően változik, a terjesztési.

### <a name="conditional-access"></a>Feltételes hozzáférés

Feltételes hozzáférés nem támogatott, ha a Storage Explorer a Windows 10, Linux vagy macOS rendszeren használja. Ez a Storage Explorer által használt AAD-erőforrástárban korlátozásai miatt.

## <a name="mac-keychain-errors"></a>Mac kulcslánc-hibák

A macOS kulcslánc néha kérheti le a Storage Explorer hitelesítési tár problémákat okozó állapotba. Ebben az állapotban nem a kulcslánc lekéréséhez kövesse az alábbi lépéseket:

1. Zárja be a Storage Explorerben.
2. Nyissa meg a kulcslánc (**cmd + szóköz**, írja be a kulcslánc, találati adja meg).
3. Válassza ki a "bejelentkezés" kulcslánc.
4. Kattintson a lakat ikonra, zárolja a kulcsláncban (a lakat a zárolt helyzetben, amikor végzett, eltarthat néhány másodpercig függően rendelkezik alkalmazások nyissa meg a rendszer animálása).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Indítsa el a Storage Explorerben.
6. Egy előugró ablak akkor is meg kell jelennie üzenettel, ha valami, például a "Service hub szeretne hozzáférni a keychain". Amikor azt, Mac-rendszergazdai fiók jelszavát adja meg, és kattintson a **mindig** (vagy **engedélyezése** Ha **mindig** nem érhető el).
7. Próbáljon meg bejelentkezni.

### <a name="general-sign-in-troubleshooting-steps"></a>Általános bejelentkezési hibaelhárítási lépéseket

* Ha MacOS-gépeken, és soha nem megjelenik a bejelentkezési ablak fölé a "Várakozás a hitelesítés..." párbeszédpanelen, majd próbálja meg [ezeket a lépéseket](#mac-keychain-errors)
* Indítsa újra a Tártallózót
* Ha a hitelesítési ablak üres, várjon legalább egy percet, mielőtt bezárná a hitelesítési párbeszédpanelt.
* Győződjön meg arról, hogy a proxy- és a tanúsítvány beállításait konfigurálja megfelelően a gép és a Storage Explorer.
* Ha Windows használ, és rendelkezik hozzáféréssel a ugyanarra a gépre, és jelentkezzen be a Visual Studio 2019, próbálja meg bejelentkezni a Visual Studio 2019. A sikeres bejelentkezést követően a Visual Studio 2019 nyissa meg a Storage Explorert, és látja: saját fiók a fiók panelen.

Ha ezen metódusok közül egyik sem működik [nyisson egy problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Hiányzó előfizetések és a bérlők megszakadt

Ha nem tudja lekérni az előfizetéseit, miután sikeresen bejelentkezett, próbálja meg a következő hibaelhárítási módszerek:

* Győződjön meg arról, hogy a fiók rendelkezik-e hozzáférése az előfizetésekhez várt. A hozzáférés a használni kívánt Azure-környezetre vonatkozó portálra való bejelentkezés révén ellenőrizheti.
* Győződjön meg arról, hogy bejelentkezett az a megfelelő Azure-környezet (Azure, Azure China 21Vianet, Azure Germany, Azure US Government vagy egyéni környezet).
* Ha proxyt használ, ügyeljen arra, hogy konfigurálta a Storage Explorer-proxy megfelelően.
* Próbálja meg eltávolítani és újra hozzáadja a fiókot.
* Ha a "További információ" hivatkozást, keresse meg, és tekintse meg, milyen hibaüzenetek a bérlők számára, amelyek nem jelentették. Ha you'ren meg meg arról, hogy mit tegyen a hiba az üzeneteket, tekintse meg, akkor nyugodtan [nyisson egy problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Csatolt fiók vagy a storage erőforrás nem távolítható el.

Ha Ön nem távolítható el egy csatolt fiók vagy a tárolási erőforrás a felhasználói felületen, manuálisan törölheti az összes kapcsolódó erőforrás a következő mappák törlésével:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> A fenti mappák törlése előtt zárja be a Storage Explorerben.

> [!NOTE]
> Ha minden eddiginél importálta-e SSL-tanúsítványok, akkor biztonsági mentése a tartalmát a `certs` könyvtár. Később a biztonsági mentés használatával importálja újra az SSL-tanúsítványokat.

## <a name="proxy-issues"></a>Proxy kapcsolatos problémák

Először is győződjön meg arról, hogy minden helyesen-e a megadott a következő információkat:

* A proxykiszolgáló URL-cím és port száma
* Felhasználónév és jelszó, ha a proxy által igényelt

> [!NOTE]
> Storage Explorer Proxybeállítások konfigurálása proxy automatikus konfiguráció fájlok nem támogatja.

### <a name="common-solutions"></a>Általános megoldások

Ha problémák továbbra is fennáll, próbálja meg a következő hibaelhárítási módszerek:

* Ha a proxy használata nélkül is csatlakozni az internethez, ellenőrizze a Storage Explorer nélkül engedélyezve proxybeállítások működését. Ez a helyzet, ha a proxybeállításai probléma lehet. A problémák azonosítását a proxy rendszergazdával együttműködve.
* Győződjön meg arról, hogy a proxykiszolgáló használatát más alkalmazások a várt módon működik-e.
* Győződjön meg arról, hogy képes-e csatlakozni a portálon használni kívánt Azure-környezetre vonatkozó
* Győződjön meg arról, hogy kaphat szolgáltatásvégpontokra érkező válaszok. Adja meg a végpont URL-címek egyikét a böngészőbe. Csatlakoztathatja, ha egy InvalidQueryParameterValue vagy hasonló XML-választ kell kapnia.
* Ha valaki más is használja a Tártallózó fel a proxykiszolgálón, győződjön meg arról, hogy csatlakozhassanak. Csatlakozás, ha előfordulhat, forduljon a proxy server rendszergazdához.

### <a name="tools-for-diagnosing-issues"></a>Eszközök a problémák diagnosztizálása

Ha hálózati eszközök, például a Fiddlert a Windows, a következő diagnosztizálhatja a problémákat:

* Ha a proxyn keresztül működik, előfordulhat, a hálózati eszközzel csatlakozzon a proxyn keresztül történő konfigurálásához.
* Ellenőrizze a hálózati eszköz által használt port számát.
* Adja meg a helyi állomás URL-CÍMÉT és a hálózati eszköz portszám a Storage Explorerben proxykiszolgáló-beállításként. A megfelelően végzett a hálózati eszköz elindul, hálózati kérést hoz létre a felügyeleti és Szolgáltatásvégpontok Storage Explorer-naplózás. Adja meg például https://cawablobgrs.blob.core.windows.net/ fog kapni a a blob-végpont a böngészőben, és a válasz a következőhöz, ami alapján a az erőforrás létezik, de nem tudja elérni.

![Kódminta](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Lépjen kapcsolatba a proxy server-rendszergazda

Ha a proxybeállításai megfelelőek, lehet a proxy server rendszergazdához, és

* Győződjön meg arról, hogy a proxy nem tiltja az Azure felügyeleti vagy az erőforrás-végpontokra irányuló forgalmat.
* Ellenőrizze a proxy-kiszolgáló által használt hitelesítési protokoll. Storage Explorer jelenleg nem támogatja az NTLM-proxyk.

## <a name="unable-to-retrieve-children-error-message"></a>"A gyermekek lekérése nem sikerült" hibaüzenet jelenik meg

Ha proxyn keresztül csatlakozott az Azure-ba, győződjön meg arról, hogy helyesek-e a WebProxy beállításait. Ha a fiók vagy előfizetés tulajdonosa van kapott hozzáférést egy erőforráshoz, győződjön meg arról, hogy rendelkezik-e olvasási vagy listában meghatározott engedélyek.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Kapcsolati karakterlánc nem rendelkezik teljes konfigurációs beállításai

Ha ezt a hibaüzenetet kapja, akkor lehet, hogy nincs-e a tárfiók kulcsainak lekéréséhez szükséges engedélyekkel. Győződjön meg arról, ha ez a helyzet, lépjen a portálra, és keresse meg a Storage-fiókjában. Segítségével gyorsan ehhez kattintson a jobb gombbal a tárfiók a csomóponton, és kattintson a "Megnyitás a portál". Ezt követően nyissa meg a "Hozzáférési kulcsok" panel. Ha nem rendelkezik engedéllyel a kulcsok megtekintéséhez, majd megjelenik egy oldal a "Nincs hozzáférése" üzenettel. A probléma megkerüléséhez a fiókkulcs szerzi valaki és csatolása nevére és kulcsára, vagy kérjen egy SAS a tárfiókra, és csatlakoztassa a Storage-fiók használatával.

A fiókkulcsok látja, ha a fájl egy problémát a Githubon, segítünk a probléma megoldásához.

## <a name="issues-with-sas-url"></a>Problémák az SAS URL-címe

Ha egy szolgáltatás SAS URL-címet használ, és ezt a hibát tapasztaló csatlakozik:

* Győződjön meg arról, hogy az URL-címet biztosít-e olvasási és erőforrások listázása a szükséges engedélyekkel.
* Győződjön meg arról, hogy az URL-cím nem járt le.
* Ha az SAS URL-cím alapján egy hozzáférési szabályzatot, győződjön meg arról, hogy a hozzáférési szabályzat nem vissza lett vonva.

Ha véletlenül egy érvénytelen SAS URL-címet használó kapcsolt, és nem lehet leválasztani, kövesse az alábbi lépéseket:

1. Storage Explorer futtatásakor nyomja le az F12 billentyűt a fejlesztői eszközök ablak megnyitásához.
2. Kattintson az alkalmazás lapon, majd kattintson a helyi tároló > file:// a bal oldali fában.
3. Keresse meg a szolgáltatás típusa az problémás SAS URI társított kulcsot. Ha a hibás SAS URI blob tárolóhoz, keresse meg például a nevű kulcs `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. A kulcsnak az értéke egy JSON-tömböt kell lennie. A hibás URI társított objektum található, és távolítsa el azt.
5. Töltse be újra a Storage Explorer a Ctrl + R billentyűkombinációt.

## <a name="linux-dependencies"></a>Linux-függőségek

<!-- Storage Explorer 1.9.0 and later is available as a snap from the Snap Store. The Storage Explorer snap installs all of its dependencies with no extra hassle.

Storage Explorer requires the use of a password manager, which may need to be connected manually before Storage Explorer will work correctly. You can connect Storage Explorer to your system's password manager with the following command:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

You can also download the application .tar.gz file, but you'll have to install dependencies manually. -->

> [!IMPORTANT]
> A Storage Explorerben a. tar.gz letöltési csak Ubuntu disztribúciók esetében támogatott. Más disztribúciók nem ellenőrzött és igényelhetnek alternatív vagy kiegészítő csomagok.

Ezek a csomagok a Linux rendszeren a Storage Explorer leggyakoribb követelmények vonatkoznak:

* [.NET core 2.0 futtatókörnyezet](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` vagy `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer 1.7.0-ás verzió és a korábban a .NET Core 2.0 szükséges. Ha van egy újabb verziója telepítve van a .NET Core, akkor kell [javítani a Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Ha a Storage Explorer 1.8.0-as vagy nagyobb, majd futtatja .NET Core 2.2 használatával kell lennie. Nem ellenőrzött túli 2.2-es verziója jelenleg működik.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. A Storage Explorer letöltése.
2. Telepítse a [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. A Storage Explorer letöltése.
2. Telepítse a [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. A Storage Explorer letöltése
2. Telepítse a [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. A Storage Explorer letöltése
2. Telepítse a [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Futtassa a következő parancsot:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Javítás a Storage Explorer újabb verzióiban a .NET Core

A Storage Explorer 1.7.0-ás vagy régebbi kiadást használja, szükség lehet javítani a .NET Core Storage Explorer által használt verzióját.

1. Töltse le a StreamJsonRpc 1.5.43 verziója [nugetről](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Keresse meg a "Csomag letöltése" hivatkozásra az oldal jobb oldalán.
2. A csomag a letöltés után módosíthatja a fájl kiterjesztése a `.nupkg` való `.zip`.
3. A csomag kibontásához.
4. Nyissa meg a `streamjsonrpc.1.5.43/lib/netstandard1.1/` mappát.
5. Másolás `StreamJsonRpc.dll` a Storage Explorer mappán belül a következő helyekre:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Nyissa meg a Explorer, az Azure portal nem működik

Ha a "Megnyitás az Explorerben" gombra az Azure Portalon az Ön számára nem működik, győződjön meg arról, kompatibilis böngészőt használ. Az alábbi böngészők kompatibilitás teszteltük.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>További lépések

Ha a megoldások egyike sem működik, majd [nyisson egy problémát a Githubon](https://github.com/Microsoft/AzureStorageExplorer/issues). Is gyorsan kaphat a Githubra bal alsó sarokban lévő "Jelentés problémát a Githubban" gomb segítségével.

![Visszajelzés](./media/storage-explorer-troubleshooting/feedback-button.PNG)
