---
title: Azure Storage Explorer hibaelhárítási útmutató | Microsoft Docs
description: A Azure Storage Explorer hibakeresési módszereinek áttekintése
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 15df9b38abe35fe3eefad2fa160e1c1f16fe7aa7
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439459"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Az Azure Storage Explorer hibaelhárítási útmutatója

A Microsoft Azure Storage Explorer egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszeren. Az alkalmazás az Azure-ban, országos felhőkörnyezetekben és az Azure Stackben üzemeltetett tárfiókokhoz tud csatlakozni.

Ez az útmutató a Storage Explorer gyakran előforduló problémák megoldásait összegzi.

## <a name="azure-rbac-permissions-issues"></a>Az Azure RBAC engedélyeivel kapcsolatos problémák

Az Azure szerepköralapú hozzáférés-vezérlést biztosító [Azure RBAC](../../role-based-access-control/overview.md) lehetővé teszi az Azure-erőforrások nagy részletességű hozzáférés-kezelését azáltal, hogy az engedélyek csoportját a _szerepkörökbe_ ötvözi. Íme néhány stratégia az Azure RBAC optimális működéséhez Storage Explorerban.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hogyan hozzáférni a Storage Explorer erőforrásaihoz?

Ha problémák merülnek fel a tárolási erőforrások Azure RBAC való elérésekor, előfordulhat, hogy nem lett hozzárendelve a megfelelő szerepkörökhöz. A következő szakaszok ismertetik azokat az engedélyeket, Storage Explorer jelenleg a tárolási erőforrásokhoz való hozzáférésre van szükség. Ha nem biztos benne, hogy rendelkezik a megfelelő szerepkörökkel vagy engedélyekkel, forduljon az Azure-fiók rendszergazdájához.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Olvasási: a Storage-fiók (ok) engedélyeinek listázása/beolvasása" engedélyekkel kapcsolatos probléma

Rendelkeznie kell engedéllyel a Storage-fiókok listázásához. Az engedély beszerzéséhez hozzá kell rendelnie az _olvasó_ szerepkört.

#### <a name="list-storage-account-keys"></a>Storage-fiók kulcsainak listázása

A Storage Explorer a kérelmek hitelesítéséhez is használhatja a fiók kulcsait. A fiók kulcsaihoz nagyobb teljesítményű szerepkörök, például a _közreműködő_ szerepkör használatával férhet hozzá.

