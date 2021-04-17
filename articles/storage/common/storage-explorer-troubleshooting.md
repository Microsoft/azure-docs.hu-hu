---
title: Azure Storage Explorer hibaelhárítási útmutató | Microsoft Docs
description: A hibakeresési technikák áttekintése Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dfc8fe0f1b4bc043feecd5c76340d48bc5421854
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568539"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Az Azure Storage Explorer hibaelhárítási útmutatója

A Microsoft Azure Storage Explorer egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszeren. Az alkalmazás az Azure-ban, országos felhőkörnyezetekben és az Azure Stackben üzemeltetett tárfiókokhoz tud csatlakozni.

Ez az útmutató a gyakori problémák megoldásait foglalja Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC-engedélyekkel kapcsolatos problémák

Az Azure szerepköralapú hozzáférés-vezérlése [Az Azure RBAC](../../role-based-access-control/overview.md) lehetővé teszi az Azure-erőforrások részletes hozzáférés-kezelését az engedélykészletek szerepkörökbe való _egyesítésével._ Íme néhány stratégia az Azure RBAC optimális működéshez a Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hogyan az erőforrásokat a Storage Explorer?

Ha problémákat tapasztal a tárolási erőforrások Azure RBAC-n keresztüli elérésekor, előfordulhat, hogy nem rendelkezik a megfelelő szerepkörökkel. A következő szakaszok ismertetik azokat az engedélyeket Storage Explorer amelyek jelenleg a tárolási erőforrásokhoz való hozzáféréshez szükségesek. Ha nem biztos abban, hogy rendelkezik a megfelelő szerepkörökkel vagy engedélyekkel, forduljon az Azure-fiók rendszergazdájához.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Read: List/Get Storage Account(s)" engedélyekkel probléma

A tárfiókok listához engedéllyel kell rendelkeznie. Az engedélyhez Olvasó szerepkört kell _hozzárendelni._

#### <a name="list-storage-account-keys"></a>Tárfiókkulcsok listája

Storage Explorer fiókkulcsokat is használhat a kérések hitelesítéséhez. A fiókkulcsokat nagyobb teljesítményű szerepkörök, például a Közreműködő szerepkör segítségével _is elérheti._

> [!NOTE]
> A hozzáférési kulcsokkal korlátlan engedélyeket adhat mindenki számára, aki a birtokában van. Ezért nem javasoljuk, hogy ezeket a kulcsokat a fiókfelhasználóknak adja ki. Ha vissza kell vonnia a hozzáférési kulcsokat, újragenerálhatja őket a [Azure Portal.](https://portal.azure.com/)

#### <a name="data-roles"></a>Adatszerepk szerepkörök

Legalább egy olyan szerepkört hozzá kell rendelnie, amely hozzáférést biztosít az erőforrásokból származó adatok olvasása számára. Ha például blobokat szeretne listába sorolni vagy letölteni, legalább a Storage Blob-adatolvasó szerepkörre _lesz_ szüksége.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Miért van szükség felügyeleti réteg szerepkörre ahhoz, hogy az erőforrásokat a Storage Explorer?

Az Azure Storage két réteggel rendelkezik a hozzáféréshez: _felügyelet és_ _adatok._ Az előfizetések és a tárfiókok a felügyeleti rétegen keresztül érhetők el. A tárolók, blobok és más adatforrások az adatrétegen keresztül érhetők el. Ha például le szeretné kérni a tárfiókok listáját az Azure-ból, küldjön egy kérést a felügyeleti végpontra. Ha blobtárolók listáját szeretné egy fiókban, küldjön egy kérést a megfelelő szolgáltatásvégpontnak.

Az Azure-szerepkörök engedélyeket adhatnak a felügyeleti vagy az adatrétegbeli hozzáféréshez. Az Olvasó szerepkör például csak olvasási hozzáférést biztosít a felügyeleti réteg erőforrásaihoz.

Szigorúan véve az Olvasó szerepkör nem biztosít adatréteg-engedélyeket, és nem szükséges az adatréteg eléréséhez.

Storage Explorer megkönnyíti az erőforrásokhoz való hozzáférést az Azure-erőforrásokhoz való csatlakozáshoz szükséges információk összegyűjtésével. A blobtárolók megjelenítéséhez például a Storage Explorer "list containers" kérelmet küld a Blob szolgáltatásvégpontnak. A végpont le Storage Explorer megkeresi azon előfizetések és tárfiókok listáját, amelyekhez hozzáféréssel rendelkezik. Az előfizetések és tárfiókok megkeresése érdekében a Storage Explorer is hozzá kell férni a felügyeleti réteghez.

Ha nincs olyan szerepköre, amely felügyeleti rétegre vonatkozó engedélyeket biztosít, a Storage Explorer nem tudja le kérni az adatréteghez való csatlakozáshoz szükséges információkat.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Mi történik, ha nem tudom le kérni a rendszergazda által szükséges felügyeleti réteghez szükséges engedélyeket?

Ha blobtárolókat, tárolókat ADLS Gen2, könyvtárakat vagy üzenetsorokat szeretne elérni, ezeket az erőforrásokat az Azure-beli hitelesítő adataival csatolhatja.

1. Nyissa meg a Csatlakozás párbeszédpanelt.
1. Válassza ki azt az erőforrástípust, amelyhez csatlakozni szeretne.
1. Válassza **a Bejelentkezés Azure Active Directory (Azure AD) lehetőséget.** Kattintson a **Tovább** gombra.
1. Válassza ki azt a felhasználói fiókot és bérlőt, amely ahhoz az erőforráshoz van társítva, amelyhez csatolva van. Kattintson a **Tovább** gombra.
1. Adja meg az erőforrás URL-címét, és adjon meg egy egyedi megjelenítendő nevet a kapcsolat számára. Válassza **a Tovább,** majd a **Csatlakozás lehetőséget.**

Más erőforrástípusok esetében jelenleg nincs Azure RBAC-hoz kapcsolódó megoldás. Áthidaló megoldásként az alábbi lépésekkel kérhet SAS URL-címet, majd csatolhatja az erőforráshoz:

1. Nyissa meg a Csatlakozás párbeszédpanelt.
1. Válassza ki azt az erőforrástípust, amelyhez csatlakozni szeretne.
1. Válassza **a Közös hozzáférésű jogosultság jogosultsága (SAS) lehetőséget.** Kattintson a **Tovább** gombra.
1. Adja meg a kapott SAS URL-címet, és adjon meg egy egyedi megjelenítendő nevet a kapcsolat számára. Válassza **a Tovább,** majd a **Csatlakozás lehetőséget.**
 
Az erőforrásokhoz való csatolással kapcsolatos további információkért lásd: Attach to an Individual Resource (Csatolás [egyéni erőforráshoz).](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource)

### <a name="recommended-azure-built-in-roles"></a>Ajánlott beépített Azure-szerepkörök

Számos beépített Azure-szerepkör használhatja a virtuális Storage Explorer. Néhány ilyen szerepkör:
- [Tulajdonos:](../../role-based-access-control/built-in-roles.md#owner)Mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is.
- [Közreműködő:](../../role-based-access-control/built-in-roles.md#contributor)Mindent kezelhet, kivéve az erőforrásokhoz való hozzáférést.
- [Olvasó:](../../role-based-access-control/built-in-roles.md#reader)Erőforrások olvasása és listása.
- [Tárfiók-közreműködő:](../../role-based-access-control/built-in-roles.md#storage-account-contributor)A tárfiókok teljes körű kezelése.
- [Storage-blobadatok tulajdonosa:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)Teljes hozzáférés az Azure Storage-blobtárolókhoz és -adatokhoz.
- [Storage-blobadatok közreműködője:](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)Azure Storage-tárolók és -blobok olvasása, írása és törlése.
- [Storage-blobadatok olvasója:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)Azure Storage-tárolók és -blobok olvasása és listása.

> [!NOTE]
> A Tulajdonos, Közreműködő és Tárfiók-közreműködő szerepkör fiókkulcs-hozzáférést biztosít.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hiba: Önaírt tanúsítvány a tanúsítványláncban (és hasonló hibák)

A tanúsítványhibák általában a következő helyzetekben fordulnak elő:

- Az alkalmazás egy transzparens _proxyn keresztül csatlakozik._ Ez azt jelenti, hogy egy kiszolgáló (például a vállalati kiszolgáló) elfogja a HTTPS-forgalmat, visszafejti, majd önaírt tanúsítvánnyal titkosítja azt.
- Olyan alkalmazást futtat, amely önaírt TLS-/SSL-tanúsítványt injektál a kapott HTTPS-üzenetekbe. A tanúsítványokat be injektáló alkalmazások közé tartozik például a víruskereső és a hálózati forgalomellenőrző szoftver.

Ha Storage Explorer vagy nem megbízható tanúsítványt lát, többé nem tudja, hogy a fogadott HTTPS-üzenet megváltozott-e. Ha rendelkezik az önaírt tanúsítvány másolatával, az alábbi lépésekkel utasíthatja Storage Explorer, hogy bízzon meg benne:

1. Szerezze be a tanúsítvány Base-64 kódolású X.509 (.cer) példányát.
2. Nyissa meg **az SSL-tanúsítványok** szerkesztése Tanúsítványok importálása lehetőséget, majd a fájlválasztóval keresse meg, válassza ki és nyissa meg a  >    >  .cer fájlt.

Ez a probléma akkor is előfordulhat, ha több tanúsítvány (gyökér- és köztes tanúsítvány) van. A hiba kijavításhoz mindkét tanúsítványt hozzá kell adni.

Ha nem tudja biztosan, honnan érkezik a tanúsítvány, az alábbi lépéseket követve találhatja meg:

1. Telepítse az OpenSSL-t.
    * [Windows:](https://slproweb.com/products/Win32OpenSSL.html)A világos verziók bármelyikének elegendőnek kell lennie.
    * Mac és Linux: Szerepelnie kell az operációs rendszernek.
2. Futtassa az OpenSSL-t.
    * Windows: Nyissa meg a telepítési könyvtárat, válassza a **/bin/** lehetőséget, majd kattintson duplán a **openssl.exe.**
    * Mac és Linux: `openssl` Futtassa a következőt egy terminálból: .
3. Futtassa az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha nem biztos benne, hogy mely tanúsítványok önaírtak, jegyezze fel, hogy a tulajdonos és a kiállító `("s:")` `("i:")` bárhol azonos-e.
5. Ha önaírt tanúsítványokat talál, mindegyikhez másolja és illesszen be mindent a fájlból (és beleértve a tanúsítványt is) `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` egy új .cer fájlba.
6. Nyissa meg Storage Explorer, és válassza az **SSL-tanúsítványok szerkesztése**  >    >  **Tanúsítványok importálása gombra.** Ezután a fájlválasztóval keresse meg, jelölje ki és nyissa meg a létrehozott .cer fájlokat.

Ha nem talál önaírt tanúsítványokat az alábbi lépésekkel, lépjen kapcsolatba velünk a visszajelzési eszköz segítségével. A parancssorból is Storage Explorer a `--ignore-certificate-errors` jelölővel. Ha ezzel a jelölővel nyitja meg, Storage Explorer figyelmen kívül hagyja a tanúsítványhibákat.

## <a name="sign-in-issues"></a>Bejelentkezési problémák

### <a name="understanding-sign-in"></a>A bejelentkezés ismertetése

Mindenképpen olvassa el a [Bejelentkezés](./storage-explorer-sign-in.md) a Storage Explorer dokumentációját.

### <a name="frequently-having-to-reenter-credentials"></a>Gyakran kell újra megadni a hitelesítő adatokat

A hitelesítő adatok újra megadását valószínűleg az AAD-rendszergazda által beállított feltételes hozzáférési szabályzatok eredményezik. Amikor Storage Explorer hitelesítő adatok megadását kéri a fiók panelén, egy Hiba **részletei...** hivatkozást kell látnia. Kattintson rá, hogy lássa, Storage Explorer kéri a hitelesítő adatok újra megadását. A feltételes hozzáférési szabályzat olyan hibái, amelyek a hitelesítő adatok újra megadását igénylik, az alábbihoz hasonlóak lehetnek:
- A frissítési jogkivonat lejárt...
- A hozzáféréshez többtényezős hitelesítést kell használnia...
- A rendszergazda által történt konfigurációváltozás miatt...

Ha csökkenteni szeretné a hitelesítő adatoknak a fentihez hasonló hibák miatti újra megadásának gyakoriságát, forduljon az AAD-rendszergazdához.

### <a name="conditional-access-policies"></a>Feltételes hozzáférési szabályzatok

Ha olyan feltételes hozzáférési szabályzatokkal rendelkezik, amelyek teljesülnek a fiókjához, győződjön meg arról, hogy az Alapértelmezett **webböngésző** értéket használja a **Bejelentkezés a következővel** beállításhoz. A beállítással kapcsolatos információkért [lásd: A bejelentkezés helyének módosítása.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>A jogkivonat nem szerezhető be, a bérlő ki van szűrve

Ha olyan hibaüzenetet kap, amely szerint a jogkivonat nem szerezhető be, mert a bérlő ki van szűrve, az azt jelenti, hogy egy olyan erőforráshoz próbál hozzáférni, amely egy kiszűrt bérlőben van. A bérlő adatokkal való feltöltésének a kiszivárgását a Fiók panelen ellenőrizheti, hogy be van-e jelölve a bérlőhöz a hibaüzenetben megadott jelölőnégyzet.  A [bérlők szűrésével](./storage-explorer-sign-in.md#managing-accounts) kapcsolatos további információkért tekintse meg a fiókok kezelésével kapcsolatos Storage Explorer.

## <a name="authentication-library-failed-to-start-properly"></a>A hitelesítési kódtár nem tudott megfelelően elindulni

Ha indításkor egy hibaüzenet jelenik meg, amely szerint a Storage Explorer hitelesítési kódtárának nem sikerült megfelelően elindulni, akkor győződjön meg arról, hogy a telepítési környezet megfelel az összes [előfeltételnek.](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites) A hibaüzenet legvalószínűbb oka az, hogy nem egyik előfeltétel sem áll elő.

Ha úgy véli, hogy a telepítési környezet megfelel az összes előfeltételnek, nyisson egy problémát [a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues/new) A probléma megnyitásakor győződjön meg arról, hogy a következőket tartalmazza:
- Az operációs rendszer.
- A Storage Explorer használt verziószáma.
- Ha ellenőrizte az előfeltételeket.
- [Sikertelen indításból](#authentication-logs) származó hitelesítési naplók Storage Explorer. Az ilyen típusú hibák után a részletes hitelesítés naplózása automatikusan engedélyezve lesz.

### <a name="blank-window-when-using-integrated-sign-in"></a>Üres ablak az integrált bejelentkezés használata esetén

Ha integrált bejelentkezést  választott, és üres bejelentkezési ablakot lát, valószínűleg egy másik bejelentkezési módszerre kell váltania. Az üres bejelentkezési párbeszédpanelek leggyakrabban akkor fordulnak elő, ha egy Active Directory összevonási szolgáltatások (AD FS)- (ADFS-) kiszolgáló Storage Explorer az Electron által nem támogatott átirányítás végrehajtásához.

Ha egy másik bejelentkezési módszerre vált, módosítsa a **Bejelentkezés** a következővel beállítást a **Beállítások**  >    >  **alkalmazásba való bejelentkezés beállításával:**. További információ a különböző bejelentkezési módszerekről: A bejelentkezés [helyének módosítása.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="reauthentication-loop-or-upn-change"></a>Újrahitelesítő hurkok vagy UPN-módosítás

Ha újrahitelesítő hurokba került, vagy módosította az egyik fiókja egyszerű egyszerű felhasználóit, próbálkozzon az alábbi lépésekkel:

1. Nyissa meg Storage Explorer
2. Ugrás a Súgó > gombra
3. Győződjön meg arról, hogy legalább a Hitelesítés be van jelölve. A nem alaphelyzetbe állítani kívánt többi elem jelölését is törölje.
4. Kattintson az Alaphelyzetbe állítás gombra
5. Indítsa Storage Explorer, majd próbáljon meg újra bejelentkezni.

Ha az alaphelyzetbe állítás után továbbra is problémákat fog látni, próbálkozzon az alábbi lépésekkel:

1. Nyissa meg Storage Explorer
2. Távolítsa el az összes fiókot, majd zárja be Storage Explorer.
3. Törölje a `.IdentityService` mappát a gépről. Windows rendszeren a mappa a következő helyen található: `C:\users\<username>\AppData\Local` . Mac és Linux rendszereken a mappa a felhasználói könyvtár gyökerében található.
4. Mac vagy Linux rendszeren a Microsoft.Developer.IdentityService bejegyzést is törölnie kell az operációs rendszer kulcstárolójában. A Mac gépen a kulcstár a *Keychain-alkalmazás.* Linux rendszeren az alkalmazás neve általában _Keyring_, de a név a disztribúciótól függően eltérő lehet.
6. Indítsa Storage Explorer, majd próbáljon meg újra bejelentkezni.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: kulcslánchibák vagy nincs bejelentkezési ablak

A macOS-kulcslánc néha olyan állapotot is beírhat, amely problémákat okoz a Storage Explorer hitelesítési kódtárában. A kulcslánc az alábbi lépésekkel lép ki ebből az állapotból:

1. Zárja be Storage Explorer.
2. Nyissa meg a kulcsláncot (nyomja le a Command+Spacebar billentyűkombinációt, írja be a **kulcsláncot,** majd nyomja le az Enter billentyűt).
3. Válassza a "bejelentkezés" kulcsláncot.
4. A kulcslánc zároláshoz válassza a lakat ikont. (A folyamat befejezésekor a lakat zárolva jelenik meg. A megnyitott alkalmazásoktól függően néhány másodperc is eltelhet).

    ![Lakat ikon](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Nyissa meg a Storage Explorert.
6. A rendszer a következő üzenethez hasonló üzenetet fog kérni: "Service Hub wants to access the Keychain" (A Szolgáltatásközpont hozzá szeretne férni a kulcslánchoz). Adja meg a Mac-rendszergazdai fiók jelszavát, és válassza az **Always Allow** (vagy az **Allow** (Mindig engedélyezi) lehetőséget, ha az **Always Allow** nem érhető el).
7. Próbáljon meg bejelentkezni.

### <a name="default-browser-doesnt-open"></a>Az alapértelmezett böngésző nem nyílik meg

Ha az alapértelmezett böngésző nem nyílik meg a bejelentkezéskor, próbálkozzon az alábbi technikákkal:
- Újraindítási Storage Explorer
- Nyissa meg manuálisan a böngészőt a bejelentkezés megkezdése előtt
- Próbálja meg az integrált [](./storage-explorer-sign-in.md#changing-where-sign-in-happens) **bejelentkezést használni,** ehhez a bejelentkezési hely módosításával és az ehhez való útmutatásért tekintse meg a következőt: .

### <a name="other-sign-in-issues"></a>Egyéb bejelentkezési problémák

Ha a fentiek egyike sem vonatkozik a bejelentkezési problémára, vagy ha nem sikerül megoldania a bejelentkezési problémát, nyisson egy problémát [a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues)

### <a name="missing-subscriptions-and-broken-tenants"></a>Hiányzó előfizetések és hibás bérlők

Ha a sikeres bejelentkezés után nem tudja lekérni az előfizetéseket, próbálkozzon az alábbi hibaelhárítási módszerekkel:

* Ellenőrizze, hogy a fiókja rendelkezik-e hozzáféréssel a várt előfizetéshez. A hozzáférést úgy ellenőrizheti, hogy bejelentkezik a portálra a használni próbált Azure-környezethez.
* Győződjön meg arról, hogy a megfelelő Azure-környezetben jelentkezett be (Azure, Azure China 21Vianet, Azure Germany, Azure US Government vagy egyéni környezet).
* Ha proxykiszolgáló mögött van, győződjön meg arról, hogy megfelelően konfigurálta Storage Explorer proxyt.
* Próbálja meg eltávolítani, majd újra hozzáadni a fiókot.
* Ha van "További információ" vagy "Hiba részletei" hivatkozás, ellenőrizze, hogy a rendszer mely hibaüzeneteket jelenti a sikertelen bérlők számára. Ha nem tudja, hogyan reagáljon a hibaüzenetekre, nyisson egy [problémát a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Nem lehet eltávolítani egy csatolt tárfiókot vagy erőforrást

Ha nem tud eltávolítani egy csatolt fiókot vagy tárerőforrást a felhasználói felületen keresztül, manuálisan törölheti az összes csatolt erőforrást a következő mappák törlésével:

* Windows: `%AppData%/StorageExplorer`
* Macos: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> A Storage Explorer törlése előtt zárja be a mappát.

> [!NOTE]
> Ha importált már SSL-tanúsítványokat, biztonsági adatokat kell futtatnia a könyvtár `certs` tartalmáról. Később a biztonsági másolat segítségével újraimportálhatja az SSL-tanúsítványokat.

## <a name="proxy-issues"></a>Proxyval kapcsolatos problémák

Storage Explorer támogatja az Azure Storage-erőforrások proxykiszolgálón keresztüli csatlakozását. Ha problémát tapasztal az Azure-hoz proxyn keresztül történő csatlakozáskor, íme néhány javaslat.

> [!NOTE]
> Storage Explorer csak a proxykiszolgálók alapszintű hitelesítését támogatja. Más hitelesítési módszerek, például az NTLM, nem támogatottak.

> [!NOTE]
> Storage Explorer nem támogatja a proxybeállítások konfigurálásához szükséges automatikus proxykonfigurációs fájlokat.

### <a name="verify-storage-explorer-proxy-settings"></a>Proxybeállítások Storage Explorer ellenőrzése

Az **Application → Proxy → proxykonfigurációs** beállítás határozza meg, Storage Explorer melyik forrásból kapja meg a proxykonfigurációt.

Ha a "Környezeti változók használata" lehetőséget választja, mindenképpen állítsa be a vagy a környezeti változót (a környezeti változók megkülönböztetik a kis- és nagybetűket, ezért ügyeljen arra, hogy a megfelelő változókat `HTTPS_PROXY` `HTTP_PROXY` állítsa be). Ha ezek a változók meghatározatlanok vagy érvénytelenek, Storage Explorer nem fog proxyt használni. Indítsa Storage Explorer környezeti változók módosítása után.

Ha az "Alkalmazásproxy-beállítások használata" lehetőséget választja, ellenőrizze, hogy helyesek-e az alkalmazáson belüli proxybeállítások.

### <a name="steps-for-diagnosing-issues"></a>A problémák diagnosztizálásának lépései

Ha továbbra is problémákat tapasztal, próbálkozzon az alábbi hibaelhárítási módszerekkel:

1. Ha a proxy használata nélkül is tud csatlakozni az internethez, ellenőrizze, hogy a Storage Explorer proxybeállítások engedélyezése nélkül működik-e. Ha Storage Explorer sikeresen csatlakozik, probléma lehet a proxykiszolgálóval. A problémák azonosításához forduljon a rendszergazdához.
2. Ellenőrizze, hogy a proxykiszolgálót használó egyéb alkalmazások a várt módon működnek-e.
3. Ellenőrizze, hogy tud-e csatlakozni a portálhoz a használni próbált Azure-környezethez.
4. Ellenőrizze, hogy fogadhat-e válaszokat a szolgáltatásvégpontjairól. Adja meg az egyik végponti URL-címét a böngészőben. Ha tud csatlakozni, egy vagy hasonló `InvalidQueryParameterValue` XML-választ kell kapnia.
5. Ellenőrizze, hogy tud-e csatlakozni valaki Storage Explorer, aki azonos proxykiszolgálóval használja a kapcsolatot. Ha igen, előfordulhat, hogy kapcsolatba kell lépnie a proxykiszolgáló rendszergazdával.

### <a name="tools-for-diagnosing-issues"></a>Eszközök a problémák diagnosztizálására

Egy hálózati eszköz, például a Fiddler segíthet diagnosztizálni a problémákat.

1. Konfigurálja a hálózati eszközt a helyi gazdagépen futó proxykiszolgálóként. Ha továbbra is egy tényleges proxy mögött kell dolgoznia, előfordulhat, hogy a hálózati eszközt úgy kell konfigurálnia, hogy a proxyn keresztül csatlakozzon.
2. Ellenőrizze a hálózati eszköz által használt portszámot.
3. Konfigurálja Storage Explorer proxybeállításokat a helyi gazdagép és a hálózati eszköz portszámának használatára (például "localhost:8888").
 
Ha megfelelően van beállítva, a hálózatkezelési eszköz naplóz a Storage Explorer és szolgáltatásvégpontokkal kapcsolatos hálózati kéréseket.
 
Ha úgy tűnik, hogy a hálózati eszköz nem Storage Explorer a forgalmat, próbálja meg egy másik alkalmazással tesztelni az eszközt. Adja meg például az egyik tárolási erőforrás (például ) végponti URL-címét egy webböngészőben, és a következőhöz hasonló választ `https://contoso.blob.core.windows.net/` fog kapni:

  ![Kódminta](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  A válasz arra utal, hogy az erőforrás létezik, pedig nem fér hozzá.

Ha a hálózati eszköz csak más alkalmazásokból származó forgalmat mutat, előfordulhat, hogy módosítania kell a proxybeállításokat a Storage Explorer. Ellenkező esetben módosítania kellett az eszköz beállításait.

### <a name="contact-proxy-server-admin"></a>Kapcsolatfelvétel a proxykiszolgáló rendszergazdával

Ha a proxybeállítások helyesek, előfordulhat, hogy kapcsolatba kell lépnie a proxykiszolgáló rendszergazdájával, hogy:

* Győződjön meg arról, hogy a proxy nem blokkolja az Azure-felügyeletbe vagy az erőforrásvégpontba történő forgalmat.
* Ellenőrizze a proxykiszolgáló által használt hitelesítési protokollt. Storage Explorer csak az alapszintű hitelesítési protokollokat támogatja. Storage Explorer nem támogatja az NTLM-proxykat.

## <a name="unable-to-retrieve-children-error-message"></a>"Nem sikerült lekérni a gyermekeket" hibaüzenet

Ha proxyn keresztül csatlakozik az Azure-hoz, ellenőrizze, hogy a proxybeállítások helyesek-e.

Ha egy előfizetés vagy fiók tulajdonosa hozzáférést adott Önnek egy erőforráshoz, ellenőrizze, hogy rendelkezik-e olvasási vagy listás engedélyekkel az adott erőforráshoz.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A kapcsolati sztring nem rendelkezik teljes konfigurációs beállításokkal

Ha ezt a hibaüzenetet kapja, lehetséges, hogy nem rendelkezik a tárfiók kulcsának beszerzéséhez szükséges engedélyekkel. Annak megerősítéséhez, hogy ez a helyzet, keresse meg a tárfiókját a portálon. Ehhez kattintson a jobb gombbal a tárfiók csomópontjára, és válassza a Megnyitás a **portálon gombra.** Ezután válassza a Hozzáférési **kulcsok panelt.** Ha nem rendelkezik a kulcsok megtekintéséhez szükséges engedélyekkel, a "Nincs hozzáférése" üzenet jelenik meg. A probléma megoldásához beszerezheti a fiókkulcsot valaki mástól, és a névvel és a kulccsal csatolhatja, vagy megkérhet valakit, hogy kérjen SAS-t a tárfiókhoz, és használja a tárfiók csatolásához.

Ha látja a fiókkulcsokat, jelentsen be egy problémát a GitHubon, hogy segítünk a probléma megoldásában.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Hiba történt az új kapcsolat hozzáadása során: TypeError: Nem olvasható a nem definiált "version" tulajdonsága

Ha ezt a hibaüzenetet kapja, amikor egyéni kapcsolatot próbál hozzáadni, a helyi hitelesítőadat-kezelőben tárolt kapcsolati adatok sérültek lehetnek. A probléma megoldásához törölje a sérült helyi kapcsolatokat, majd adja hozzá újra őket:

1. Indítsa el Storage Explorer. A menüben válassza a **Súgó**  >  **Fejlesztői eszközök** lehetőséget.
2. A megnyitott ablakban, az **Alkalmazás lapon** válassza a Helyi **tárterület** (bal oldal) > **file://.**
3. A problémás kapcsolat típusától függően keresse meg a kulcsát, majd másolja az értékét egy szövegszerkesztőbe. Az érték az egyéni kapcsolatneveket tartalmazó tömb, az alábbihoz hasonló:
    * Tárfiókok
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blobtárolók
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Fájlmegosztások
        * `StorageExplorer_CustomConnections_Files_v1`
    * Üzenetsorok
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Táblák
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Miután elmenti az aktuális kapcsolatneveket, állítsa az értékét a Fejlesztői eszközök `[]` értékre.

Ha meg szeretné őrizni a nem sérült kapcsolatokat, a következő lépésekkel megkeresheti a sérült kapcsolatokat. Ha nem gond, hogy az összes meglévő kapcsolat megszakad, kihagyhatja ezeket a lépéseket, és a platformspecifikus utasításokat követve törölheti a kapcsolati adatokat.

1. Egy szövegszerkesztőben adja hozzá újra az egyes kapcsolatneveket a Fejlesztői eszközök, majd ellenőrizze, hogy a kapcsolat továbbra is működik-e.
2. Ha egy kapcsolat megfelelően működik, akkor nem sérült, és nyugodtan hagyhatja ott. Ha egy kapcsolat nem működik, távolítsa el az értékét a Fejlesztői eszközök, és rögzítse, hogy később újra hozzáadja.
3. Ismételje meg ezt, amíg meg nem vizsgálta az összes kapcsolatot.

Miután végigment az összes kapcsolaton, minden olyan kapcsolatnévnél, amelyet nem adott vissza, törölje a sérült adatokat (ha vannak), majd adja hozzá őket a következő szabványos Storage Explorer:

### <a name="windows"></a>[Windows](#tab/Windows)

1. A **Start menüben** keressen rá a **Hitelesítőadat-kezelő** és nyissa meg.
2. Ugrás a **Windows-hitelesítő adatokra.**
3. Az **Általános hitelesítő adatok alatt** keresse meg a kulccsal (például : ) szereplő `<connection_type_key>/<corrupted_connection_name>` `StorageExplorer_CustomConnections_Accounts_v1/account1` bejegyzéseket.
4. Törölje ezeket a bejegyzéseket, és adja hozzá újra a kapcsolatokat.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Nyissa meg a Reflektorfényt (Command+Szóköz billentyűkombináció), és keressen rá a **Kulcslánc-hozzáférés kifejezésre.**
2. Keresse meg a kulccsal (például: `<connection_type_key>/<corrupted_connection_name>` ) szereplő `StorageExplorer_CustomConnections_Accounts_v1/account1` bejegyzéseket.
3. Törölje ezeket a bejegyzéseket, és adja hozzá újra a kapcsolatokat.

### <a name="linux"></a>[Linux](#tab/Linux)

A helyi hitelesítő adatok kezelése a Linux-disztribúciótól függően változik. Ha a Linux-disztribúció nem biztosít beépített grafikus felhasználói felületi eszközt a helyi hitelesítő adatok kezeléséhez, telepíthet egy külső eszközt a helyi hitelesítő adatok kezeléséhez. Használhatja például a [Seahorse](https://wiki.gnome.org/Apps/Seahorse/)eszközt, egy nyílt forráskódú grafikus felhasználói felületi eszközt a Linux helyi hitelesítő adatainak kezeléséhez.

1. Nyissa meg a helyi hitelesítőadat-kezelő eszközt, és keresse meg a mentett hitelesítő adatokat.
2. Keresse meg a kulcsot (például ) is. `<connection_type_key>/<corrupted_connection_name>` `StorageExplorer_CustomConnections_Accounts_v1/account1`
3. Törölje ezeket a bejegyzéseket, és adja hozzá újra a kapcsolatokat.
---

Ha a lépések futtatása után is ezt a hibát találja, vagy meg szeretné osztani a kapcsolatok sérülésére gyanakszik, nyisson meg egy problémát [a](https://github.com/microsoft/AzureStorageExplorer/issues) GitHub-oldalon.

## <a name="issues-with-sas-url"></a>SAS URL-lel kapcsolatos problémák

Ha SAS URL-címen keresztül csatlakozik egy szolgáltatáshoz, és hibát tapasztal:

* Ellenőrizze, hogy az URL-cím biztosítja-e az erőforrások olvassához vagy listához szükséges engedélyeket.
* Ellenőrizze, hogy az URL-cím nem járt-e le.
* Ha az SAS URL-cím egy hozzáférési szabályzaton alapul, ellenőrizze, hogy a hozzáférési szabályzat nincs-e visszavonva.

Ha véletlenül érvénytelen SAS URL-címet használt, és most nem tudja leválasztani, kövesse az alábbi lépéseket:

1. A futtatott alkalmazás Storage Explorer nyomja le az F12 billentyűt a Fejlesztői eszközök megnyitásához.
2. Az Alkalmazás **lapon** válassza a **Helyi tárterület**  >  **file://** a bal oldali fában.
3. Keresse meg a problémás SAS URI szolgáltatástípushoz társított kulcsot. Ha például egy blobtároló sas URI-ja hibás, keresse meg a nevű `StorageExplorer_AddStorageServiceSAS_v1_blob` kulcsot.
4. A kulcs értékének JSON-tömbnek kell lennie. Keresse meg a hibás URI-val társított objektumot, majd törölje.
5. Nyomja le a Ctrl+R billentyűkombinációt a Storage Explorer.

## <a name="linux-dependencies"></a>Linux-függőségek

### <a name="snap"></a>Eltör

Storage Explorer 1.10.0-s vagy újabb a beépülő modultárolóból érhető el illesztésként. A Storage Explorer az összes függőségét automatikusan telepíti, és akkor frissül, amikor elérhetővé válik a beépülő modul új verziója. A Storage Explorer telepítés ajánlott módja a telepítés.

Storage Explorer jelszókezelőt kell használnia, amelyet manuálisan kell csatlakoztatnia, mielőtt Storage Explorer megfelelően működne. A Storage Explorer a rendszer jelszókezelőjéhez a következő parancs futtatásával csatlakozhat:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>.tar.gz fájl

Az alkalmazást .tar.gz fájlként is letöltheti, de a függőségeket manuálisan kell telepítenie.

Storage Explorer .tar.gz letöltésben megadott beállítások csak az Ubuntu következő verzióihoz támogatottak. Storage Explorer linuxos disztribúciók is működnek, de hivatalosan nem támogatottak.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Storage Explorer a .NET Core telepítése szükséges a rendszeren. A .NET Core 2.1-et javasoljuk, Storage Explorer a 2.2-essel is működni fog.

> [!NOTE]
> Storage Explorer 1.7.0-s és korábbi verzióihoz a .NET Core 2.0 szükséges. Ha a .NET Core újabb verziója van telepítve, javítást kell [Storage Explorer.](#patching-storage-explorer-for-newer-versions-of-net-core) Ha az 1.8.0-Storage Explorer fut, legalább a .NET Core 2.1-es vagy újabb rendszerre lesz szüksége.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Töltse le Storage Explorer .tar.gz fájlt.
2. Telepítse a [.NET Core-runtime-t:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Töltse le Storage Explorer .tar.gz fájlt.
2. Telepítse a [.NET Core-runtime-t:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Töltse le Storage Explorer .tar.gz fájlt.
2. Telepítse a [.NET Core-runtime-t:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

A canonical Storage Explorer szükséges kódtárak előre telepítették a Canonical ubuntus standard telepítéseit. Előfordulhat, hogy néhány kódtárból hiányoznak egyéni környezetek. Ha problémái vannak a Storage Explorer, javasoljuk, hogy győződjön meg arról, hogy a következő csomagok telepítve vannak a rendszeren:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Javítás Storage Explorer .NET Core újabb verzióihoz

Az Storage Explorer 1.7.0-s vagy korábbi verzióiban előfordulhat, hogy javítást kell alkalmaznia a .NET Core által használt Storage Explorer:

1. Töltse le a StreamJsonRpc 1.5.43-as verzióját [a NuGetről.](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) Keresse meg a "Csomag letöltése" hivatkozást a lap jobb oldalán.
2. A csomag letöltése után módosítsa a fájlkiterjesztését a `.nupkg` következőre: `.zip` .
3. Csomagolja ki a csomagot.
4. Nyissa meg a `streamjsonrpc.1.5.43/lib/netstandard1.1/` mappát.
5. Másolja `StreamJsonRpc.dll` a következő helyre a Storage Explorer mappában:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>A "Megnyitás az Explorerben" a Azure Portal nem működik

Ha az **Open In Explorer** (Böngésző megnyitása) Azure Portal nem működik, ellenőrizze, hogy kompatibilis böngészőt használ-e. Az alábbi böngészők kompatibilitását tesztelték:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Naplók gyűjtése

Amikor jelentést készít egy problémáról a GitHubon, előfordulhat, hogy a rendszer arra kéri, hogy gyűjtsön össze bizonyos naplókat a probléma diagnosztizálásához.

### <a name="storage-explorer-logs"></a>Storage Explorer naplók

Az 1.16.0-s verziótól kezdődően a Storage Explorer a saját alkalmazásnaplóiba naplózza a különböző dolgokat. Ezeket a naplókat könnyen elérheti, ha a Súgó > a Naplók könyvtárának megnyitása elemre. Alapértelmezés szerint Storage Explorer a naplókat alacsony részletességi szinten. A részletességi szint módosítása érdekében adjon hozzá egy nevű környezeti változót a következő értékek `STG_EX_LOG_LEVEL` bármelyikével:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (alapértelmezett szint)
- `verbose`
- `debug`

A naplók mappákba vannak osztva a futtatott Storage Explorer munkamenetenként. Javasoljuk, hogy a megosztani kívánt naplófájlokat tömörítsen egy zip-archívumba, különböző munkamenetek fájljaival különböző mappákban.

### <a name="authentication-logs"></a>Hitelesítési naplók

A bejelentkezéssel vagy a Storage Explorer hitelesítési kódtárával kapcsolatos problémák esetén nagy valószínűséggel hitelesítési naplókat kell gyűjtenie. A hitelesítési naplók a következő adatokat tárolják:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS és Linux `~/.ServiceHub/logs`

Általában az alábbi lépéseket követve gyűjtheti össze a naplókat:

1. A Bejelentkezés a > lapon > részletes hitelesítés naplózása jelölőnégyzetet. Ha Storage Explorer a hitelesítési kódtárával való probléma miatt nem indul el, ezt a rendszer automatikusan meg fogja tenni.
2. Zárja be Storage Explorer.
1. Választható/ajánlott: törölje a meglévő naplókat a `logs` mappából. Ezzel csökkentheti a nekünk küldenünk szükséges információk mennyiségét.
4. Nyissa meg Storage Explorer, és reprodukálja a problémát
5. Zárja be Storage Explorer
6. Tömörítse a mappa `log` tartalmát.

### <a name="azcopy-logs"></a>AzCopy-naplók

Ha problémákat okoz az adatok átvitele, előfordulhat, hogy le kell szereznie az AzCopy-naplókat. Az AzCopy-naplók könnyen megtalálhatóak két különböző módszerrel:
- Ha a sikertelen átvitelek továbbra is a tevékenységnaplóban vannak, kattintson az "Ugrás az AzCopy-naplófájlhoz" elemre
- A múltban meghiúsult átvitelek esetén az AzCopy logs mappában. Ez a mappa itt található:
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS és Linux ~/.azcopy

### <a name="network-logs"></a>Hálózati naplók

Egyes problémákhoz meg kell adnia a hálózati hívások naplóit a Storage Explorer. Windows rendszeren ezt a Fiddler használatával is meg lehet tenni.

> [!NOTE]
> A Fiddler-nyomkövetések tartalmazhatnak a böngészőben a nyomkövetés gyűjtése során megadott/küldött jelszavakat. Mindenképpen olvassa el a Fiddler-nyomkövetések sanitizálására vonatkozó utasításokat. Ne töltsön fel Fiddler-nyomkövetéseket a GitHubra. A rendszer tudatja, hogy hová küldheti biztonságosan a Fiddler-nyomkövetést.

1. rész: A Fiddler telepítése és konfigurálása

1. A Fiddler telepítése
2. A Fiddler elindul
3. Ugrás az Eszközök > beállításokra
4. Kattintson a HTTPS lapra
5. Győződjön meg arról, hogy a Capture CONNECTs és a Decrypt HTTPS-forgalom be van jelölve
6. Kattintson a Műveletek gombra
7. A következő párbeszédpanelen válassza a "Főtanúsítvány megbízhatósága", majd az "Igen" lehetőséget
8. Kattintson ismét a Műveletek gombra
9. Válassza a "Főtanúsítvány exportálása asztali gépre" lehetőséget
10. Ugrás az asztalra
11. Keresse meg a FiddlerRoot.cer fájlt
12. Kattintson duplán a megnyitáshoz
13. Ugrás a Részletek lapra
14. Kattintson a "Másolás fájlba..." elemre.
15. Az exportálási varázslóban válassza a következő beállításokat
    - Base-64 kódolású X.509
    - A fájlnévhez kattintson a Tallózás... a C:\Users \<your user dir> \AppData\Roaming\StorageExplorer\certs mappába, majd bármilyen fájlnévként mentheti
16. Zárja be a tanúsítványablakot
17. Első Storage Explorer
18. Ugrás a Proxy > szerkesztésére
19. A párbeszédpanelen válassza az "Alkalmazásproxy-beállítások használata" lehetőséget, és állítsa az URL-címet a következőre: , a portot pedig http://localhost a 8888-asra.
20. Kattintson az OK gombra.
21. Újraindítási Storage Explorer
22. A Fiddlerben látnia kell egy folyamatból `storageexplorer:` származó hálózati hívásokat

2. rész: A probléma reprodukálja
1. A Fiddleren kívül minden alkalmazás bezárása
2. Törölje a Fiddler-naplót (X ikon a bal felső sarokban, a Nézet menü közelében)
3. Választható/ajánlott: állítsa be a Fiddlert néhány percre, ha hálózati hívások jelennek meg, kattintson rájuk a jobb gombbal, és válassza a "Szűrés most" > "Elrejtés" <process name> lehetőséget
4. Első Storage Explorer
5. Reprodukálja a hibát
6. Kattintson a File (Fájl) > Save > All Sessions... (Minden munkamenet mentése) elemre, majd mentsen egy olyan helyet, ahol nem fog megfeledkezni
7. Zárja be a Fiddlert, és Storage Explorer

3. rész: A Fiddler nyomkövetésének sanitizálása
1. Kattintson duplán a Fiddler nyomkövetésére (.saz file)
2. Nyomja meg `ctrl`+`f`
3. A megjelenő párbeszédpanelen győződjön meg arról, hogy be vannak állítva a következő beállítások: Search = Requests and responses, Examine = Headers and bodies
4. Keressen rá a Fiddler nyomkövetésének gyűjtése során használt jelszavakra, a kiemelt bejegyzésekre, kattintson a jobb gombbal, és válassza a Remove > Selected sessions (Kiválasztott munkamenetek eltávolítása) lehetőséget
5. Ha biztosan megadta a jelszavakat a böngészőben a nyomkövetés gyűjtése közben, de nem talál bejegyzéseket a Ctrl+f billentyűkombináció használata közben, és nem szeretné módosítani a jelszavakat/a más fiókokhoz használt jelszavakat, nyugodtan kihagyhatja az .saz fájl küldését. Jobb, ha biztonságban van, mint sajnáljuk. :)
6. Mentse újra a nyomkövetést egy új névvel
7. Nem kötelező: törölje az eredeti nyomkövetést

## <a name="next-steps"></a>Következő lépések

Ha ezek közül egyik megoldás sem működik, a következőt teheti:
- Támogatási jegy létrehozása
- [Nyisson egy problémát a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues) Ezt a bal alsó sarokban található **Report issue to GitHub** (Probléma jelentése a GitHubon) gombra kattintva is meg lehet tenni.

![Visszajelzés](./media/storage-explorer-troubleshooting/feedback-button.PNG)