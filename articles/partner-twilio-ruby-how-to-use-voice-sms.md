---
title: A Twilio használata a Voice és az SMS (Ruby) szolgáltatáshoz | Microsoft Docs
description: Megtudhatja, hogyan készíthet telefonhívást, és hogyan küldhet SMS-üzenetet a Twilio API szolgáltatással az Azure-ban. A Rubyban írt kód-minták.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 49203195bf7746d0bff1b9543d1641f69ab23359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95542677"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>A Twilio használata a hang-és SMS-funkciókhoz a Rubyban
Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure Twilio API szolgáltatásával. A tárgyalt forgatókönyvek közé tartozik a telefonhívás kezdeményezése és egy rövid üzenetküldési szolgáltatás (SMS) üzenet küldése. A Twilio és a hang-és SMS-alkalmazások alkalmazásokban való használatáról további információt a [következő lépések](#NextSteps) című szakaszban talál.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio egy telefonos webszolgáltatási API, amely lehetővé teszi a meglévő webes nyelvek és készségek használatát a hangalapú és SMS-alkalmazások készítéséhez. A Twilio egy harmadik féltől származó szolgáltatás (nem Azure-szolgáltatás, nem pedig Microsoft-termék).

A **Twilio Voice** lehetővé teszi az alkalmazások számára telefonhívások készítését és fogadását. A **TWILIO SMS** lehetővé teszi az alkalmazások számára az SMS-üzenetek készítését és fogadását. A **Twilio-ügyfél** lehetővé teszi, hogy alkalmazásai lehetővé tegyék a hangalapú kommunikációt a meglévő internetkapcsolatok, például a mobil kapcsolatok használatával.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>A Twilio díjszabása és különleges ajánlataink
A Twilio díjszabásával kapcsolatos információk a [Twilio díjszabásában][twilio_pricing]érhetők el. Az Azure-ügyfelek [különleges ajánlatot][special_offer]kapnak: 1000-es vagy 1000-os ingyenes kredit Az ajánlatra való feliratkozáshoz vagy további információért látogasson el ide [https://ahoy.twilio.com/azure][special_offer] .  

## <a name="concepts"></a><a id="Concepts"></a>Alapelvek
A Twilio API egy REST-alapú API, amely hang-és SMS-funkciókat biztosít az alkalmazásokhoz. Az ügyféloldali kódtárak több nyelven is elérhetők; a listákat lásd: [TWILIO API-kódtárak][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
A TwiML olyan XML-alapú utasításokat tartalmaz, amelyek tájékoztatják a Twilio a hívások vagy SMS-üzenetek feldolgozásáról.

Példaként a következő TwiML konvertálja a szöveget **Hello World** beszédre.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Minden TwiML-dokumentum `<Response>` legfelső szintű eleme. Innentől kezdve a Twilio-műveletek használatával határozhatja meg az alkalmazás viselkedését.

### <a name="twiml-verbs"></a><a id="Verbs"></a>TwiML-műveletek
A Twilio-műveletek olyan XML-címkék, amelyek elmondják **a Twilio.** Például a **&lt; Say &gt;** művelet arra utasítja a Twilio, hogy hallhatóan kézbesítse az üzenetet egy híváson. 

A következő lista az Twilio-műveletek listáját tartalmazza.

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

A Twilio-műveletekkel, azok attribútumaival és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll egy Twilio-fiók beszerzésére, regisztráljon a [Twilio kipróbálása][try_twilio]gombra. Elindíthat egy ingyenes fiókot, és később is frissítheti a fiókját.

Amikor regisztrál egy Twilio-fiókra, ingyenes telefonszámot kap az alkalmazáshoz. A fiók biztonsági azonosítóját és hitelesítési jogkivonatát is megkapja. Mindkettőre szükség lesz a Twilio API-hívások létrehozásához. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében őrizze meg biztonságos hitelesítési tokenjét. A fiók biztonsági azonosítójának és hitelesítési jogkivonatának megtekinthető a [Twilio fiók lapján][twilio_account], a **fiók SID** és **hitelesítési jogkivonat** mezőjében.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Telefonszámok ellenőrzése
A Twilio által megadott számon túl ellenőrizheti, hogy az Ön által használt számokat (azaz a mobiltelefont vagy az otthoni telefonszámot) is megtekintheti az alkalmazásokban való használatra. 

A telefonszámok ellenőrzéséről a [számok kezelése][verify_phone]című témakörben olvashat bővebben.

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Ruby-alkalmazás létrehozása
A Twilio szolgáltatást használó Ruby-alkalmazás, amely az Azure-ban fut, nem más, mint bármely más Ruby-alkalmazás, amely a Twilio szolgáltatást használja. Habár a Twilio-szolgáltatások megpihennek, és számos módon hívhatók a Rubyban, ez a cikk a Twilio-szolgáltatások a [Rubyhoz készült Twilio Helper Library][twilio_ruby]használatával történő használatát ismerteti.

Először [állítson be egy új Azure linuxos virtuális gépet][azure_vm_setup] , amely az új Ruby-webalkalmazáshoz tartozó gazdagépként működik. Hagyja figyelmen kívül a Rails-alkalmazás létrehozásával kapcsolatos lépéseket, csak állítsa be a virtuális gépet. Győződjön meg arról, hogy létrehoz egy 80-es külső portot és egy 5000-es belső portot tartalmazó végpontot.

Az alábbi példákban az [Sinatra][sinatra]és a Ruby egy egyszerű webes keretrendszerét fogjuk használni. De természetesen használhatja a Ruby-hez készült Twilio Helper-függvénytárat bármilyen más webes keretrendszerrel, beleértve a Ruby on Rails szolgáltatást is.

SSH-t az új virtuális gépre, és hozzon létre egy könyvtárat az új alkalmazáshoz. A könyvtáron belül hozzon létre egy Gemfile nevű fájlt, és másolja a következő kódot:

```bash
source 'https://rubygems.org'
gem 'sinatra'
gem 'thin'
```

A parancssorban futtassa a parancsot `bundle install` . Ez a fenti függőségeket fogja telepíteni. Ezután hozzon létre egy nevű fájlt `web.rb` . Ez lesz a webalkalmazáshoz tartozó kód. Illessze be a következő kódot:

```ruby
require 'sinatra'

get '/' do
    "Hello Monkey!"
end
```

Ezen a ponton el kell tudnia érni a parancsot `ruby web.rb -p 5000` . Ez egy kisméretű webkiszolgálót hoz létre a 5000-as porton. Az Azure-beli virtuális géphez beállított URL-cím megkeresésével böngészhet a böngészőben az alkalmazásban. Ha elérheti a webalkalmazást a böngészőben, készen áll a Twilio-alkalmazások létrehozásának megkezdésére.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Az alkalmazás konfigurálása a Twilio használatára
A webalkalmazást úgy is beállíthatja, hogy a Twilio-függvénytárat használja a következő `Gemfile` sor belefoglalásával:

```bash
gem 'twilio-ruby'
```

A parancssorban futtassa a parancsot `bundle install` . Most nyissa meg és válassza ki `web.rb` a következő sort:

```ruby
require 'twilio-ruby'
```

Most már készen áll a Ruby Twilio segítő könyvtárának használatára a webalkalmazásban.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: kimenő hívás létrehozása
A következő ábrán látható, hogyan lehet kimenő hívást kezdeményezni. A legfontosabb fogalmak közé tartozik az Twilio Helper Library for Ruby, amely REST API hívásokat és renderelési TwiML tesz elérhetővé. Helyettesítse be a **Feladó** és **a** telefonszám értékeit, és győződjön meg arról, hogy a kód futtatása előtt ellenőrizze a Twilio **-** fiók telefonszámát.

Adja hozzá ezt a függvényt a következőhöz `web.md` :

```ruby
# Set your account ID and authentication token.
sid = "your_twilio_account_sid";
token = "your_twilio_authentication_token";

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from = "NNNNNNNNNNN";

# The number of the phone receiving call.
to = "NNNNNNNNNNN";

# Use the Twilio-provided site for the TwiML response.
url = "http://yourdomain.cloudapp.net/voice_url";

get '/make_call' do
    # Create the call client.
    client = Twilio::REST::Client.new(sid, token);

    # Make the call
    client.account.calls.create(to: to, from: from, url: url)
end

post '/voice_url' do
    "<Response>
        <Say>Hello Monkey!</Say>
    </Response>"
end
```

Ha egy böngészőben nyitja meg a programot, a elindítja a hívást a Twilio API-nak a telefonhívás meghívásához `http://yourdomain.cloudapp.net/make_call` . Az első két paraméter `client.account.calls.create` meglehetősen magától értetődő: a hívás száma `from` és a hívás száma `to` . 

A harmadik paraméter ( `url` ) az az URL-cím, amellyel a Twilio kéri, hogy milyen lépéseket kell tennie a hívás csatlakoztatása után. Ebben az esetben olyan URL-címet () állítunk be `http://yourdomain.cloudapp.net` , amely egy egyszerű TwiML-dokumentumot ad vissza, és a `<Say>` művelettel elvégzi a szöveg és a beszéd megjelenítését, és a "Hello Monkey" kifejezést használja a hívást fogadó személynek.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Útmutató: SMS-üzenet fogadása
Az előző **példában a bejövõ telefonhívást** kezdeményezték. Ezúttal használjuk azt a telefonszámot, amelyet a Twilio adott a regisztráció során a **beérkező** SMS-üzenetek feldolgozásához.

Először jelentkezzen be a Twilio- [irányítópultra][twilio_account]. Kattintson a "Numbers" (számok) elemre a felső navigációs panelen, majd kattintson a megadott Twilio-számra. Két konfigurálható URL-cím jelenik meg. Egy hangkérelem URL-címe és egy SMS-kérelem URL-címe. Ezek azok az URL-címek, amelyeket a Twilio hív meg, vagy SMS-t küld a számnak. Az URL-címeket "webhookoknak" is nevezzük.

Szeretnénk feldolgozni a beérkező SMS-üzeneteket, ezért frissítsük az URL-címet a következőre: `http://yourdomain.cloudapp.net/sms_url` . A lap alján kattintson a módosítások mentése gombra. Most térjünk vissza a `web.rb` programba, hogy kezelje az alkalmazást:

```ruby
post '/sms_url' do
    "<Response>
        <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
    </Response>"
end
```

A módosítás elvégzése után indítsa újra a webalkalmazást. Most vegye fel a telefont, és küldjön SMS-t a Twilio-számra. Azonnal küldjön egy SMS-választ, amely szerint a "Hey, thanks for the ping!" üzenet jelenik meg. Twilio és az Azure rock! ".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Útmutató: további Twilio-szolgáltatások használata
Az itt bemutatott példákon kívül a Twilio webalapú API-kat kínál, amelyek segítségével további Twilio funkciókat alkalmazhat az Azure-alkalmazásból. További részletekért tekintse meg a [TWILIO API dokumentációját][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>További lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozásokat, ahol további információt talál:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Twilio-útmutatók és-példa kódja][twilio_howtos]
* [Twilio gyors útmutatók][twilio_quickstarts] 
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio-támogatásról][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: /previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app