> [!NOTE]
> A hozzáférési kulcsok nem korlátozott engedélyeket biztosítanak mindazoknak, akik tárolják azokat. Ezért nem javasoljuk, hogy ezeket a kulcsokat a fiókok felhasználói számára kiadja. Ha vissza kell vonnia a hozzáférési kulcsokat, újragenerálhatja őket a [Azure Portalból](https://portal.azure.com/).

#### <a name="data-roles"></a>Adatszerepkörök

Legalább egy olyan szerepkört hozzá kell rendelnie, amely hozzáférést biztosít az erőforrásokból származó adatok olvasásához. Ha például blobokat szeretne listázni vagy letölteni, akkor legalább a _Storage blob Adatolvasó_ szerepkörre lesz szüksége.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Miért van szükség felügyeleti rétegbeli szerepkörre az erőforrások Storage Explorer való megtekintéséhez?

Az Azure Storage két hozzáférési réteggel rendelkezik: a felügyelethez és _az_ _adatkezeléshez_ . Az előfizetések és a Storage-fiókok a felügyeleti rétegen keresztül érhetők el. A tárolók, a blobok és az egyéb adatforrások az adatrétegen keresztül érhetők el. Ha például szeretné lekérni a Storage-fiókjainak listáját az Azure-ból, küldjön egy kérelmet a felügyeleti végpontnak. Ha egy fiókban szeretné megtekinteni a blob-tárolók listáját, egy kérést küld a megfelelő szolgáltatási végpontnak.

Az Azure-szerepkörök engedélyeket biztosíthatnak a felügyelethez vagy az adatréteghez való hozzáféréshez. Az olvasó szerepkör például csak olvasási hozzáférést biztosít a felügyeleti réteg erőforrásaihoz.

Szigorúan véve az olvasó szerepkör nem biztosít adatrétegbeli engedélyeket, és nem szükséges az adatréteghez való hozzáféréshez.

A Storage Explorer megkönnyíti az erőforrások elérését az Azure-erőforrásokhoz való kapcsolódáshoz szükséges információk összegyűjtésével. A blob-tárolók megjelenítéséhez például Storage Explorer a "tárolók listázása" kérelmet küld a blob Service-végpontnak. A végpont beszerzéséhez Storage Explorer megkeresi azon előfizetések és Storage-fiókok listáját, amelyekhez hozzáférése van. Az előfizetések és a Storage-fiókok megkereséséhez Storage Explorer is hozzá kell férnie a felügyeleti réteghez.

Ha nincs olyan szerepköre, amely felügyeleti rétegbeli engedélyeket biztosít, Storage Explorer nem tudja lekérni az adatréteghez való kapcsolódáshoz szükséges adatokat.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Mi a teendő, ha nem tudom beolvasni a felügyeleti réteghez szükséges engedélyeket a rendszergazdától?

Ha a blob-tárolókat, ADLS Gen2 tárolókat vagy címtárakat vagy várólistákat szeretné elérni, az Azure-beli hitelesítő adataival csatolhatja ezeket az erőforrásokat.

1. Nyissa meg a csatlakozási párbeszédpanelt.
1. Válassza ki azt az erőforrás-típust, amelyhez csatlakozni szeretne.
1. Válassza **a bejelentkezés Azure Active Directory (Azure ad) használatával** lehetőséget. Kattintson a **Tovább** gombra.
1. Válassza ki azt a felhasználói fiókot és bérlőt, amelyhez hozzá kívánja rendelni az erőforrást. Kattintson a **Tovább** gombra.
1. Adja meg az erőforrás URL-címét, és adjon meg egy egyedi megjelenítendő nevet a kapcsolódáshoz. Válassza a **tovább** , majd a **kapcsolat** lehetőséget.

Más erőforrástípusok esetében jelenleg nem áll rendelkezésre Azure RBAC kapcsolatos megoldás. Megkerülő megoldásként igényelhet SAS URL-címet, majd csatolja az erőforráshoz a következő lépések végrehajtásával:

1. Nyissa meg a csatlakozási párbeszédpanelt.
1. Válassza ki azt az erőforrás-típust, amelyhez csatlakozni szeretne.
1. Válassza a **közös hozzáférési aláírás (SAS)** lehetőséget. Kattintson a **Tovább** gombra.
1. Adja meg a kapott SAS URL-címet, és adjon meg egy egyedi megjelenítendő nevet a kapcsolatok számára. Válassza a **tovább** , majd a **kapcsolat** lehetőséget.
 
További információ az erőforrásokhoz való csatolásról: [csatolás egy adott erőforráshoz](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Ajánlott Azure beépített szerepkörök

Több Azure-beli beépített szerepkör is rendelkezésre áll, amelyek biztosítják a Storage Explorer használatához szükséges engedélyeket. A szerepkörök némelyike:
- [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner): mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is.
- [Közreműködő](../../role-based-access-control/built-in-roles.md#contributor): mindent kezelhet, kivéve az erőforrásokhoz való hozzáférést.
- [Olvasó](../../role-based-access-control/built-in-roles.md#reader): erőforrások olvasása és listázása.
- [Storage-fiók közreműködői](../../role-based-access-control/built-in-roles.md#storage-account-contributor): teljes körű felügyelet a Storage-fiókok esetében.
- [Storage blob-adat tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): teljes hozzáférés az Azure Storage blob-tárolók és-adattárakhoz.
- [Storage blob-adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): az Azure Storage-tárolók és-Blobok olvasása, írása és törlése.
- [Storage blob Adatolvasó](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): az Azure Storage-tárolók és-Blobok olvasása és listázása.

> [!NOTE]
> A tulajdonos, a közreműködő és a Storage-fiók közreműködői szerepkörei biztosítják a fiók kulcsának elérését.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hiba: önaláírt tanúsítvány a tanúsítványláncot (és hasonló hibák)

A tanúsítványok hibái általában az alábbi helyzetekben fordulnak elő:

- Az alkalmazás egy _transzparens proxyn_ keresztül csatlakozik. Ez azt jelenti, hogy egy kiszolgáló (például a vállalati kiszolgáló) elfogja a HTTPS-forgalmat, visszafejti, majd egy önaláírt tanúsítvány használatával titkosítja.
- Olyan alkalmazást futtat, amely egy önaláírt TLS/SSL-tanúsítványt szúr be a kapott HTTPS-üzenetbe. A tanúsítványokat beinjektáló alkalmazások például a víruskereső és a hálózati forgalom-ellenőrzési szoftver.

Ha Storage Explorer egy önaláírt vagy nem megbízható tanúsítványt lát, már nem tudja, hogy megváltozott-e a kapott HTTPS-üzenet. Ha rendelkezik az önaláírt tanúsítvány egy példányával, akkor a következő lépések végrehajtásával utasíthatja Storage Explorer megbízni:

1. Szerezze be a tanúsítvány Base-64 kódolású X. 509 (. cer) másolatát.
2. Nyissa **meg az**  >  **SSL-tanúsítványok**  >  **importálása tanúsítványokat**, majd a file Picker használatával keresse meg, válassza ki és nyissa meg a. cer fájlt.

Ez a probléma akkor is előfordulhat, ha több tanúsítvány van (root és Intermediate). A hiba kijavításához mindkét tanúsítványt hozzá kell adni.

Ha nem biztos abban, hogy honnan származik a tanúsítvány, kövesse az alábbi lépéseket a kereséséhez:

1. Telepítse az OpenSSL-t.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): a fényváltozatok bármelyikének elegendőnek kell lennie.
    * Mac és Linux: az operációs rendszer részét képezi.
2. Futtassa az OpenSSL-t.
    * Windows: Nyissa meg a telepítési könyvtárat, válassza a **/bin/** lehetőséget, majd kattintson duplán a **openssl.exe** elemre.
    * Mac és Linux: Futtatás `openssl` terminálról.
3. Futtassa a `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha nem biztos abban, hogy mely tanúsítványok önaláírtak, jegyezze fel a tárgyat és a `("s:")` kiállítót bárhol `("i:")` .
5. Ha önaláírt tanúsítványokat talál, mindegyikhez másolja ki és illessze be a (és a (többek között) `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` új. cer kiterjesztésű fájlba.
6. Nyissa meg Storage Explorer, és **kattintson az**  >  **SSL-tanúsítványok**  >  **importálása tanúsítványok importálása** gombra. Ezután a file Picker használatával megkeresheti, kiválaszthatja és megnyithatja a létrehozott. cer fájlokat.

Ha a fenti lépések követésével nem talál önaláírt tanúsítványokat, lépjen kapcsolatba velünk a visszajelzési eszköz használatával. Storage Explorer a parancssorból is megnyithatja a `--ignore-certificate-errors` jelző használatával. Ezzel a jelzővel megnyitva Storage Explorer figyelmen kívül hagyja a tanúsítvány hibáit.

## <a name="sign-in-issues"></a>Bejelentkezési problémák

### <a name="blank-sign-in-dialog-box"></a>Üres bejelentkezési párbeszédpanel

Az üres bejelentkezési párbeszédpanelek leggyakrabban akkor fordulnak elő, ha a Active Directory összevonási szolgáltatások (AD FS) (AD FS) az elektron által nem támogatott átirányítás elvégzésére kéri Storage Explorer. A probléma megkerüléséhez próbálja meg a bejelentkezéshez használni az eszköz kódjának folyamatát. Ehhez kövesse az alábbi lépéseket:

1. A bal oldali függőleges eszköztáron nyissa meg a **Beállítások menüpontot**. A beállítások panelen lépjen az alkalmazás-   >  **Bejelentkezés** elemre. Engedélyezze **az eszköz kódjának áramlását a bejelentkezést**.
2. Nyissa meg a **Csatlakoztatás** párbeszédpanelt (vagy a bal oldali függőleges sávban a dugó ikonra kattintva vagy a fiók **hozzáadása** a fiók paneljén).
3. Válassza ki azt a környezetet, amelyre be szeretné jelentkezni.
4. Válassza **a bejelentkezés** lehetőséget.
5. Kövesse a következő panelen megjelenő utasításokat.

Ha nem tud bejelentkezni a használni kívánt fiókba, mert az alapértelmezett böngésző már be van jelentkezve egy másik fiókba, tegye a következők egyikét:

- Manuálisan másolja a hivatkozást és a kódot a böngésző privát munkamenetére.
- Manuálisan másolja a hivatkozást és a kódot egy másik böngészőbe.

### <a name="reauthentication-loop-or-upn-change"></a>Újrahitelesítési hurok vagy UPN-változás

Ha újrahitelesítési hurkot használ, vagy módosította valamelyik fiók UPN-azonosítóját, kövesse az alábbi lépéseket:

1. Távolítsa el az összes fiókot, majd zárjunk be Storage Explorer.
2. Törölje a t. IdentityService mappát a számítógépről. Windows rendszeren a mappa a következő helyen található: `C:\users\<username>\AppData\Local` . Mac és Linux rendszereken a felhasználói könyvtár gyökerében található mappát találja.
3. Ha Mac vagy Linux rendszert futtat, akkor törölnie kell a Microsoft. Developer. IdentityService bejegyzést az operációs rendszer főtárolójából is. A Mac gépen a-tároló a *GNOME kulcstartó* alkalmazás. A Linux rendszerben az alkalmazást általában _kulcstartónak_ nevezik, de a név a disztribúciótól függően eltérő lehet.

### <a name="conditional-access"></a>Feltételes hozzáférés

Az Storage Explorer által használt Azure AD-könyvtár korlátozásai miatt a feltételes hozzáférés nem támogatott, ha a Windows 10, a Linux vagy a macOS rendszeren Storage Explorer használatban van.

## <a name="mac-keychain-errors"></a>Mac kulcstartó hibák

A macOS-kulcstartó időnként olyan állapotot is megadhat, amely problémákat okoz a Storage Explorer hitelesítési függvénytárban. A kulcstartónak az állapotból való lekéréséhez kövesse az alábbi lépéseket:

1. Storage Explorer bezárásához.
2. Nyissa meg a kulcstartót (nyomja le a Command + szóköz billentyűkombinációt, írja be a **kulcstartó** parancsot, majd nyomja le az ENTER billentyűt
3. Válassza ki a "login" kulcstartót.
4. Válassza a lakat ikont a kulcstartó zárolásához. (A lakat a folyamat befejeződése után zárolva jelenik meg. Eltarthat néhány másodpercig, attól függően, hogy milyen alkalmazásokat nyitott meg).

    ![Lakat ikon](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Nyissa meg a Storage Explorert.
6. Egy üzenet jelenik meg, például: "a Service hub szeretné elérni a kulcstartót." Adja meg a Mac-rendszergazdai fiók jelszavát, és válassza a **mindig az engedélyezés** lehetőséget (vagy **engedélyezze** , hogy a **mindig** engedélyezve legyen) lehetőséget.
7. Próbáljon meg bejelentkezni.

### <a name="general-sign-in-troubleshooting-steps"></a>Általános bejelentkezési hibaelhárítási lépések

* Ha macOS-en van, és a bejelentkezési ablak soha nem jelenik meg a **hitelesítésre vár** párbeszédpanelen, próbálja meg [ezeket a lépéseket](#mac-keychain-errors).
* Storage Explorer újraindítása.
* Ha a hitelesítési ablak üres, várjon legalább egy percet, mielőtt bezárja a hitelesítés párbeszédpanelt.
* Győződjön meg arról, hogy a proxy és a tanúsítvány beállításai megfelelően vannak konfigurálva a gép és a Storage Explorer számára.
* Ha Windows rendszert futtat, és hozzáfér a Visual Studio 2019-hoz ugyanazon a gépen és a bejelentkezési hitelesítő adatokhoz, próbáljon meg bejelentkezni a Visual Studio 2019ba. Miután sikeresen bejelentkezett a Visual Studio 2019-be, megnyithatja Storage Explorer és megtekintheti a fiókját a fiók panelen.

Ha egyik módszer sem működik, [Nyisson meg egy problémát a githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Hiányzó előfizetések és megszakított bérlők

Ha a sikeres bejelentkezést követően nem tudja lekérni az előfizetéseket, próbálkozzon a következő hibaelhárítási módszerekkel:

* Ellenőrizze, hogy a fiókja rendelkezik-e hozzáféréssel a várt előfizetésekhez. A hozzáférést a használni kívánt Azure-környezethez való bejelentkezéssel ellenőrizheti a portálon.
* Győződjön meg arról, hogy a megfelelő Azure-környezetben (Azure, Azure China 21Vianet, Azure Germany, Azure USA Government vagy egyéni környezet) keresztül jelentkezett be.
* Ha a proxykiszolgáló mögött van, ellenőrizze, hogy helyesen konfigurálta-e a Storage Explorer proxyt.
* Próbálja meg eltávolítani és újból hozzáadni a fiókot.
* Ha van "További információ" hivatkozás, ellenőrizze, hogy a rendszer milyen hibaüzeneteket jelentett a sikertelen bérlők számára. Ha nem tudja, hogyan válaszoljon a hibaüzenetekre, nyugodtan [Nyisson meg egy problémát a githubon](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Csatolt fiók vagy tárolási erőforrás nem távolítható el

Ha nem távolíthat el egy csatolt fiókot vagy tárolási erőforrást a felhasználói felületen, manuálisan törölheti az összes csatolt erőforrást a következő mappák törlésével:

* Windows: `%AppData%/StorageExplorer`
* MacOS `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> A mappák törlése előtt Storage Explorer bezárásához.

> [!NOTE]
> Ha valaha is importált SSL-tanúsítványokat, biztonsági mentést készíthet a könyvtár tartalmáról `certs` . Később a biztonsági mentés használatával újraimportálhatja az SSL-tanúsítványokat.

## <a name="proxy-issues"></a>Proxyval kapcsolatos problémák

Storage Explorer támogatja az Azure Storage-erőforrásokhoz való csatlakozást egy proxykiszolgálón keresztül. Ha az Azure-hoz proxyn keresztüli csatlakozással kapcsolatos problémákat tapasztal, itt talál néhány javaslatot.

> [!NOTE]
> Storage Explorer csak a proxykiszolgálók alapszintű hitelesítését támogatja. Más hitelesítési módszerek, például az NTLM, nem támogatottak.

> [!NOTE]
> A Storage Explorer nem támogatja a proxy automatikus konfigurációs fájljait a proxybeállítások konfigurálásához.

### <a name="verify-storage-explorer-proxy-settings"></a>Storage Explorer proxybeállítások ellenőrzése

Az **alkalmazás → Proxy → proxy konfigurációs** beállítás határozza meg, hogy melyik forrás Storage Explorer szerzi be a proxy konfigurációját.

Ha a "környezeti változók használata" lehetőséget választja, ügyeljen a `HTTPS_PROXY` vagy `HTTP_PROXY` környezeti változók beállítására (a környezeti változók megkülönböztetik a kis-és nagybetűket, ezért ügyeljen arra, hogy a helyes változókat állítsa be). Ha a változók nincsenek meghatározva vagy érvénytelenek, Storage Explorer nem használ proxyt. A környezeti változók módosítása után indítsa újra Storage Explorer.

Ha az "alkalmazás-proxybeállítások használata" lehetőséget választja, ellenőrizze, hogy helyesek-e az alkalmazáson belüli proxy beállításai.

### <a name="steps-for-diagnosing-issues"></a>A problémák diagnosztizálásának lépései

Ha továbbra is problémákat tapasztal, próbálkozzon a következő hibaelhárítási módszerekkel:

1. Ha a proxy használata nélkül tud csatlakozni az internethez, győződjön meg arról, hogy a Storage Explorer a proxybeállítások engedélyezése nélkül működik. Ha Storage Explorer sikeresen csatlakozik, lehet, hogy probléma van a proxykiszolgálóhoz. A problémák azonosításához működjön együtt a rendszergazdájával.
2. Ellenőrizze, hogy a proxykiszolgálót használó más alkalmazások a várt módon működnek-e.
3. Ellenőrizze, hogy tud-e csatlakozni a használni kívánt Azure-környezethez a portálhoz.
4. Ellenőrizze, hogy kaphat-e válaszokat a szolgáltatási végpontokról. Adja meg az egyik végpont URL-címét a böngészőben. Ha csatlakozni tud, egy `InvalidQueryParameterValue` vagy hasonló XML-választ kell kapnia.
5. Győződjön meg arról, hogy valaki más használja-e a Storage Explorert ugyanazzal a proxykiszolgálón keresztül. Ha lehetséges, kapcsolatba kell lépnie a proxykiszolgáló rendszergazdájával.

### <a name="tools-for-diagnosing-issues"></a>Problémák diagnosztizálására szolgáló eszközök

A hálózati eszközök, például a Hegedűs, segíthetnek a problémák diagnosztizálásában.

1. Konfigurálja a hálózati eszközt a helyi gazdagépen futó proxykiszolgálóként. Ha továbbra is a tényleges proxy mögött kell dolgoznia, előfordulhat, hogy konfigurálnia kell a hálózati eszközt a proxyn keresztüli csatlakozáshoz.
2. Keresse meg a hálózati eszköz által használt portszámot.
3. Konfigurálja Storage Explorer proxybeállításokat a helyi gazdagép és a hálózati eszköz portszámának (például "localhost: 8888") használatára.
 
Ha helyesen van beállítva, a hálózati eszköz naplózza Storage Explorer által a felügyeleti és a szolgáltatási végpontoknak küldött hálózati kérelmeket.
 
Ha a hálózatkezelési eszköz nem úgy tűnik, hogy naplózza Storage Explorer forgalmat, próbálja meg tesztelni az eszközt egy másik alkalmazással. Adja meg például az egyik tároló-erőforrás végpontjának URL-címét (például `https://contoso.blob.core.windows.net/` ) egy webböngészőben, és a következőhöz hasonló választ kap:

  ![Kódminta](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  A válasz azt sugallja, hogy az erőforrás létezik, még akkor is, ha nem fér hozzá.

Ha a hálózati eszköz csak más alkalmazásokból származó forgalmat mutat, előfordulhat, hogy módosítania kell a proxybeállításokat a Storage Explorerban. Ellenkező esetben módosítania kell az eszköz beállításait.

### <a name="contact-proxy-server-admin"></a>Kapcsolatfelvétel a proxykiszolgáló rendszergazdájával

Ha a proxybeállítások helyesek, előfordulhat, hogy a következőkre kell felvennie a kapcsolatot a proxykiszolgáló rendszergazdájával:

* Győződjön meg arról, hogy a proxy nem blokkolja az Azure-felügyeletre vagy az erőforrás-végpontokra irányuló forgalmat.
* Ellenőrizze a proxykiszolgáló által használt hitelesítési protokollt. Storage Explorer csak az alapszintű hitelesítési protokollok használatát támogatja. A Storage Explorer nem támogatja az NTLM-proxykat.

## <a name="unable-to-retrieve-children-error-message"></a>"Nem sikerült beolvasni a gyermekeket" hibaüzenet jelenik meg

Ha egy proxyn keresztül csatlakozik az Azure-hoz, ellenőrizze, hogy helyesek-e a proxybeállítások.

Ha egy előfizetés vagy fiók tulajdonosa hozzáférést kapott egy erőforráshoz, ellenőrizze, hogy rendelkezik-e az adott erőforráshoz tartozó olvasási vagy listázási engedélyekkel.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A kapcsolatok karakterlánca nem rendelkezik teljes konfigurációs beállításokkal

Ha ez a hibaüzenet jelenik meg, lehetséges, hogy nem rendelkezik a szükséges engedélyekkel a Storage-fiók kulcsainak beszerzéséhez. Ha ellenőrizni szeretné, hogy ez a helyzet, lépjen a portálra, és keresse meg a Storage-fiókját. Ezt úgy teheti meg, hogy a jobb gombbal a Storage-fiók csomópontjára kattint, és kiválasztja **a Megnyitás a portálon** lehetőséget. Ezután nyissa meg a **hozzáférési kulcsok** panelt. Ha nem rendelkezik a kulcsok megtekintéséhez szükséges engedélyekkel, a "nincs hozzáférése" üzenet jelenik meg. A probléma megkerüléséhez megszerezheti a fiók kulcsát valaki mástól, és csatolhatja a nevet és a kulcsot, vagy megkérheti a SAS-t a Storage-fiókhoz, és használhatja azt a Storage-fiók csatolásához.

Ha látja a fiók kulcsait, akkor a GitHubon a probléma megoldásához segítséget nyújthat.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Hiba történt az új kapcsolódás hozzáadásakor: TypeError: nem olvasható a (z) nem definiált "version" tulajdonság.

Ha ezt a hibaüzenetet kapja, amikor egyéni csatlakozást próbál hozzáadni, előfordulhat, hogy a helyi hitelesítőadat-kezelőben tárolt kapcsolódási adatok sérültek. A probléma megkerüléséhez próbálkozzon a sérült helyi kapcsolatok törlésével, majd adja hozzá újra a következőt:

1. Storage Explorer elindítása. A menüből válassza a **Súgó**  >  **váltógomb fejlesztői eszközök** lehetőséget.
2. A megnyitott ablak **alkalmazás** lapján lépjen a **helyi tároló** (bal oldalon) > **file://** elemre.
3. Attól függően, hogy milyen típusú kapcsolatra van probléma, keresse meg a kulcsát, majd másolja az értékét egy szövegszerkesztőbe. Az érték az egyéni nevek tömbje, a következőhöz hasonlóan:
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
4. Az aktuális kapcsolatok nevének mentése után állítsa be Fejlesztői eszközök értékét a következőre: `[]` .

Ha meg szeretné őrizni a nem sérült kapcsolatokat, a következő lépésekkel megkeresheti a sérült kapcsolatokat. Ha nem veszik figyelembe az összes meglévő kapcsolat elvesztését, kihagyhatja ezeket a lépéseket, és követheti a platformra vonatkozó utasításokat a kapcsolati adatai törléséhez.

1. Egy szövegszerkesztőben adja hozzá újra az egyes kapcsolatok nevét a Fejlesztői eszközökhoz, majd győződjön meg arról, hogy a kapcsolat továbbra is működik-e.
2. Ha a kapcsolat megfelelően működik, nem sérült, és nyugodtan elhagyhatja. Ha egy kapcsolat nem működik, távolítsa el az értékét Fejlesztői eszközökból, és jegyezze fel, hogy később újra hozzá lehessen adni.
3. Ismételje meg az összes kapcsolat vizsgálatát.

Miután az összes kapcsolaton áthaladt, az összes nem hozzáadott kapcsolat nevében törölje a sérült adataikat (ha van ilyen), és adja hozzá őket a Storage Explorer alábbi szabványos lépéseivel:

# <a name="windows"></a>[Windows](#tab/Windows)

1. A **Start** menüben keresse meg a **hitelesítőadat-kezelőt** , és nyissa meg.
2. Nyissa meg a **Windows rendszerbeli hitelesítő adatokat**.
3. Az **általános hitelesítő adatok** területen keresse meg a kulcsot tartalmazó bejegyzéseket `<connection_type_key>/<corrupted_connection_name>` (például: `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Törölje ezeket a bejegyzéseket, majd adja hozzá újra a kapcsolatokat.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Nyisson meg egy reflektorfényt (Command + szóköz), és keressen rá a **kulcstartó-hozzáférés** kifejezésre.
2. Keresse meg a kulcsot tartalmazó bejegyzéseket `<connection_type_key>/<corrupted_connection_name>` (például: `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Törölje ezeket a bejegyzéseket, majd adja hozzá újra a kapcsolatokat.

# <a name="linux"></a>[Linux](#tab/Linux)

A helyi hitelesítő adatok kezelése a Linux-disztribúciótól függően változhat. Ha a Linux-disztribúció nem biztosít beépített GUI-eszközt a helyi hitelesítő adatok kezeléséhez, telepíthet egy külső gyártótól származó eszközt a helyi hitelesítő adatok kezeléséhez. Használhatja például a [csikóhalat](https://wiki.gnome.org/Apps/Seahorse/), amely egy nyílt forráskódú GUI-eszköz a Linux helyi hitelesítő adatainak kezeléséhez.

1. Nyissa meg a helyi Hitelesítőadat-kezelő eszközt, és keresse meg a mentett hitelesítő adatokat.
2. Keresse meg a kulcsot tartalmazó bejegyzéseket `<connection_type_key>/<corrupted_connection_name>` (például: `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Törölje ezeket a bejegyzéseket, majd adja hozzá újra a kapcsolatokat.
---

Ha a lépések futtatása után továbbra is ezt a hibát tapasztalja, vagy ha meg szeretné osztani, hogy mi sérült a kapcsolat, [Nyisson meg egy problémát](https://github.com/microsoft/AzureStorageExplorer/issues) a GitHub oldalán.

## <a name="issues-with-sas-url"></a>Az SAS URL-címmel kapcsolatos problémák

Ha SAS URL-címen keresztül csatlakozik egy szolgáltatáshoz, és hibát észlelt:

* Ellenőrizze, hogy az URL-cím tartalmazza-e a szükséges engedélyeket az erőforrások olvasásához vagy listázásához.
* Ellenőrizze, hogy az URL-cím nem járt-e le.
* Ha a SAS URL-cím egy hozzáférési házirenden alapul, ellenőrizze, hogy a hozzáférési házirendet nem vonták-e vissza.

Ha véletlenül csatolva van egy érvénytelen SAS URL-cím használatával, és most nem tud leválasztani, kövesse az alábbi lépéseket:

1. Storage Explorer futtatásakor nyomja meg az F12 billentyűt a Fejlesztői eszközök ablak megnyitásához.
2. Az **alkalmazás** lapon válassza a **helyi tároló**  >  **file://** a bal oldali fában.
3. Keresse meg a problémás SAS URI-azonosító szolgáltatás típusához társított kulcsot. Ha például a hibás SAS URI egy blob-tárolóhoz tartozik, keresse meg a nevű kulcsot `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. A kulcs értékének JSON-tömbnek kell lennie. Keresse meg a rossz URI-hoz társított objektumot, majd törölje.
5. Nyomja le a CTRL + R billentyűkombinációt Storage Explorer újratöltéséhez.

## <a name="linux-dependencies"></a>Linux-függőségek

### <a name="snap"></a>Beépülő modul

Storage Explorer 1.10.0 és újabb verzió is elérhető a Snap áruházban. A Storage Explorer beépülő modul automatikusan telepíti az összes függőségét, és frissül, ha a beépülő modul új verziója érhető el. Az Storage Explorer beépülő modul telepítése a javasolt telepítési módszer.

Storage Explorer szükség van egy Password Manager használatára, amelyhez szükség lehet a manuális kapcsolódásra, mielőtt Storage Explorer megfelelően működni fognak. A következő parancs futtatásával csatlakozhat Storage Explorer a rendszer jelszavas kezelőjéhez:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>. tar. gz fájl

Az alkalmazást. tar. gz fájlként is letöltheti, de a függőségeket manuálisan kell telepítenie.

A. tar. gz letöltésben megadott Storage Explorer csak az Ubuntu következő verzióiban támogatott. A Storage Explorer más Linux-disztribúciókban is működhet, de hivatalosan nem támogatottak.

- Ubuntu 20,04 x64
- Ubuntu 18,04 x64
- Ubuntu 16,04 x64

Storage Explorer szükséges, hogy a .NET Core telepítve legyen a rendszeren. Javasoljuk, hogy a .NET Core 2,1-as, de a Storage Explorer a 2,2-es is működjön.

> [!NOTE]
> Storage Explorer 1.7.0 és korábbi verziókhoz a .NET Core 2,0 szükséges. Ha a .NET Core újabb verziója van telepítve, akkor a [Storage Explorer javításra](#patching-storage-explorer-for-newer-versions-of-net-core)van szükség. Ha Storage Explorer 1.8.0-t vagy újabb verziót futtat, legalább a .NET Core 2,1-es verzióra van szüksége.

# <a name="ubuntu-2004"></a>[Ubuntu 20,04](#tab/2004)

1. Töltse le a Storage Explorer. tar. gz fájlt.
2. Telepítse a [.net Core-futtatókörnyezetet](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Töltse le a Storage Explorer. tar. gz fájlt.
2. Telepítse a [.net Core-futtatókörnyezetet](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Töltse le a Storage Explorer. tar. gz fájlt.
2. Telepítse a [.net Core-futtatókörnyezetet](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Storage Explorer számos olyan kódtárat is megadtak, amelyekre szükség van a Canonical szabványos Ubuntu-telepítésekor. Előfordulhat, hogy az egyéni környezetek hiányoznak ezekből a tárakból. Ha problémába ütközik a Storage Explorer indításakor, javasoljuk, hogy a következő csomagokat telepítse a rendszerre:

- iproute2
- libasound2
- libatm1
- libgconf2 – 4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- XDG – utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Javítás Storage Explorer a .NET Core újabb verzióihoz

Storage Explorer 1.7.0 vagy korábbi verzió esetén előfordulhat, hogy a Storage Explorer által használt .NET Core-verziót kell megjavítania:

1. Töltse le a StreamJsonRpc 1.5.43 verzióját a [NuGet-](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)ból. Keresse meg a "csomag letöltése" hivatkozást az oldal jobb oldalán.
2. A csomag letöltése után módosítsa a fájl kiterjesztését a verzióról a verzióra `.nupkg` `.zip` .
3. Bontsa ki a csomagot.
4. Nyissa meg a `streamjsonrpc.1.5.43/lib/netstandard1.1/` mappát.
5. Másolja a `StreamJsonRpc.dll` következő helyszínekre a Storage Explorer mappában:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Az Azure Portal nem működik az "Open in Explorer"

Ha a Azure Portal **Megnyitás a Explorerben** gomb nem működik, győződjön meg róla, hogy kompatibilis böngészőt használ. A következő böngészők lettek tesztelve a kompatibilitás érdekében:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Következő lépések

Ha egyik megoldás sem működik, [Nyisson meg egy problémát a githubon](https://github.com/Microsoft/AzureStorageExplorer/issues). Ezt úgy is megteheti, hogy kijelöli a **jelentéssel kapcsolatos problémát a GitHub** gombra a bal alsó sarokban.

![Visszajelzés](./media/storage-explorer-troubleshooting/feedback-button.PNG)