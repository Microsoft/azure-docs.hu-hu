---
title: Telefonhívás kezdeményezése a Twilio-ből (PHP) | Microsoft Docs
description: Megtudhatja, hogyan készíthet telefonhívást, és hogyan küldhet SMS-üzenetet a Twilio API szolgáltatással az Azure-ban. A minták PHP-alkalmazásokhoz használhatók.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: ef0642b26898249d78f5103e8a8f80198887bed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95542524"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Telefonhívás kezdeményezése a Twilio használatával az Azure-ban PHP-alkalmazásokban
Az alábbi példa bemutatja, hogyan használhatja a Twilio-t az Azure-ban üzemeltetett PHP-weblapokról. Az eredményül kapott alkalmazás felszólítja a felhasználót a telefonhívások értékeire, ahogy az alábbi képernyőképen is látható.

![Azure Call űrlap a Twilio és a PHP használatával][twilio_php]

A jelen témakörben található kód használatához a következőket kell tennie:

1. Twilio-fiók és hitelesítési jogkivonat beszerzése a [Twilio-konzolról][twilio_console]. A Twilio megkezdéséhez értékelje a díjszabást a következő címen: [https://www.twilio.com/pricing][twilio_pricing] . A próbaverziós fiókra a címen regisztrálhat [https://www.twilio.com/try-twilio][try_twilio] .
2. Szerezze be a [Twilio könyvtárat a PHP-hez](https://github.com/twilio/twilio-php) , vagy telepítse PEAR-csomagként. További információkért tekintse meg a [Readme fájlt](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Telepítse a PHP-hez készült Azure SDK-t. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](./app-service/quickstart-php.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Webes űrlap létrehozása hívás kezdeményezéséhez
Az alábbi HTML-kód bemutatja, hogyan hozhat létre egy olyan weboldalt (**callform.html**), amely a híváshoz felhasználói adatlekérdezéseket kér le:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>A kód létrehozása a hívás elvégzéséhez
A következő kód bemutatja, hogyan hozhat létre **MakeCall. php** fájlt, amely akkor lesz meghívva, amikor a felhasználó elküldi az **callform.html** által megjelenített űrlapot. Az alább látható kód létrehozza a hívási üzenetet, és létrehozza a hívást. Ne felejtse el használni a Twilio-fiókot és a hitelesítési jogkivonatot a [Twilio-konzolról][twilio_console] az alábbi kódban **$token** **$sidhoz** rendelt helyőrző értékek helyett.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

A hívás elvégzése mellett a **MakeCall. php** a hívási metaadatokat is megjeleníti, ahogy az alábbi képen is látható. További információ a hívási metaadatokról: [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties] .

![Azure-hívási válasz a Twilio és a PHP használatával][twilio_php_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
A következő lépés az [alkalmazás üzembe helyezése az Azure Web Apps](app-service/quickstart-php.md) -ben a git használatával (bár nem minden fontos információ). 

## <a name="next-steps"></a>Következő lépések
Ez a kód olyan alapszintű funkciókat mutat be, amelyek az Azure-ban a Twilio-t használják a PHP-ben. Mielőtt éles környezetben üzembe helyezi az Azure-t, érdemes lehet további hibakezelés vagy más funkciókat hozzáadnia. Például:

* Webes űrlap helyett az Azure Storage-Blobok vagy a SQL Database használatával tárolhatók a telefonszámok és a hívás szövege. További információ az Azure Storage-Blobok PHP-ben való használatáról: [Azure Storage használata php-alkalmazásokkal][howto_blob_storage_php]. További információ a PHP-SQL Database használatáról: [SQL Database használata php-alkalmazásokkal][howto_sql_azure_php].
* A **MakeCall. php** kód a Twilio által megadott URL-címet ( [https://twimlets.com/message][twimlet_message_url] ) használja egy Twilio Markup Language (TwiML) válasz megadására, amely arról tájékoztatja a Twilio, hogy hogyan folytathatja a hívást. A visszaadott TwiML például tartalmazhat egy olyan `<Say>` műveletet, amely a hívás címzettjének küldött szöveget eredményezi. A Twilio által biztosított URL-cím használata helyett saját szolgáltatást építhet ki a Twilio kérelmére való reagáláshoz. További információkért lásd: a [Twilio használata a hang-és SMS-funkciókhoz a PHP-ben][howto_twilio_voice_sms_php]. További információ a TwiML-ról: [https://www.twilio.com/docs/api/twiml][twiml] , és további információ a `<Say>` és más Twilio műveletekről: [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Olvassa el a Twilio biztonsági irányelveit a következő címen: [https://www.twilio.com/docs/security][twilio_docs_security] .

További információ a Twilio-ről: [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Lásd még:
* [A Twilio használata a hang-és SMS-funkciókhoz a PHP-ben](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: ./storage/blobs/storage-quickstart-blobs-php.md
[howto_sql_azure_php]: ./azure-sql/database/connect-query-content-reference-guide.md
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php