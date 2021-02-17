---
title: A Twilio használata a Voice és az SMS (Java) szolgáltatáshoz | Microsoft Docs
description: Megtudhatja, hogyan készíthet telefonhívást, és hogyan küldhet SMS-üzenetet a Twilio API szolgáltatással az Azure-ban. A Java nyelven írt példák.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: e7ff925534a9cab6523097f84a8ae199e9cb83eb
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548726"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>A Twilio használata a hang-és SMS-funkciókhoz Java-ban
Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure Twilio API szolgáltatásával. A tárgyalt forgatókönyvek közé tartozik a telefonhívás kezdeményezése és egy rövid üzenetküldési szolgáltatás (SMS) üzenet küldése. A Twilio és a hang-és SMS-alkalmazások alkalmazásokban való használatáról további információt a [következő lépések](#NextSteps) című szakaszban talál.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio egy telefonos webszolgáltatási API, amely lehetővé teszi a meglévő webes nyelvek és készségek használatát a hangalapú és SMS-alkalmazások készítéséhez. A Twilio egy harmadik féltől származó szolgáltatás (nem Azure-szolgáltatás, nem pedig Microsoft-termék).

A **Twilio Voice** lehetővé teszi az alkalmazások számára telefonhívások készítését és fogadását. A **TWILIO SMS** lehetővé teszi az alkalmazások számára az SMS-üzenetek készítését és fogadását. A **Twilio-ügyfél** lehetővé teszi, hogy alkalmazásai lehetővé tegyék a hangalapú kommunikációt a meglévő internetkapcsolatok, például a mobil kapcsolatok használatával.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>A Twilio díjszabása és különleges ajánlataink
A Twilio díjszabásával kapcsolatos információk a [Twilio díjszabásában][twilio_pricing]érhetők el. Az Azure-ügyfelek [különleges ajánlatot][special_offer]kapnak: 1000-es vagy 1000-os ingyenes kredit Az ajánlatra való feliratkozáshoz vagy további információért látogasson el ide [https://ahoy.twilio.com/azure][special_offer] .

## <a name="concepts"></a><a id="Concepts"></a>Alapelvek
A Twilio API egy REST-alapú API, amely hang-és SMS-funkciókat biztosít az alkalmazásokhoz. Az ügyféloldali kódtárak több nyelven is elérhetők; a listákat lásd: [TWILIO API-kódtárak][twilio_libraries].

A Twilio API legfontosabb szempontjai a Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-műveletek
Az API a Twilio-műveletek használatát teszi lehetővé; például a **&lt; Say &gt;** művelet arra utasítja a Twilio, hogy hallhatóan kézbesítse az üzenetet egy híváson.

A következő lista az Twilio-műveletek listáját tartalmazza.

* **&lt; Dial &gt;**: a hívó csatlakoztatása egy másik telefonhoz.
* Összegyűjtése: a telefon billentyűzetén megadott numerikus számjegyeket gyűjti. **&lt; &gt;**
* **&lt; Vonalbontás &gt;**: egy hívást ér véget.
* **&lt; Lejátszás &gt;**: hangfájl lejátszása.
* **&lt; Üzenetsor &gt;**: adja hozzá a-t a hívók várólistához.
* **&lt; Szüneteltetés &gt;**: csendes várakozás a megadott számú másodpercre.
* **&lt; Rekord &gt;**: a hívó hangját rögzíti, és egy, a rögzítést tartalmazó fájl URL-címét adja vissza.
* **&lt; Átirányítás &gt;**: egy hívás vagy SMS átadása a TWIML egy másik URL-címen keresztül.
* **&lt; Elutasítás &gt;**: elutasítja a Twilio-szám bejövő hívását a számlázás nélkül.
* **&lt; Tegyük &gt;** fel, hogy a szöveget a híváson végzett beszédre alakítja át.
* **&lt; SMS &gt;**: SMS-üzenet küldése.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
A TwiML XML-alapú utasításokat tartalmaz a Twilio műveletek alapján, amelyek tájékoztatják a Twilio, hogy hogyan dolgozzák fel a hívást vagy az SMS-t.

Példaként a következő TwiML konvertálja a szöveget **Hello World!** a beszédhez.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Amikor az alkalmazás meghívja a Twilio API-t, az egyik API-paraméter az a URL-cím, amely a TwiML választ adja vissza. A Twilio által biztosított URL-címek segítségével megadhatja az alkalmazások által használt TwiML-válaszokat. Saját URL-címeket is tárolhat a TwiML-válaszok létrehozásához, és egy másik lehetőség a **TwiMLResponse** objektum használata.

A Twilio-műveletekkel, azok attribútumaival és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll egy Twilio-fiók beszerzésére, regisztráljon a [Twilio kipróbálása][try_twilio]gombra. Elindíthat egy ingyenes fiókot, és később is frissítheti a fiókját.

Amikor regisztrál egy Twilio-fiókra, megkapja a fiók AZONOSÍTÓját és a hitelesítési jogkivonatot. Mindkettőre szükség lesz a Twilio API-hívások létrehozásához. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében őrizze meg biztonságos hitelesítési tokenjét. A fiók azonosítója és a hitelesítési token a Twilio- [konzolon][twilio_console], a **fiók SID** és **hitelesítési jogkivonat** mezőjében látható.

## <a name="create-a-java-application"></a><a id="create_app"></a>Java-alkalmazás létrehozása
1. Szerezze be a Twilio JAR-t, és adja hozzá a Java Build elérési útjához és a háborús üzembe helyezési szerelvényhez. A [https://github.com/twilio/twilio-java][twilio_java] -ben letöltheti a GitHub-forrásokat, és létrehozhat saját jar-t, vagy letöltheti az előre elkészített jar-t (függőségekkel vagy anélkül).
2. Győződjön meg arról, hogy a JDK **hitesítésszolgáltatói** -tárolója tartalmazza a Equifax biztonságos hitelesítésszolgáltatói tanúsítványát MD5 ujjlenyomattal 67: CB: 9D: C0:13:24:8a: 82:9b: B2:17:1e: D1:1b: ec: D4 (a sorozatszám 35: de: F4: CF és az SHA1 ujjlenyomata D2:32:09: ad: 23: d3:14:23:21:74: E4:0D: 7F: 9D: 62:13:97:86:63:3a). Ez a hitelesítésszolgáltató (CA) tanúsítványa a [https://api.twilio.com][twilio_api_service] szolgáltatáshoz, amelyet a rendszer Twilio API-k használatakor hív meg.

A Java-Twilio ügyféloldali kódtár használatának részletes utasításait az [Azure-beli Java-alkalmazások Twilio használatával hívhatják meg][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Az alkalmazás konfigurálása Twilio-kódtárak használatára
A kódban az alkalmazásban használni kívánt Twilio-csomagok vagy osztályok számára is hozzáadhat **importálási** utasításokat.

Java-forrásfájlok esetén:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Java Server Page (JSP) forrásfájlok esetén:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
```

Attól függően, hogy milyen Twilio-csomagokat vagy-osztályokat kíván használni, az **importálási** utasítások eltérőek lehetnek.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: kimenő hívás létrehozása
Az alábbiakban bemutatjuk, hogyan lehet kimenő hívást kezdeményezni a **Call** osztály használatával. Ez a kód egy Twilio által biztosított helyet is használ a Twilio Markup Language (TwiML) válaszának visszaadásához. Helyettesítse be a **Feladó** és **a** telefonszám értékeit, és győződjön meg arról, hogy a kód futtatása előtt ellenőrizze a Twilio **-** fiók telefonszámát.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

További információ a **Call. Creator** metódusnak átadott paraméterekről: [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Ahogy említettük, ez a kód egy Twilio által biztosított helyet használ a TwiML válasz visszaadásához. Ehelyett használhatja a saját webhelyét, hogy megadja a TwiML választ; További információkért lásd: [TwiML-válaszok megadása egy Java-alkalmazásban az Azure-ban](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Útmutató: SMS-üzenet küldése
A következő bemutatja, hogyan küldhet SMS-üzenetet az **üzenet** osztály használatával. Az **SMS-üzenetek** küldéséhez a Twilio által biztosított **4155992671**-as számú próbaverziót kell megadnia. A számot a kód futtatása előtt ellenőrizni kell a Twilio **-** fiókhoz.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

További információ az **üzenet. Creator** metódusban átadott paraméterekről: [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms] .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Útmutató: TwiML-válaszok megadása saját webhelyről
Amikor az alkalmazás kezdeményez egy hívást a Twilio API-ra, például a **CallCreator. Create** metódussal, a Twilio elküldi a kérést egy olyan URL-címre, amely egy TwiML-válasz visszaadására vár. A fenti példa a Twilio által megadott URL-címet használja [https://twimlets.com/message][twimlet_message_url] . (Noha a TwiML webszolgáltatások számára készült, a böngészőben megtekintheti a TwiML. Ha például egy [https://twimlets.com/message][twimlet_message_url] üres **&lt; válaszüzenetet &gt;** szeretne megtekinteni, kattintson ide egy olyan [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] **&lt; &gt; Válasz** elem megjelenítéséhez, amely tartalmaz egy **&lt; Say &gt;** elemet.)

Ahelyett, hogy a Twilio által megadott URL-címet kellene megadnia, létrehozhat egy saját URL-helyet, amely a HTTP-válaszokat adja vissza. A helyet bármilyen nyelven létrehozhatja, amely a HTTP-válaszokat adja vissza; Ez a témakör feltételezi, hogy az URL-címet egy JSP-lapon fogja üzemeltetni.

A következő JSP-oldal egy TwiML választ eredményez, amely a **Hello Worldt mondja!** a híváson.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A következő JSP-lap egy olyan TwiML-választ eredményez, amely egy bizonyos szöveget tartalmaz, több szüneteltetéssel rendelkezik, és a Twilio API-verzióval és az Azure-szerepkör nevével kapcsolatos információkat jeleníti meg.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

A **ApiVersion** paraméter a Twilio hangkérések (nem SMS-kérelmek) esetében érhető el. A hang-és SMS-kérelmek Twilio elérhető kérelmek paramétereinek megtekintéséhez lásd: <https://www.twilio.com/docs/api/twiml/twilio_request> és <https://www.twilio.com/docs/api/twiml/sms/twilio_request> . A **RoleName** környezeti változó egy Azure-telepítés részeként érhető el. (Ha egyéni környezeti változókat szeretne hozzáadni, hogy azok a **System. GETENV** használatával is felvehetők legyenek, tekintse meg a környezeti változók szakaszt a [különböző szerepkörök konfigurációs beállításainál][misc_role_config_settings].)

Miután beállította a JSP-lapot a TwiML-válaszok megadására, használja a JSP-oldal URL-címét a **Call. Creator** metódusnak átadott URL-címként. Ha például egy MyTwiML nevű webalkalmazást helyez üzembe egy Azure által üzemeltetett szolgáltatásban, és a JSP-oldal neve mytwiml.jsp, az URL-cím átadható a **Call. creatornak** , ahogyan az a következő képen látható:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Egy másik lehetőség, hogy válaszoljon a TwiML a **VoiceResponse** osztályon keresztül, amely a **com. twilio. TwiML** csomagban érhető el.

További információ a Twilio az Azure-ban Java használatával történő használatáról: [telefonhívás kezdeményezése a Twilio használatával egy Java-alkalmazásban az Azure-ban][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Útmutató: további Twilio-szolgáltatások használata
Az itt bemutatott példákon kívül a Twilio webalapú API-kat kínál, amelyek segítségével további Twilio funkciókat alkalmazhat az Azure-alkalmazásból. További részletekért tekintse meg a [TWILIO API dokumentációját][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>További lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozásokat, ahol további információt talál:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Twilio HowTo és example kód][twilio_howtos]
* [Twilio gyors útmutatók][twilio_quickstarts]
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio-támogatásról][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
