---
title: A Twilio használata a Voice és az SMS (Python) használatához | Microsoft Docs
description: Megtudhatja, hogyan készíthet telefonhívást, és hogyan küldhet SMS-üzenetet a Twilio API szolgáltatással az Azure-ban. Pythonban írt példák a kódokra.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: a9d6ec6aa5f30af04bf64e238f4a25ec6aef4f5d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504753"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>A Twilio használata a hang-és SMS-funkciókhoz a Pythonban
Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure Twilio API szolgáltatásával. A tárgyalt forgatókönyvek közé tartozik a telefonhívás kezdeményezése és egy rövid üzenetküldési szolgáltatás (SMS) üzenet küldése. A Twilio és a hang-és SMS-alkalmazások alkalmazásokban való használatáról további információt a [következő lépések](#NextSteps) című szakaszban talál.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio az üzleti kommunikáció jövőjét fejleszti, és lehetővé teszi, hogy a fejlesztők hang-, VoIP-és üzenetkezelési adatokat ágyaznak be alkalmazásokba. Minden szükséges infrastruktúra virtualizálása egy felhőalapú, globális környezetben, amely a Twilio Communications API platformon keresztül teszi elérhetővé. Az alkalmazások egyszerűen létrehozhatók és méretezhetők. Az utólagos elszámolású díjszabás és a Felhőbeli megbízhatóság előnyeit élvezheti.

A **Twilio Voice** lehetővé teszi az alkalmazások számára telefonhívások készítését és fogadását.
A **TWILIO SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon.
A **Twilio-ügyfél** lehetővé teszi, hogy bármilyen telefonról, táblaszámítógépről vagy böngészőből kezdeményezheti a VoIP-hívásokat, és támogatja a WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>A Twilio díjszabása és különleges ajánlataink
Az Azure-ügyfelek a Twilio-fiók frissítésekor a Twilio-kreditek $10-es [különleges ajánlatát][special_offer] kapják meg. Ez a Twilio-kredit bármely Twilio-felhasználásra alkalmazható ($10 kredit, amely akár több mint 1 000 SMS-üzenetet küld, akár 1000 bejövő hang-percet is igénybe vehet, a telefonszám és az üzenet vagy a hívás célhelyének helyétől függően). Váltsa be ezt a [Twilio-kreditet][special_offer] , és kezdje el.

A Twilio az utólagos elszámolású szolgáltatás. Nincsenek beállított díjak, és bármikor lezárhatja a fiókját. További részleteket a [Twilio díjszabását][twilio_pricing]ismertető webhelyen talál.

## <a name="concepts"></a><a id="Concepts"></a>Alapelvek
A Twilio API egy REST-alapú API, amely hang-és SMS-funkciókat biztosít az alkalmazásokhoz. Az ügyféloldali kódtárak több nyelven is elérhetők; a listákat lásd: [TWILIO API-kódtárak][twilio_libraries].

A Twilio API legfontosabb szempontjai a Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-műveletek
Az API a Twilio-műveletek használatát teszi lehetővé; például a **&lt; Say &gt;** művelet arra utasítja a Twilio, hogy hallhatóan kézbesítse az üzenetet egy híváson.

A következő lista az Twilio-műveletek listáját tartalmazza. Ismerje meg a többi műveletet és képességeket a [Twilio Markup Language dokumentációján][twiml]keresztül.

* **&lt; Dial &gt;**: a hívó csatlakoztatása egy másik telefonhoz.
* Összegyűjtése: a telefon billentyűzetén megadott numerikus számjegyeket gyűjti. **&lt; &gt;**
* **&lt; Vonalbontás &gt;**: egy hívást ér véget.
* **&lt; Szüneteltetés &gt;**: csendes várakozás a megadott számú másodpercre.
* **&lt; Lejátszás &gt;**: hangfájl lejátszása.
* **&lt; Üzenetsor &gt;**: adja hozzá a-t a hívók várólistához.
* **&lt; Rekord &gt;**: a hívó hangját rögzíti, és egy, a rögzítést tartalmazó fájl URL-címét adja vissza.
* **&lt; Átirányítás &gt;**: egy hívás vagy SMS átadása a TWIML egy másik URL-címen keresztül.
* **&lt; Elutasítás &gt;**: elutasítja a Twilio-szám bejövő hívását a számlázás nélkül.
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

Amikor az alkalmazás meghívja a Twilio API-t, az egyik API-paraméter az a URL-cím, amely a TwiML választ adja vissza. A Twilio által biztosított URL-címek segítségével megadhatja az alkalmazások által használt TwiML-válaszokat. Saját URL-címeket is tárolhat a TwiML-válaszok létrehozásához, és egy másik lehetőség az objektum használata `TwiMLResponse` .

A Twilio-műveletekkel, azok attribútumaival és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll egy Twilio-fiók beszerzésére, regisztráljon a [Twilio kipróbálása][try_twilio]gombra. Elindíthat egy ingyenes fiókot, és később is frissítheti a fiókját.

Amikor regisztrál egy Twilio-fiókra, megkapja a fiók biztonsági azonosítóját és egy hitelesítési jogkivonatot. Mindkettőre szükség lesz a Twilio API-hívások létrehozásához. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében őrizze meg biztonságos hitelesítési tokenjét. A fiók biztonsági azonosítója és hitelesítési jogkivonata a [Twilio-konzolon][twilio_console], a **fiók SID** és **hitelesítési jogkivonat** mezőjében látható.

## <a name="create-a-python-application"></a><a id="create_app"></a>Python-alkalmazás létrehozása
A Twilio szolgáltatást használó és az Azure-ban futó Python-alkalmazások nem különböznek a Twilio szolgáltatást használó más Python-alkalmazástól. Habár a Twilio-szolgáltatások REST-alapúak, és a Python számos módon hívhatók meg, ez a cikk a Twilio-szolgáltatások a [githubról a Pythonhoz készült Twilio-kódtár][twilio_python]használatával történő használatát ismerteti. További információ a Pythonhoz készült Twilio-függvénytár használatáról: [https://www.twilio.com/docs/libraries/python][twilio_lib_docs] .

Először [állítson be egy új Azure linuxos virtuális gépet][azure_vm_setup] , amely az új Python-webalkalmazáshoz tartozó gazdagépként működik. A virtuális gép futása után közzé kell tennie az alkalmazást egy nyilvános porton az alább leírtak szerint.

### <a name="add-an-incoming-rule"></a>Bejövő szabály hozzáadása
  1. Nyissa meg a [hálózati biztonsági csoport][azure_nsg] lapot.
  2. Válassza ki azt a hálózati biztonsági csoportot, amely megfelel a virtuális gépnek.
  3. Adjon hozzá egy **kimenő szabályt** a 80-es **porthoz**. Ügyeljen arra, hogy bármely címről engedélyezze a bejövő adatforrást.

### <a name="set-the-dns-name-label"></a>A DNS-név címke beállítása
  1. Lépjen a [nyilvános IP-címek][azure_ips] lapra.
  2. Válassza ki azt a nyilvános IP-címet, amely megfelel a virtuális gépnek.
  3. Állítsa be a **DNS-név címkéjét** a **konfiguráció** szakaszban. Ebben a példában a következőhöz hasonlóan fog kinézni: *a-domain-Label*. CentralUS.cloudapp.Azure.com

Ha SSH-kapcsolaton keresztül tud csatlakozni a virtuális géphez, telepítheti az Ön által választott webes keretrendszert (a két legismertebb Pythonban a [lombikot](http://flask.pocoo.org/) és a [Django](https://www.djangoproject.com)is). Ezek közül bármelyiket telepítheti a parancs futtatásával `pip install` .

Ne feledje, hogy a virtuális gépet úgy konfiguráltuk, hogy csak a 80-es porton engedélyezze a forgalmat. Ügyeljen arra, hogy az alkalmazást a port használatára konfigurálja.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Az alkalmazás konfigurálása Twilio-kódtárak használatára
Az alkalmazás két módon konfigurálható a Pythonhoz készült Twilio-függvénytár használatára:

* Telepítse a Python Twilio-kódtárat pip-csomagként. A következő parancsokkal telepíthető:
   
  `$ pip install twilio`

    -VAGY-

* Töltse le a Pythonhoz készült Twilio-kódtárat a GitHubról ( [https://github.com/twilio/twilio-python][twilio_python] ), és telepítse a következőhöz hasonló módon:

  `$ python setup.py install`

Miután telepítette a Pythonhoz készült Twilio-kódtárat, a `import` Python-fájlokban is elvégezheti a következőket:

  `import twilio`

További információ: [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: kimenő hívás létrehozása
A következő ábrán látható, hogyan lehet kimenő hívást kezdeményezni. Ez a kód egy Twilio által biztosított helyet is használ a Twilio Markup Language (TwiML) válaszának visszaadásához. Helyettesítse be az értékeket a **from_number** és **to_number** a telefonszámokat, és ellenőrizze, hogy a kód futtatása előtt ellenőrizte-e a Twilio-fiók **from_number** telefonszámát.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> A telefonszámokat "+" és "országkód" formátumban kell formázni. Például: + 16175551212 (E. 164 formátum). A Twilio nem formázott US-számokat is elfogad. Például: (415) 555-1212 vagy 415-555-1212.

Ahogy említettük, ez a kód egy Twilio által biztosított helyet használ a TwiML válasz visszaadásához. Ehelyett használhatja a saját webhelyét, hogy megadja a TwiML választ; További információkért lásd: [TwiML-válaszok megadása a saját webhelyéről](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Útmutató: SMS-üzenet küldése
A következő bemutatja, hogyan küldhet SMS-üzenetet a `TwilioRestClient` osztály használatával. Az SMS-üzenetek küldéséhez a Twilio által megadott **from_number** számot kell megadnia a próbaverziós fiókok számára. A kód futtatása előtt a **to_number** számot ellenőrizni kell a Twilio-fiókhoz.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Útmutató: TwiML-válaszok megadása saját webhelyről
Amikor az alkalmazás kezdeményez egy hívást a Twilio API-hoz, a Twilio egy olyan URL-címre küldi a kérést, amely egy TwiML-válasz visszaadására vár. A fenti példa a Twilio által megadott URL-címet használja [https://twimlets.com/message][twimlet_message_url] . (Míg a TwiML a Twilio általi használatra készült, megtekintheti a böngészőben. Ha például egy [https://twimlets.com/message][twimlet_message_url] üres elemet szeretne megtekinteni, `<Response>` egy másik példaként kattintson ide [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] egy `<Response>` elemet tartalmazó elem megjelenítéséhez `<Say>` .)

Ahelyett, hogy a Twilio által megadott URL-címet kellene megadnia, létrehozhat egy saját helyet, amely visszaadja a HTTP-válaszokat. A helyet bármilyen nyelven létrehozhatja, amely az XML-válaszokat adja vissza; Ez a témakör azt feltételezi, hogy a Python használatával hozza létre a TwiML.

Az alábbi példákban egy TwiML választ fog kiadni, amely a híváson **Hello World** .

Lombiktal:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

A Django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Ahogy a fenti példában is látható, a TwiML válasz egyszerűen egy XML-dokumentum. A Pythonhoz készült Twilio-könyvtár olyan osztályokat tartalmaz, amelyek a TwiML-t fogják előállítani. Az alábbi példa a fentiekben látható egyenértékű választ állítja elő, de a `twiml` Pythonhoz készült Twilio-kódtár modulját használja:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

További információ a TwiML: [https://www.twilio.com/docs/api/twiml][twiml_reference] .

Ha már beállította a Python-alkalmazást a TwiML-válaszok biztosításához, használja az alkalmazás URL-címét a metódusnak átadott URL-címként `client.calls.create`  . Ha például egy **MyTwiML** nevű webalkalmazást telepít egy Azure-beli üzemeltetett szolgáltatásra, az URL-címet webhookként használhatja az alábbi példában látható módon:

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Útmutató: további Twilio-szolgáltatások használata
Az itt bemutatott példákon kívül a Twilio webalapú API-kat kínál, amelyek segítségével további Twilio funkciókat alkalmazhat az Azure-alkalmazásból. További részletekért tekintse meg a [TWILIO API dokumentációját][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>További lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozásokat, ahol további információt talál:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Twilio útmutatók és mintakód][twilio_howtos]
* [Twilio gyors útmutatók][twilio_quickstarts]
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio-támogatásról][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[azure_ips]: https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal
[azure_nsg]: https://docs.microsoft.com/azure/virtual-network/manage-network-security-group
