---
title: A Twilio használata a hang-és SMS-hez (PHP) | Microsoft Docs
description: Megtudhatja, hogyan készíthet telefonhívást, és hogyan küldhet SMS-üzenetet a Twilio API szolgáltatással az Azure-ban. A PHP-ben írt kódok mintái.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: bf1ab01b39d594002bc5e677ffe6c3049fbb91ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95521019"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>A Twilio használata a hang-és SMS-funkciókhoz a PHP-ben
Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure Twilio API szolgáltatásával. A tárgyalt forgatókönyvek közé tartozik a telefonhívás kezdeményezése és egy rövid üzenetküldési szolgáltatás (SMS) üzenet küldése. A Twilio és a hang-és SMS-alkalmazások alkalmazásokban való használatáról további információt a [következő lépések](#NextSteps) című szakaszban talál.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio az üzleti kommunikáció jövőjét fejleszti, és lehetővé teszi, hogy a fejlesztők hang-, VoIP-és üzenetkezelési adatokat ágyaznak be alkalmazásokba. Minden szükséges infrastruktúra virtualizálása egy felhőalapú, globális környezetben, amely a Twilio Communications API platformon keresztül teszi elérhetővé. Az alkalmazások egyszerűen létrehozhatók és méretezhetők. Az utólagos elszámolású díjszabás és a Felhőbeli megbízhatóság előnyeit élvezheti.

A **Twilio Voice** lehetővé teszi az alkalmazások számára telefonhívások készítését és fogadását. A **TWILIO SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon. A **Twilio-ügyfél** lehetővé teszi, hogy bármilyen telefonról, táblaszámítógépről vagy böngészőből kezdeményezheti a VoIP-hívásokat, és támogatja a WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>A Twilio díjszabása és különleges ajánlataink
A Twilio-fiók frissítésekor az Azure-ügyfelek [különleges ajánlatot](https://www.twilio.com/azure)kapnak: ingyenes, $10-Twilio kreditet. Ez a Twilio-kredit bármely Twilio-felhasználásra alkalmazható ($10 kredit, amely akár több mint 1 000 SMS-üzenetet küld, akár 1000 bejövő hang-percet is igénybe vehet, a telefonszám és az üzenet vagy a hívás célhelyének helyétől függően). Váltsa be ezt a Twilio-kreditet, és kezdje a következő címen: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure) .

A Twilio az utólagos elszámolású szolgáltatás. Nincsenek beállított díjak, és bármikor lezárhatja a fiókját. További részleteket a [Twilio díjszabását][twilio_pricing]ismertető webhelyen talál.

## <a name="concepts"></a><a id="Concepts"></a>Alapelvek
A Twilio API egy REST-alapú API, amely hang-és SMS-funkciókat biztosít az alkalmazásokhoz. Az ügyféloldali kódtárak több nyelven is elérhetők; a listákat lásd: [TWILIO API-kódtárak][twilio_libraries].

A Twilio API legfontosabb szempontjai a Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-műveletek
Az API a Twilio-műveletek használatát teszi lehetővé; például a **&lt; Say &gt;** művelet arra utasítja a Twilio, hogy hallhatóan kézbesítse az üzenetet egy híváson.

A következő lista az Twilio-műveletek listáját tartalmazza. Ismerje meg a többi műveletet és képességeket a [Twilio Markup Language dokumentációján](https://www.twilio.com/docs/api/twiml)keresztül.

* **&lt; Dial &gt;**: a hívó csatlakoztatása egy másik telefonhoz.
* Összegyűjtése: a telefon billentyűzetén megadott numerikus számjegyeket gyűjti. **&lt; &gt;**
* **&lt; Vonalbontás &gt;**: egy hívást ér véget.
* **&lt; Lejátszás &gt;**: hangfájl lejátszása.
* **&lt; Szüneteltetés &gt;**: csendes várakozás a megadott számú másodpercre.
* **&lt; Rekord &gt;**: a hívó hangját rögzíti, és egy, a rögzítést tartalmazó fájl URL-címét adja vissza.
* **&lt; Átirányítás &gt;**: egy hívás vagy SMS átadása a TWIML egy másik URL-címen keresztül.
* **&lt; Elutasítás &gt;**: elutasítja a Twilio-szám bejövő hívását a számlázás nélkül
* **&lt; Tegyük &gt;** fel, hogy a szöveget a híváson végzett beszédre alakítja át.
* **&lt; SMS &gt;**: SMS-üzenet küldése.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
A TwiML XML-alapú utasításokat tartalmaz a Twilio műveletek alapján, amelyek tájékoztatják a Twilio, hogy hogyan dolgozzák fel a hívást vagy az SMS-t.

Példaként a következő TwiML konvertálja a szöveget **Hello World** beszédre.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Amikor az alkalmazás meghívja a Twilio API-t, az egyik API-paraméter az a URL-cím, amely a TwiML választ adja vissza. A Twilio által biztosított URL-címek segítségével megadhatja az alkalmazások által használt TwiML-válaszokat. Saját URL-címeket is tárolhat a TwiML-válaszok létrehozásához, és egy másik lehetőség a **TwiMLResponse** objektum használata.

A Twilio-műveletekkel, azok attribútumaival és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll egy Twilio-fiók beszerzésére, regisztráljon a [Twilio kipróbálása][try_twilio]gombra. Elindíthat egy ingyenes fiókot, és később is frissítheti a fiókját.

Amikor regisztrál egy Twilio-fiókra, megkapja a fiók AZONOSÍTÓját és a hitelesítési jogkivonatot. Mindkettőre szükség lesz a Twilio API-hívások létrehozásához. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében őrizze meg biztonságos hitelesítési tokenjét. A fiók-azonosító és a hitelesítési jogkivonat a [Twilio fiók lapján][twilio_account], a **fiók SID** és **hitelesítési jogkivonat** mezőjében látható.

## <a name="create-a-php-application"></a><a id="create_app"></a>PHP-alkalmazás létrehozása
A Twilio szolgáltatást használó és az Azure-ban futó PHP-alkalmazások nem különböznek a Twilio szolgáltatást használó más PHP-alkalmazástól. Habár a Twilio-szolgáltatások REST-alapúak, és a PHP-ből többféleképpen hívhatók, ez a cikk arra összpontosít, hogy hogyan használhatja a Twilio-szolgáltatásokat a [PHP-hez készült Twilio Library használatával a githubról][twilio_php]. További információ a PHP-hez készült Twilio-könyvtár használatáról: [https://www.twilio.com/docs/libraries/php][twilio_lib_docs] .

A Twilio/PHP-alkalmazások Azure-ban való létrehozásával és üzembe helyezésével kapcsolatos részletes utasítások az [Azure-beli php-alkalmazásban a Twilio használatával történő][howto_phonecall_php]telefonhívások során érhetők el.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Az alkalmazás konfigurálása Twilio-kódtárak használatára
Az alkalmazás két módon konfigurálható úgy, hogy a Twilio-függvénytárat használja a PHP-hez:

1. Töltse le a Twilio könyvtárat a PHP-hez a GitHubról ( [https://github.com/twilio/twilio-php][twilio_php] ), és adja hozzá a **Services** könyvtárat az alkalmazáshoz.
   
    -VAGY-
2. Telepítse a PHP-hez készült Twilio könyvtárat PEAR-csomagként. A következő parancsokkal telepíthető:

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

A PHP-hez készült Twilio-könyvtár telepítését követően hozzáadhat egy **require_once** UTASÍTÁST a php-fájlok tetején a könyvtárra való hivatkozáshoz:

```php
require_once 'Services/Twilio.php';
```

További információ: [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: kimenő hívás létrehozása
Az alábbiakban bemutatjuk, hogyan lehet kimenő hívást kezdeményezni a **Services_Twilio** osztály használatával. Ez a kód egy Twilio által biztosított helyet is használ a Twilio Markup Language (TwiML) válaszának visszaadásához. Helyettesítse be a **Feladó** és **a** telefonszám értékeit, és győződjön meg arról, hogy a kód futtatása előtt ellenőrizze a Twilio **-** fiók telefonszámát.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";

// The number of the phone initiating the call.
$from_number = "NNNNNNNNNNN";

// The number of the phone receiving call.
$to_number = "NNNNNNNNNNN";

// Use the Twilio-provided site for the TwiML response.
$url = "https://twimlets.com/message";

// The phone message text.
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

//Make the call.
try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Ahogy említettük, ez a kód egy Twilio által biztosított helyet használ a TwiML válasz visszaadásához. Ehelyett használhatja a saját webhelyét, hogy megadja a TwiML választ; További információkért lásd: [TwiML-válaszok megadása a saját webhelyéről](#howto_provide_twiml_responses).

* **Megjegyzés**: a TLS/SSL-tanúsítvány érvényesítési hibáinak elhárításához lásd: [https://www.twilio.com/docs/api/errors][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Útmutató: SMS-üzenet küldése
A következő bemutatja, hogyan küldhet SMS-üzenetet a **Services_Twilio** osztály használatával. Az SMS **-** üzenetek küldéséhez a Twilio által biztosított próbaverziót adja meg. A számot a kód futtatása előtt ellenőrizni kell a Twilio **-** fiókhoz.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";


$from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
$to_number = "NNNNNNNNNNN";
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

// Send the SMS message.
try
{
    $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Útmutató: TwiML-válaszok megadása saját webhelyről
Amikor az alkalmazás kezdeményez egy hívást a Twilio API-hoz, a Twilio egy olyan URL-címre küldi a kérést, amely egy TwiML-válasz visszaadására vár. A fenti példa a Twilio által megadott URL-címet használja [https://twimlets.com/message][twimlet_message_url] . (Míg a TwiML a Twilio általi használatra készült, megtekintheti azt a böngészőben. Ha például egy [https://twimlets.com/message][twimlet_message_url] üres elemet szeretne megtekinteni, `<Response>` egy másik példaként kattintson ide [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] egy `<Response>` elemet tartalmazó elem megjelenítéséhez `<Say>` .)

Ahelyett, hogy a Twilio által megadott URL-címet kellene megadnia, létrehozhat egy saját helyet, amely visszaadja a HTTP-válaszokat. A helyet bármilyen nyelven létrehozhatja, amely az XML-válaszokat adja vissza; Ez a témakör feltételezi, hogy a PHP-t fogja használni a TwiML létrehozásához.

A következő PHP-oldal egy TwiML választ eredményez, amely a hívás **Hello Worldét** mondja.

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

Ahogy a fenti példában is látható, a TwiML válasz egyszerűen egy XML-dokumentum. A PHP-hez készült Twilio-könyvtár olyan osztályokat tartalmaz, amelyek a TwiML-t fogják előállítani. Az alábbi példa a fentiekben látható egyenértékű választ állítja elő, de a **\_ Twilio \_ Twiml** osztályt használja a PHP-hez készült Twilio-könyvtárban:

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

További információ a TwiML: [https://www.twilio.com/docs/api/twiml][twiml_reference] . 

Miután beállította a PHP-oldalát, hogy TwiML válaszokat adjon meg, használja a PHP-oldal URL-címét a metódusnak átadott URL-címként  `Services_Twilio->account->calls->create`  . Ha például egy **MyTwiML** nevű webalkalmazást helyez üzembe egy Azure által üzemeltetett szolgáltatásban, és a php-oldal neve **MyTwiML. php**, az URL-cím átadható  **Services_Twilio->Account->calls->Create**  as the következő példában látható módon:

```php
require_once 'Services/Twilio.php';

$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";
$from_number = "NNNNNNNNNNN";
$to_number = "NNNNNNNNNNN";
$url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

// The phone message text.
$message = "Hello world.";

$client = new Services_Twilio($sid, $token, "2010-04-01");

try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

További információ a Twilio az Azure-ban PHP-vel való használatáról: [telefonhívás kezdeményezése Twilio használatával php-alkalmazásban az Azure-ban][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Útmutató: további Twilio-szolgáltatások használata
Az itt bemutatott példákon kívül a Twilio webalapú API-kat kínál, amelyek segítségével további Twilio funkciókat alkalmazhat az Azure-alkalmazásból. További részletekért tekintse meg a [TWILIO API dokumentációját][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>További lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozásokat, ahol további információt talál:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Twilio HowTo és example kód][twilio_howtos]
* [Twilio gyors útmutatók][twilio_quickstarts] 
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio-támogatásról][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart