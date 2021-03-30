---
title: Alkalmazások közötti egyszeri bejelentkezés engedélyezése iOS-en a ADAL használatával | Microsoft Docs
description: A ADAL SDK funkcióinak használata az egyszeri bejelentkezés engedélyezéséhez az alkalmazások között.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08b018082c753b9524cb12a72d637fe5458d9114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383699"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Útmutató: alkalmazások közötti egyszeri bejelentkezés engedélyezése iOS-en a ADAL használatával

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára, hogy egyszer csak egyszer adják meg a hitelesítő adataikat, és hogy ezek a hitelesítő adatok automatikusan működjenek az alkalmazásokban és a más alkalmazások által használható platformok között (például Microsoft-fiókok vagy munkahelyi fiók Microsoft 365), függetlenül a közzétevőtől.

A Microsoft Identity platformja az SDK-k használatával egyszerűen engedélyezheti az egyszeri bejelentkezést a saját csomagján belül, illetve a közvetítői képességgel és a hitelesítő alkalmazásokkal a teljes eszközön.

Ebben az útmutatóban megtudhatja, hogyan konfigurálhatja az SDK-t az alkalmazáson belül, hogy SSO-t nyújtson ügyfeleinek.

Ez a következő útmutatók érvényesek:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* B2B Azure Active Directory
* Feltételes hozzáférés Azure Active Directory

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató feltételezi, hogy tudja, hogyan:

