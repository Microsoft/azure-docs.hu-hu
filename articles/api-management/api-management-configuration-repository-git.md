---
title: A API Management szolgáltatás konfigurálása a git használatával – Azure | Microsoft Docs
description: Megtudhatja, hogyan mentheti és konfigurálhatja a API Management szolgáltatás konfigurációját a git használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89400355"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Az API Management-szolgáltatáskonfiguráció mentése és konfigurálása a Git használatával

Minden API Management szolgáltatási példány egy olyan konfigurációs adatbázist tart fenn, amely a szolgáltatás példányának konfigurációjával és metaadataival kapcsolatos információkat tartalmaz. A szolgáltatási példány módosításait úgy végezheti el, ha módosítja egy beállítást a Azure Portal egy PowerShell-parancsmag használatával, vagy egy REST API hívást végez. Ezen módszerek mellett a git használatával is kezelheti a szolgáltatás példányának konfigurációját, így a szolgáltatás-felügyeleti forgatókönyvek, például a következők lehetnek:

* Konfiguráció verziószámozása – a szolgáltatás konfigurációjának különböző verzióinak letöltése és tárolása
* Tömeges konfigurációs változások – a szolgáltatás konfigurációjának több részén módosítható a helyi tárházban, és a módosításokat egyetlen művelettel integrálja a kiszolgálóra.
* Ismerős git-toolchain és-munkafolyamat – a már ismert git-eszközök és-munkafolyamatok használata

A következő ábra áttekintést nyújt az API Management-szolgáltatási példány konfigurálásának különböző módjairól.

![Git-konfigurálás][api-management-git-configure]

Ha a Azure Portal, a PowerShell-parancsmagok vagy a REST API használatával módosítja a szolgáltatást, a szolgáltatás konfigurációs adatbázisát a végpont használatával felügyeli `https://{name}.management.azure-api.net` , ahogy az a diagram jobb oldalán látható. A diagram bal oldalán látható, hogyan kezelheti a szolgáltatás konfigurációját a git-és git-tárház használatával, amely a szolgáltatásában található `https://{name}.scm.azure-api.net` .

Az alábbi lépések áttekintést nyújtanak a API Management Service-példánynak a git használatával történő kezeléséről.

1. A git-konfiguráció elérése a szolgáltatásban
2. A szolgáltatás konfigurációs adatbázisának mentése a git-tárházba
3. A git-tárház klónozása a helyi gépre
4. A legfrissebb tárház lekérése a helyi gépre, és a módosítások visszaküldése a tárházba
5. A tárház változásainak üzembe helyezése a szolgáltatás konfigurációs adatbázisába

Ez a cikk bemutatja, hogyan engedélyezheti és használhatja a git szolgáltatást a szolgáltatás konfigurációjának kezeléséhez, és a git-tárházban található fájlokra és mappákra mutató hivatkozást biztosít.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>A git-konfiguráció elérése a szolgáltatásban

A git konfigurációs beállításainak megtekintéséhez és konfigurálásához kattintson a **központi telepítés és infrastruktúra** menüre, és navigáljon az **adattár** lapra.

![GIT engedélyezése][api-management-enable-git]

> [!IMPORTANT]
> Minden olyan titkot, amely névvel ellátott értékként van definiálva, a tárházban tárolódik, és a korábbi előzményekben marad, amíg le nem tiltja és újra engedélyezni szeretné a git-hozzáférést. A nevesített értékek biztonságos helyet biztosítanak a konstans karakterlánc-értékek (például titkok) kezelésére az összes API-konfigurációban és-házirendben, így nem kell azokat közvetlenül a házirend-utasításokban tárolnia. További információ: [nevesített értékek használata az Azure API Management-házirendekben](api-management-howto-properties.md).
>
>

A git-hozzáférés a REST API használatával történő engedélyezésével vagy letiltásával kapcsolatos információkért lásd [a git-hozzáférés engedélyezése vagy letiltása a REST API használatával](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit)című témakört.

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>A szolgáltatás konfigurációjának mentése a git-tárházba

A tárház klónozása előtt az első lépés a szolgáltatás konfigurációjának aktuális állapotának mentése a tárházba. Kattintson **a Mentés a tárházba** lehetőségre.

Végezze el a kívánt módosításokat a megerősítő képernyőn, majd kattintson a **Save (Mentés** ) gombra a mentéshez.

