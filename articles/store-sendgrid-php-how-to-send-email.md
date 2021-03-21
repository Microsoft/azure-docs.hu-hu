---
title: A SendGrid e-mail szolgáltatás használata (PHP) | Microsoft Docs
description: Ismerje meg, hogyan küldhet e-mailt az Azure SendGrid e-mail szolgáltatásával. A PHP-ben írt kódok mintái.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023788"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>A SendGrid E-mail szolgáltatás használata a PHP-ből

Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure SendGrid e-mail szolgáltatásával. A mintákat a PHP-ben írták.
A tárgyalt forgatókönyvek közé tartozik például az **e-mailek** létrehozása, az **e-mail küldése** és a **mellékletek hozzáadása**. További információt a SendGrid és az e-mailek küldéséről a [következő lépések](#next-steps) című szakaszban talál.

## <a name="what-is-the-sendgrid-email-service"></a>Mi a SendGrid E-mail szolgáltatás?
A SendGrid egy [felhőalapú e-mail-szolgáltatás] , amely megbízható [tranzakciós e-mail-kézbesítést], skálázhatóságot és valós idejű elemzéseket biztosít, valamint rugalmas API-kat, amelyek egyszerűvé teszik az egyéni integrációt. Az általános SendGrid-használati forgatókönyvek a következők:

* Nyugták automatikus küldése az ügyfeleknek
* A terjesztési listán szereplő ügyfelek havi e-szórólapok és különleges ajánlatok küldésének felügyelete
* Valós idejű mérőszámok gyűjtése a letiltott e-mailekhez és az ügyfelekre való válaszadáshoz
* Jelentések létrehozása a trendek azonosításához
* Ügyfelekkel kapcsolatos kérdések továbbítása
* Az alkalmazás e-mail-értesítései

További információ: [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>A SendGrid használata a PHP-alkalmazásból

A SendGrid Azure PHP-alkalmazásokban való használata nem igényel speciális konfigurációt vagy kódolást. Mivel a SendGrid szolgáltatás, a Felhőbeli alkalmazásokból ugyanúgy elérhető, mint egy helyszíni alkalmazásból.

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése

E-mailt az SendGrid által biztosított SMTP vagy a webes API használatával is elküldhet.

### <a name="smtp-api"></a>SMTP API

Ha a SendGrid SMTP API-val szeretne e-mailt küldeni, használja a *Swift-levelezőt*, egy összetevő-alapú könyvtárat, amely e-maileket küld a PHP-alkalmazásokból. Letöltheti a [Swift Mailer Library](https://swiftmailer.symfony.com/) v 5.3.0 (a [zeneszerző] használatával telepítheti a Swift-levelezőt). E-mailek küldése a könyvtárnak a `Swift\_SmtpTransport` , `Swift\_Mailer` , és osztályok példányainak létrehozását `Swift\_Message` , a megfelelő tulajdonságok beállítását és a metódus meghívását jelenti `Swift\_Mailer::send` .

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Webes API
Az SendGrid web API használatával e-maileket küldhet a PHP [curl függvénnyel][curl function] .

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

A SendGrid webes API-ját nagyon hasonlít egy REST APIhoz, bár ez nem igazán egy REST API, mivel a legtöbb hívás esetében a GET és a POST művelet is felhasználható.

## <a name="how-to-add-an-attachment"></a>Útmutató: melléklet hozzáadása

### <a name="smtp-api"></a>SMTP API

A mellékletek SMTP API-val való elküldése egy további kódrészletet is magában foglal, amely egy e-mailt küld a Swift Mailer használatával.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

A kód további sora a következő:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Ez a sor a csatolási metódust hívja meg az `Swift\_Message` objektumon, és statikus metódust használ `fromPath` az `Swift\_Attachment` osztályban egy fájl üzenethez való lekéréséhez és csatolásához.

### <a name="web-api"></a>Webes API

A mellékletek webes API-val történő küldése nagyon hasonlít a webes API-t használó e-mailek küldéséhez. Vegye figyelembe azonban, hogy a következő példában szereplő paraméternek tartalmaznia kell ezt az elemet:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Példa

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Útmutató: szűrők használata a láblécek, a követés és az elemzés engedélyezéséhez

A SendGrid további e-mail-funkciókat biztosít a *szűrők* használatával. Ezek olyan beállítások, amelyek hozzáadhatók egy e-mail-üzenethez, amely lehetővé teszi bizonyos funkciók használatát, például a követést, a Google Analyticset, az előfizetés nyomon követését stb.

A szűrők az üzenetekre a szűrők tulajdonság használatával alkalmazhatók. A szűrőket a szűrőre jellemző beállításokat tartalmazó kivonat határozza meg. A következő példa engedélyezi a lábléc szűrőjét, és megadja az e-mail-üzenet aljához hozzáfűzni kívánt szöveges üzenetet. Ebben a példában a [sendgrid-php könyvtárat]fogjuk használni.

A [zeneszerző] használata a könyvtár telepítéséhez:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Példa  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, kövesse az alábbi hivatkozásokat további információért.

* SendGrid dokumentációja: <https://sendgrid.com/docs>
* SendGrid PHP-könyvtár: <https://github.com/sendgrid/sendgrid-php>
* SendGrid Speciális ajánlat az Azure-ügyfelek számára: <https://sendgrid.com/windowsazure.html>

További információ: a [php fejlesztői központ](https://azure.microsoft.com/develop/php/)is.

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[felhőalapú e-mail szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail kézbesítés]: https://sendgrid.com/transactional-email
[sendgrid – php-könyvtár]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