* Az alkalmazás kiépítése az Azure AD-hez készült örökölt portálon. További információ: [alkalmazás regisztrálása](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Az alkalmazás integrálása az [Azure ad iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc)-val.

## <a name="single-sign-on-concepts"></a>Az egyszeri bejelentkezés alapelvei

### <a name="identity-brokers"></a>Identity Broker

A Microsoft minden olyan mobil platformhoz biztosít alkalmazásokat, amely lehetővé teszi a különböző gyártóktól származó alkalmazások hitelesítő adatainak összekapcsolását, valamint olyan továbbfejlesztett funkciókat, amelyekhez egyetlen biztonságos hely szükséges a hitelesítő adatok érvényesítéséhez. Ezeket **közvetítőknek** nevezzük.

Az iOS-és Android-eszközökön a közvetítőket olyan letölthető alkalmazásokkal biztosítjuk, amelyeket az ügyfelek önállóan telepítenek vagy továbbítanak az eszközre egy olyan vállalat, aki az alkalmazottak számára az eszközöket felügyeli. A brókerek csak bizonyos alkalmazások vagy a teljes eszköz biztonságának felügyeletét támogatják a rendszergazda konfigurációja alapján. A Windowsban ezt a funkciót egy, az operációs rendszerbe beépített fiók-Chooser biztosítja, amely technikailag a webes hitelesítési közvetítőként is ismert.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>A mobileszközökön való bejelentkezéshez használt minták

Az eszközök hitelesítő adataihoz való hozzáférés két alapvető mintát követ:

* Nem brókerek által támogatott bejelentkezések
* A közvetítő által támogatott bejelentkezések

#### <a name="non-broker-assisted-logins"></a>Nem brókerek által támogatott bejelentkezések

A nem brókerek által támogatott bejelentkezések olyan bejelentkezési élmények, amelyek az alkalmazással összhangban történnek, és az eszközön lévő helyi tárolót használják az adott alkalmazáshoz. Ez a tárterület több alkalmazás között is megosztható, de a hitelesítő adatok szorosan kötődnek az alkalmazáshoz vagy az alkalmazásokhoz a hitelesítő adatok használatával. Valószínűleg sok mobil alkalmazásban tapasztalta ezt, ha az alkalmazásban felhasználónevet és jelszót ad meg.

Ezek a bejelentkezések az alábbi előnyökkel járnak:

* A felhasználói élmény teljes mértékben az alkalmazáson belül van.
* A hitelesítő adatok megoszthatók az azonos tanúsítvánnyal aláírt alkalmazások között, így egyszeri bejelentkezéses felhasználói élményt biztosíthatnak az alkalmazások alkalmazáscsomag számára.
* A bejelentkezés felhasználói élményének szabályozása a bejelentkezés előtt és után biztosított az alkalmazás számára.

Ezek a bejelentkezések a következő hátrányokkal rendelkeznek:

* A felhasználók nem tapasztalhatnak egyszeri bejelentkezést a Microsoft-identitást használó összes alkalmazásban, csak azokon a Microsoft-identitásokon, amelyeket az alkalmazása konfigurált.
* Az alkalmazás nem használható olyan speciális üzleti funkciókkal, mint például a feltételes hozzáférés, vagy az Intune Suite termékek használata.
* Az alkalmazás nem támogatja a tanúsítvány alapú hitelesítést az üzleti felhasználók számára.

Az alábbi példa azt mutatja be, hogy az SDK hogyan működik együtt az alkalmazások megosztott tárhelyével az egyszeri bejelentkezés engedélyezéséhez:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>A közvetítő által támogatott bejelentkezések

A közvetítő által támogatott bejelentkezések a közvetítő alkalmazáson belül bekövetkező bejelentkezési élmények, és a közvetítő tárterületének és biztonságának használatával megoszthatják a hitelesítő adatokat az eszközön található összes alkalmazásban, amely az Identity platformot alkalmazza. Ez azt jelenti, hogy alkalmazásai támaszkodnak a közvetítőre, hogy aláírják a felhasználókat a alkalmazásban. Az iOS és az Android rendszerekben ezek a közvetítők olyan letölthető alkalmazásokon keresztül érhetők el, amelyeket az ügyfelek önállóan telepítenek, vagy az eszközt egy olyan vállalat küldi el, aki felügyeli az eszközt a felhasználó számára. Ilyen típusú alkalmazás például az iOS rendszeren futó Microsoft Authenticator alkalmazás. A Windowsban ez a funkció egy olyan fiók, amely az operációs rendszerhez készült, amely technikailag ismert a webes hitelesítési közvetítőként.

Az élmény a platformtól függ, és esetenként zavaró lehet a felhasználók számára, ha nem megfelelően kezeli őket. Valószínűleg ismeri ezt a mintát, ha telepítve van a Facebook-alkalmazás, és használja a Facebook-kapcsolatot egy másik alkalmazásból. Az Identity platform ugyanazt a mintát használja.

Az iOS esetén ez egy "átmenet" animációhoz vezet, ahol az alkalmazást a háttérben küldik el, míg a Microsoft Authenticator-alkalmazások előtérben elérhetővé válnak a felhasználó számára annak kiválasztására, hogy melyik fiókot szeretnék bejelentkezni.

Android és Windows esetén a fiók kiválasztása az alkalmazás tetején jelenik meg, ami kevésbé zavaró a felhasználó számára.

#### <a name="how-the-broker-gets-invoked"></a>A közvetítő meghívásának módja

Ha a kompatibilis közvetítő telepítve van az eszközön, például a Microsoft Authenticator alkalmazáshoz, az SDK-k automatikusan elvégzik a közvetítő meghívását, ha a felhasználó azt jelzi, hogy az Identity platform bármely fiókjának használatával szeretne bejelentkezni. Ez a fiók lehet személyes Microsoft-fiók, munkahelyi vagy iskolai fiók, valamint az Azure-ban a B2C-és B2B-termékekkel üzemeltetett fiók.

#### <a name="how-we-ensure-the-application-is-valid"></a>Az alkalmazás érvényességének biztosítása

Biztosítani kell, hogy a közvetítőt meghívó alkalmazás identitása létfontosságú legyen a bróker által támogatott bejelentkezésekben nyújtott biztonság szempontjából. Sem az iOS, sem az Android nem kényszeríti ki az olyan egyedi azonosítókat, amelyek csak egy adott alkalmazás esetében érvényesek, így a rosszindulatú alkalmazások "hamisítják" a legitim alkalmazás azonosítóját, és megkapják a legitim alkalmazás számára jelentett jogkivonatokat. Annak biztosítása érdekében, hogy a rendszer mindig a megfelelő alkalmazással kommunikáljon futásidőben, megkéri a fejlesztőt, hogy adjon meg egy egyéni redirectURI, amikor az alkalmazást a Microsofttal regisztrálja. A fejlesztők számára az átirányítási URI-t az alábbi részletesen tárgyaljuk. Ez az egyéni redirectURI tartalmazza az alkalmazás csomag-AZONOSÍTÓját, és gondoskodik arról, hogy az Apple App Store-ban egyedi legyen az alkalmazás. Amikor egy alkalmazás meghívja a közvetítőt, a közvetítő arra kéri az iOS operációs rendszert, hogy a közvetítőnek nevezett köteg-AZONOSÍTÓval lássa el. A közvetítő ezt a köteg-azonosítót biztosítja a Microsoftnak a személyazonossági rendszer meghívása során. Ha az alkalmazás csomag-azonosítója nem egyezik meg a fejlesztő által a regisztráció során nekünk megadott köteg-AZONOSÍTÓval, a rendszer megtagadja a hozzáférést az alkalmazás által kért erőforrás jogkivonatához. Ez az ellenőrzés biztosítja, hogy csak a fejlesztő által regisztrált alkalmazás fogadja a jogkivonatokat.

**A fejlesztő választhat, hogy az SDK meghívja-e a közvetítőt, vagy a nem közvetítő által támogatott folyamatot használja-e.** Ha azonban a fejlesztő úgy dönt, hogy nem használja a közvetítő által támogatott folyamatot, elveszítik a felhasználó által az eszközön már hozzáadott SSO hitelesítő adatok használatának előnyeit, és meggátolják, hogy alkalmazásaikat a Microsoft üzleti funkcióival együtt használják, például a feltételes hozzáférést, az Intune felügyeleti képességeit és a tanúsítványalapú hitelesítést.

Ezek a bejelentkezések az alábbi előnyökkel járnak:

* A felhasználó az összes alkalmazásában egyszeri bejelentkezést észlel a gyártótól függetlenül.
* Az alkalmazás olyan speciális üzleti funkciókat is használhat, mint például a feltételes hozzáférés vagy az Intune-csomag használata.
* Az alkalmazás képes a tanúsítványalapú hitelesítés támogatására az üzleti felhasználók számára.
* Sokkal biztonságosabb bejelentkezési élmény az alkalmazás identitása és a felhasználó ellenőrzése a közvetítő alkalmazás által további biztonsági algoritmusokkal és titkosítással történik.

Ezek a bejelentkezések a következő hátrányokkal rendelkeznek:

* Az iOS-ben a felhasználó átvált az alkalmazás felhasználói felületéről, miközben a rendszer hitelesítő adatokat választ.
* Az alkalmazáson belüli ügyfelek bejelentkezési élményének kezelése.

Az alábbi példa azt szemlélteti, hogy az SDK hogyan működik együtt a közvetítő alkalmazásokkal az egyszeri bejelentkezés engedélyezéséhez:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

## <a name="enabling-cross-app-sso-using-adal"></a>Alkalmazások közötti egyszeri bejelentkezés engedélyezése a ADAL használatával

Itt a ADAL iOS SDK-t használjuk a következőkre:

* A nem bróker által támogatott egyszeri bejelentkezés bekapcsolása az alkalmazások alkalmazáscsomag számára
* Támogatás bekapcsolása a közvetítő által támogatott egyszeri bejelentkezéshez

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>SSO bekapcsolása nem bróker által támogatott egyszeri bejelentkezéshez

A nem brókerek által támogatott egyszeri bejelentkezések esetében az SDK-k az egyszeri bejelentkezés sok összetettségét kezelik. Ebbe beletartozik a megfelelő felhasználó megkeresése a gyorsítótárban, és a bejelentkezett felhasználók listájának fenntartása a lekérdezéshez.

Ha engedélyezni szeretné az egyszeri bejelentkezést a saját alkalmazásai között, a következőket kell tennie:

1. Győződjön meg arról, hogy az összes alkalmazás ugyanazt az ügyfél-azonosítót vagy az alkalmazás-azonosítót használja.
2. Győződjön meg arról, hogy az összes alkalmazás ugyanazzal az aláírási tanúsítvánnyal rendelkezik az Apple-től, hogy meg tudja osztani a kulcstartókat.
3. Az egyes alkalmazásokhoz ugyanazt a kulcstartó jogosultságot kell kérnie.
4. Tájékoztassa az SDK-kat a használni kívánt megosztott kulcstartóról.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Ugyanaz az ügyfél-azonosító/alkalmazás-azonosító használata az alkalmazások alkalmazáscsomag összes alkalmazásához

Ahhoz, hogy az Identity platform tudja, hogy a tokenek megoszthatók az alkalmazásokban, minden alkalmazásnak ugyanazzal az ügyfél-AZONOSÍTÓval vagy alkalmazás-AZONOSÍTÓval kell rendelkeznie. Ez az egyedi azonosító, amelyet az első alkalmazásnak a portálon való regisztrálása során adott meg.

Az átirányítási URI-k lehetővé teszik a különböző alkalmazások azonosítását a Microsoft Identity Service-ben, ha ugyanazt az alkalmazást használják. Az egyes alkalmazások több átirányítási URI-t is regisztrálhatnak a bevezetési portálon. A csomag minden alkalmazásának egy másik átirányítási URI-ja lesz. Példa erre a nézetre:

App1 átirányítási URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 átirányítási URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 átirányítási URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Ezek az ügyfél-azonosító/alkalmazás-azonosító alá vannak ágyazva, és a rendszer az SDK-konfigurációban részünkre visszaadott átirányítási URI alapján keresi fel őket.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

Az átirányítási URI-k formátumát alább találja. Bármely átirányítási URI-t használhat, hacsak nem szeretné támogatni a közvetítőt, ebben az esetben a fentiekhez hasonlóan kell kinéznie *

#### <a name="create-keychain-sharing-between-applications"></a>Kulcstartó megosztásának létrehozása az alkalmazások között

A kulcstartó megosztásának engedélyezése meghaladja a dokumentum hatókörét, és az Apple a dokumentumhoz való [hozzáadásával](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)is rendelkezik. Fontos, hogy eldöntse, mit szeretne meghívni a kulcstartóhoz, és adja hozzá ezt a képességet az összes alkalmazásához.

Ha a jogosultságok megfelelően vannak beállítva, akkor a projekt könyvtárában található fájlnak kell megjelennie, amely `entitlements.plist` a következőhöz hasonlót tartalmaz:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Miután az összes alkalmazásban engedélyezte a kulcstartó jogosultságot, és készen áll az egyszeri bejelentkezés használatára, tájékoztassa az Identity SDK-t a kulcstartóról a következő beállítás használatával `ADAuthenticationSettings` :

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Amikor megoszt egy kulcstartót az alkalmazásai között, bármely alkalmazás törölheti a felhasználókat, vagy rosszabban törölheti az összes tokent az alkalmazásban. Ez különösen akkor katasztrofális, ha olyan alkalmazásokkal rendelkezik, amelyek a jogkivonatokat használják a háttérben végzett munkához. A kulcstartó megosztása azt jelenti, hogy nagyon körültekintően kell lennie az összes eltávolítási műveletnek az Identity SDK-k használatával.

Ennyi az egész! Az SDK ekkor megosztja a hitelesítő adatokat az összes alkalmazásban. A felhasználói lista az alkalmazás példányai között is meg lesz osztva.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Az SSO bekapcsolása a közvetítő által támogatott egyszeri bejelentkezéshez

**Alapértelmezés szerint ki van kapcsolva** az alkalmazás azon képessége, hogy bármely, az eszközön telepített közvetítőt használhassanak. Ahhoz, hogy az alkalmazást a közvetítővel használhassa, további konfigurációt kell végrehajtania, és hozzá kell adnia egy kódot az alkalmazáshoz.

A követendő lépések a következők:

1. Engedélyezze a Broker üzemmódot az alkalmazás kódjának az MS SDK-ban való meghívásakor.
2. Hozzon létre egy új átirányítási URI-t, és adja meg, hogy az alkalmazás és az alkalmazás regisztrálása is megtörténjen.
3. URL-séma regisztrálása.
4. Adjon hozzá egy engedélyt az info. plist fájlhoz.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1. lépés: a közvetítő mód engedélyezése az alkalmazásban

Az alkalmazás a közvetítő használatára való képessége a "környezet" vagy a hitelesítési objektum kezdeti beállításának létrehozásakor be van kapcsolva. Ezt a hitelesítő adatoknak a kódban való megadásával teheti meg:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A `AD_CREDENTIALS_AUTO` beállítás lehetővé teszi, hogy az SDK megpróbálja meghívni a közvetítőt, `AD_CREDENTIALS_EMBEDDED` megakadályozza, hogy az SDK meghívja a közvetítőt.

#### <a name="step-2-registering-a-url-scheme"></a>2. lépés: URL-séma regisztrálása

Az Identity platform URL-címeket használ a közvetítő meghívásához, majd visszaküldi a vezérlést az alkalmazáshoz. A kerekítés befejezéséhez szüksége lesz egy, az alkalmazáshoz regisztrált URL-sémára, amelyet az Identity platform tudni fog. Ez bármilyen más alkalmazás-sémán kívül is lehet, amelyet korábban már regisztrált az alkalmazásában.

> [!WARNING]
> Azt javasoljuk, hogy az URL-séma meglehetősen egyedi legyen, hogy csökkentse egy másik alkalmazás esélyét ugyanazzal az URL-séma használatával. Az Apple nem kényszeríti ki az App Store-ban regisztrált URL-sémák egyediségét.

Az alábbi példa bemutatja, hogyan jelenik meg ez a projekt konfigurációjában. Ezt XCode is elvégezheti:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>3. lépés: hozzon létre egy új átirányítási URI-t az URL-sémával

Annak biztosítása érdekében, hogy mindig a megfelelő alkalmazáshoz adja vissza a hitelesítő jogkivonatokat, meg kell győződni arról, hogy az iOS operációs rendszer által ellenőrizhető módon vissza kell hívni az alkalmazásba. Az iOS operációs rendszer jelentései a Microsoft Broker-alkalmazások számára a meghívó alkalmazás köteg-azonosítója. Ezt nem lehet meghamisítani egy szélhámos alkalmazás. Ezért ezt a közvetítői alkalmazás URI-JÁT is kihasználjuk, hogy a jogkivonatok a megfelelő alkalmazáshoz legyenek visszaküldve. Ehhez egyedi átirányítási URI-t kell létrehoznia az alkalmazásban, és az átirányítási URI-t kell beállítani a fejlesztői portálon.

Az átirányítási URI-nak a megfelelő formában kell lennie:

`<app-scheme>://<your.bundle.id>`

pl.: *x-msauth-mytestiosapp://com.MyApp.mytestapp*

Ezt az átirányítási URI-t meg kell adni az alkalmazás regisztrációjában a [Azure Portal](https://portal.azure.com/)használatával. Az Azure AD-alkalmazás regisztrálásával kapcsolatos további információkért lásd: [integráció a Azure Active Directoryával](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>3a. lépés: adjon hozzá egy átirányítási URI-t az alkalmazásban és a fejlesztői portálon a tanúsítványalapú hitelesítés támogatásához

A tanúsítványalapú hitelesítés támogatásához egy második "msauth" kell regisztrálni az alkalmazásban és a [Azure Portal](https://portal.azure.com/) a tanúsítvány-hitelesítés kezeléséhez, ha a támogatást hozzá kívánja adni az alkalmazáshoz.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

pl.: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.MyApp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>4. lépés: konfigurációs paraméter hozzáadása az alkalmazáshoz

ADAL – canOpenURL: annak ellenőrzését, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verziójában az Apple lezárta az alkalmazás által lekérdezhető sémákat. A "msauth" kifejezést hozzá kell adnia a összes szakaszhoz `info.plist file` .

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Konfigurálta az SSO-t!

Most az Identity SDK automatikusan megosztja a hitelesítő adatokat az alkalmazásokban, és meghívja a közvetítőt, ha az eszközön van.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [egyszeri bejelentkezéses SAML protokollról](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