Néhány pillanat elteltével a rendszer menti a konfigurációt, és megjeleníti a tárház konfigurációs állapotát, beleértve az utolsó konfigurációs módosítás dátumát és időpontját, valamint a szolgáltatás konfigurációja és az adattár közötti utolsó szinkronizálást.

Miután a rendszer mentette a konfigurációt a tárházba, klónozott lehet.

A műveletnek a REST API használatával történő végrehajtásával kapcsolatos információkért lásd: konfigurációs pillanatkép elvégzése [a REST API használatával](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>A tárház klónozása a helyi gépre

A tárház klónozásához szüksége lesz a tárház, a Felhasználónév és a jelszó URL-címére. A Felhasználónév és az egyéb hitelesítő adatok lekéréséhez kattintson az oldal tetején található **hozzáférési hitelesítő adatok** elemre.

A jelszó létrehozásához először ellenőrizze, hogy a **lejárat** a kívánt lejárati dátumra és időpontra van-e állítva, majd kattintson a **Létrehozás** gombra.

> [!IMPORTANT]
> Jegyezze fel ezt a jelszót. Ha elhagyja ezt a lapot, a jelszó nem jelenik meg újra.
>

Az alábbi példák a git bash eszközt használják a [Windowsban](https://www.git-scm.com/downloads) , de használhatja a már ismert git-eszközöket is.

Nyissa meg a git eszközt a kívánt mappában, és futtassa a következő parancsot a git-tárháznak a helyi gépre történő klónozásához a Azure Portal által megadott parancs használatával.

```
git clone https://{name}.scm.azure-api.net/
```

Ha a rendszer kéri, adja meg a felhasználónevet és a jelszót.

Ha hibaüzenetet kap, próbálja meg módosítani a `git clone` parancsot úgy, hogy az tartalmazza a felhasználónevet és a jelszót, ahogyan az a következő példában látható.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Ha ez hibát jelez, próbálja meg az URL-címet kódolni a parancs jelszó részében. Ennek egyik gyors módja a Visual Studio megnyitása, és a következő parancs kiadása a **parancsablakban**. Az **azonnali ablak** megnyitásához nyisson meg bármilyen megoldást vagy projektet a Visual Studióban (vagy hozzon létre egy új üres konzol alkalmazást), és válassza a **Windows**, **azonnali** lehetőséget a **hibakeresés** menüben.

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

A git parancs létrehozásához használja a kódolt jelszót a felhasználónévvel és a tárház helyével együtt.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

A tárház klónozása után megtekintheti és használhatja a helyi fájlrendszerben. További információ: [a helyi git-tárház fájl-és mappa szerkezetének referenciája](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>A helyi tárház frissítése a legújabb szolgáltatási példány-konfigurációval

Ha módosítja a API Management Service-példányát a Azure Portal vagy a REST API használatával, ezeket a változtatásokat a tárházba kell mentenie, mielőtt frissíteni tudja a helyi tárházat a legújabb módosításokkal. Ehhez kattintson a Mentés az **adattárba** lehetőségre a Azure Portal **adattár** lapján, majd adja ki a következő parancsot a helyi tárházban.

```
git pull
```

A futtatása előtt `git pull` Győződjön meg arról, hogy a helyi tárház mappájában van. Ha most fejezte be a `git clone` parancsot, akkor a könyvtárat a következőhöz hasonló parancs futtatásával kell módosítania a tárházra.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Változások leküldése a helyi tárházból a kiszolgálói tárházba
Ha a helyi tárházból szeretné leküldeni a módosításokat a kiszolgálói tárházba, véglegesítse a módosításokat, majd küldje el azokat a kiszolgálói tárházba. A módosítások elvégzéséhez nyissa meg a git-parancs eszközét, váltson a helyi tárház könyvtárába, és adja ki a következő parancsokat.

```
git add --all
git commit -m "Description of your changes"
```

A következő parancs futtatásával küldheti el az összes véglegesített műveletet a kiszolgálónak.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Bármely szolgáltatás-konfigurációs módosítás telepítése a API Management Service-példányra

Miután elvégezte a helyi módosításokat, és leküldte őket a kiszolgálói tárházba, telepítheti azokat a API Management Service-példányba.

A műveletnek a REST API használatával történő végrehajtásával kapcsolatos információkért lásd: [a git-módosítások telepítése a konfigurációs adatbázisba a REST API használatával](/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>A fájl-és mappák szerkezete a helyi git-adattárra mutató hivatkozás

A helyi git-tárházban található fájlok és mappák tartalmazzák a szolgáltatási példány konfigurációs adatait.

| Elem | Leírás |
| --- | --- |
| gyökérszintű API – felügyeleti mappa |A szolgáltatás példányának legfelső szintű konfigurációját tartalmazza |
| API-k mappa |Az API-k konfigurációját tartalmazza a szolgáltatási példányban. |
| csoportok mappa |A szolgáltatási példányban lévő csoportok konfigurációját tartalmazza. |
| házirendek mappa |A szolgáltatási példányban található szabályzatokat tartalmazza |
| portalStyles mappa |A fejlesztői portál testreszabási konfigurációját tartalmazza a szolgáltatási példányban. |
| termékek mappa |A szolgáltatási példányban található termékek konfigurációját tartalmazza. |
| Sablonok mappa |A szolgáltatási példányban található e-mail-sablonok konfigurációját tartalmazza. |

Minden mappa tartalmazhat egy vagy több fájlt, bizonyos esetekben pedig egy vagy több mappát, például az egyes API-k, termékek vagy csoportok mappáit. Az egyes mappákban található fájlok a Mappanév által leírt típusú entitásokra vonatkoznak.

| Fájl típusa | Cél |
| --- | --- |
| json |A megfelelő entitás konfigurációs adatai |
| html |Az entitásra vonatkozó leírások, amelyek gyakran megjelennek a fejlesztői portálon |
| xml |Házirend-utasítások |
| css |Stíluslapok a fejlesztői portál testreszabásához |

Ezeket a fájlokat létrehozhatja, törölheti, szerkesztheti és felügyelheti a helyi fájlrendszerben, és a módosítások a API Management szolgáltatás példányára lesznek telepítve.

> [!NOTE]
> A következő entitások nem szerepelnek a git-tárházban, és nem konfigurálhatók a git használatával.
>
> * [Felhasználók](/rest/api/apimanagement/2019-12-01/user)
> * [Előfizetések](/rest/api/apimanagement/2019-12-01/subscription)
> * Névvel ellátott értékek
> * A fejlesztői portál más entitásai, mint a stílusok
>

### <a name="root-api-management-folder"></a>Gyökérszintű API – felügyeleti mappa
A gyökérmappa `api-management` tartalmaz egy `configuration.json` fájlt, amely a következő formátumban tartalmazza a szolgáltatási példánnyal kapcsolatos legfelső szintű információkat.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

Az első négy beállítás ( `RegistrationEnabled` , `UserRegistrationTerms` , `UserRegistrationTermsEnabled` és `UserRegistrationTermsConsentRequired` ) az alábbi beállításokra mutat a **fejlesztői portál** **identitások** lapján.

| Identitás beállítása | Maps to |
| --- | --- |
| RegistrationEnabled |**Felhasználónév és jelszó-** identitás szolgáltatójának jelenléte |
| UserRegistrationTerms |**Használati feltételek felhasználói regisztráció** szövegmezőben |
| UserRegistrationTermsEnabled |Használati **feltételek megjelenítése a feliratkozási lapon** jelölőnégyzet |
| UserRegistrationTermsConsentRequired |**Beleegyezikés megkövetelése** jelölőnégyzet |
| RequireUserSigninEnabled |**Névtelen felhasználók átirányítása a bejelentkezési oldalra** jelölőnégyzet |

A következő négy beállítás ( `DelegationEnabled` , `DelegationUrl` , `DelegatedSubscriptionEnabled` és `DelegationValidationKey` ) leképezi a következő beállításokat a **fejlesztői portál** **delegálás** lapján.

| Delegálási beállítás | Maps to |
| --- | --- |
| DelegationEnabled |**Bejelentkezési & delegálása** jelölőnégyzet |
| DelegationUrl |**Delegálási végpont URL-címe** szövegmező |
| DelegatedSubscriptionEnabled |**Termék-előfizetés delegálása** jelölőnégyzet |
| DelegationValidationKey |**Ellenőrzési kulcs delegálása** szövegmező |

Az utolsó beállítás, `$ref-policy` amely a szolgáltatás példányának globális házirend-utasítások fájlját képezi le.

### <a name="apis-folder"></a>API-k mappa
A `apis` mappa a szolgáltatási példányban található minden API-hoz tartalmaz egy mappát, amely a következő elemeket tartalmazza.

* `apis\<api name>\configuration.json` – Ez az API konfigurációja, és a háttér-szolgáltatás URL-címével és műveleteivel kapcsolatos információkat tartalmaz. Ez ugyanaz az információ, amelyet a rendszer akkor ad vissza, ha a [megadott API](/rest/api/apimanagement/2019-12-01/apis/get) formátummal való meghívása megtörtént `export=true` `application/json` .
* `apis\<api name>\api.description.html` – Ez az API leírása, és az `description` [API-entitás](/java/api/com.microsoft.azure.storage.table.entityproperty)tulajdonságának felel meg.
* `apis\<api name>\operations\` – Ez a mappa olyan `<operation name>.description.html` fájlokat tartalmaz, amelyek az API-műveletekhez vannak leképezve. Minden fájl tartalmazza az API-ban lévő egyetlen művelet leírását, amely a `description` REST API [művelet entitásának](/rest/api/visualstudio/operations/list#operationproperties) tulajdonságára van leképezve.

### <a name="groups-folder"></a>csoportok mappa
A `groups` mappa a szolgáltatási példányban definiált egyes csoportok mappáját tartalmazza.

* `groups\<group name>\configuration.json` – Ez a csoport konfigurációja. Ez ugyanaz az információ, amelyet a rendszer akkor ad vissza, ha meghívja az [adott csoport beolvasása](/rest/api/apimanagement/2019-12-01/group/get) műveletet.
* `groups\<group name>\description.html` – Ez a csoport leírása, és a `description` [csoport entitás](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)tulajdonságának felel meg.

### <a name="policies-folder"></a>házirendek mappa
A `policies` mappa tartalmazza a szolgáltatási példány szabályzatának utasításait.

* `policies\global.xml` – a szolgáltatási példány globális hatókörében meghatározott szabályzatokat tartalmaz.
* `policies\apis\<api name>\` – Ha az API-hatókörben vannak definiálva szabályzatok, azok ebben a mappában találhatók.
* `policies\apis\<api name>\<operation name>\` mappa – ha a műveleti hatókörben definiált szabályzatok vannak megadva, azok a mappában találhatók, `<operation name>.xml` amelyek az egyes műveletek házirend-utasításait képezik le.
* `policies\products\` – Ha a termék hatókörében meghatározott szabályzatok vannak meghatározva, akkor ebben a mappában találhatók, amely `<product name>.xml` az egyes termékekhez tartozó házirend-utasításokhoz hozzárendelt fájlokat tartalmazza.

### <a name="portalstyles-folder"></a>portalStyles mappa
A `portalStyles` mappa konfigurációs és stíluslapokat tartalmaz a szolgáltatási példány fejlesztői portáljának testreszabásához.

* `portalStyles\configuration.json` -a fejlesztői portál által használt stíluslapok nevét tartalmazza.
* `portalStyles\<style name>.css` – minden `<style name>.css` fájl tartalmazza a fejlesztői portál stílusait ( `Preview.css` és `Production.css` alapértelmezés szerint).

### <a name="products-folder"></a>termékek mappa
A `products` mappa tartalmazza a szolgáltatási példányban definiált egyes termékek mappáját.

* `products\<product name>\configuration.json` – Ez a termék konfigurációja. Ez ugyanaz az információ, amelyet a rendszer akkor ad vissza, ha meghívja az [adott termék beolvasása](/rest/api/apimanagement/2019-12-01/product/get) műveletet.
* `products\<product name>\product.description.html` – Ez a termék leírása, és megfelel a `description` [termék entitás](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) tulajdonságának a REST APIban.

### <a name="templates"></a>sablonok
A `templates` mappa a szolgáltatás példányának [e-mail-sablonjainak](api-management-howto-configure-notifications.md) konfigurációját tartalmazza.

* `<template name>\configuration.json` – Ez az e-mail sablon konfigurációja.
* `<template name>\body.html` – Ez az e-mail sablon törzse.

## <a name="next-steps"></a>Következő lépések
A szolgáltatási példány kezelésének egyéb módjaival kapcsolatos további információkért lásd:

* A szolgáltatás példányának kezelése a következő PowerShell-parancsmagok használatával
  * [Szolgáltatások üzembe helyezése – PowerShell-parancsmagok leírása](/powershell/module/wds)
  * [Szolgáltatásfelügyelet – PowerShell-parancsmagok leírása](/powershell/azure/servicemanagement/overview)
* A szolgáltatás példányának kezelése a REST API használatával
  * [API Management REST API-hivatkozás](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png
