---
title: Az Azure Event Hubshoz való hozzáférés hitelesítése közös hozzáférési aláírásokkal
description: Ez a cikk bemutatja, hogyan hitelesítheti Event Hubs erőforrásokhoz való hozzáférést közös hozzáférési aláírások használatával.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986495"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Hozzáférés hitelesítése Event Hubs erőforrásokhoz megosztott hozzáférési aláírások (SAS) használatával
A közös hozzáférésű aláírás (SAS) részletesen szabályozza az Ön által a közös hozzáférési aláírással rendelkező ügyfelek számára biztosított hozzáférés típusát. Íme néhány, a SAS-ben beállítható vezérlő: 

- Az az intervallum, ameddig az SAS érvényes, beleértve a kezdési időt és a lejárati időt.
- Az SAS által megadott engedélyek. Egy Event Hubs-névtér SAS-je például megadhatja a figyelési engedélyt, a küldési engedélyt azonban nem.
- Csak az érvényes hitelesítő adatokat tartalmazó ügyfelek küldhetnek adatokat az Event hub-nak.
- Az ügyfél nem tud megszemélyesíteni egy másik ügyfelet.
- Egy támadó ügyfél blokkolható az adatoknak az Event hub szolgáltatásba való küldéséhez.

Ez a cikk a Event Hubs erőforrásokhoz való hozzáférésnek a SAS használatával történő hitelesítését ismerteti. Ha többet szeretne megtudni a Event Hubs erőforrásokhoz való hozzáférés **engedélyezéséről** az SAS használatával, tekintse meg [ezt a cikket](authorize-access-shared-access-signature.md). 

> [!NOTE]
> A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait a közös hozzáférésű aláírások használata helyett ajánlott biztonsági eljárásként használni, ami könnyebben feltörhető lehet. Habár továbbra is használhatja a közös hozzáférésű aláírásokat (SAS) a Event Hubs erőforrásaihoz való részletes hozzáférés biztosításához, az Azure AD hasonló képességeket kínál anélkül, hogy SAS-tokeneket kellene kezelnie, vagy nem kell aggódnia a sérült SAS visszavonása miatt.
> 
> További információ az Azure AD-integrációról az Azure Event Hubsban: [hozzáférés engedélyezése Event Hubs az Azure ad](authorize-access-azure-active-directory.md)-vel. 


## <a name="configuring-for-sas-authentication"></a>Konfigurálás SAS-hitelesítéshez
Konfigurálhatja a EventHubs megosztott hozzáférés-engedélyezési szabályát egy Event Hubs névtérben vagy egy entitáson (Event hub-példányon vagy Kafka-témakörben). A megosztott hozzáférés engedélyezési szabályának konfigurálása egy fogyasztói csoportban jelenleg nem támogatott, de a névtérben vagy entitásban konfigurált szabályokkal biztonságossá teheti a felhasználói csoportokhoz való hozzáférést. 

Az alábbi képen látható, hogyan vonatkoznak az engedélyezési szabályok a minta entitásokra. 

![Engedélyezési szabály konfigurálása](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

Ebben a példában a minta Event Hubs névtér (ExampleNamespace) két entitással rendelkezik: a EH1 és a topic1. Az engedélyezési szabályok az entitás szintjén és a névtér szintjén is definiálva vannak.  

A manageRuleNS, a sendRuleNS és a listenRuleNS engedélyezési szabályok az Event hub-példány EH1 és a T1-es témakörre egyaránt érvényesek. A listenRule-EH és a sendRule-eh engedélyezési szabályok csak az Event hub-példány EH1 vonatkoznak, és a sendRuleT-engedélyezési szabály csak a témakör topic1 vonatkozik. 

SendRuleNS-engedélyezési szabály használatakor az ügyfélalkalmazások a EH1 és a topic1 is küldhetnek. A sendRuleT-engedélyezési szabály használatakor a rendszer csak a topic1 részletes hozzáférését kényszeríti ki, ezért az ezt a szabályt használó ügyfélalkalmazások nem küldhetnek el EH1, hanem csak topic1.

## <a name="generate-a-shared-access-signature-token"></a>Közös hozzáférésű aláírási jogkivonat létrehozása 
Minden olyan ügyfél létrehozhat egy SAS-jogkivonatot, amely egy engedélyezési szabály nevének és az egyik aláíró kulcsnak a nevéhez fér hozzá. A jogkivonat a következő formátumú sztringek létrehozásával jön létre:

- `se`  – A jogkivonat lejárati ideje azonnali. Egész szám, amely a (z) 00:00:00 UTC szerint 1 – 1970 (UNIX-kor) időszakon belül, a jogkivonat lejárata után.
- `skn` – Az engedélyezési szabály neve, amely az SAS-kulcs neve.
- `sr` – Az elérni kívánt erőforrás URI-ja.
- `sig` Aláírás.

Az aláírás-karakterlánc az erőforrás URI-ja alapján kiszámított SHA-256 kivonat (a hatókör az előző szakaszban leírtak szerint), valamint a jogkivonat lejárati CRLF által elválasztott karakterlánc-ábrázolása.

A kivonatoló számítás a következő pszeudo-kódhoz hasonlóan néz ki, és egy 256 bites/32 bájtos kivonatoló értéket ad vissza. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

A jogkivonat a nem kivonatos értékeket tartalmazza, így a címzett újra kiszámíthatja a kivonatot ugyanazzal a paraméterekkel, és ellenőrizheti, hogy a kiállító rendelkezik-e érvényes aláíró kulccsal.

Az erőforrás URI-ja annak a Service Bus-erőforrásnak a teljes URI azonosítója, amelyhez hozzáférést igényelnek. Például: http:// <namespace> . servicebus.Windows.net/ <entityPath> vagy `sb://<namespace>.servicebus.windows.net/<entityPath>;` , `http://contoso.servicebus.windows.net/eventhubs/eh1` .

Az URI-nak százalékos kódolással kell rendelkeznie.

Az aláíráshoz használt megosztott hozzáférés-engedélyezési szabályt az URI által megadott entitáson vagy annak egyik hierarchikus szülője szerint kell konfigurálni. Például vagy az `http://contoso.servicebus.windows.net/eventhubs/eh1` `http://contoso.servicebus.windows.net` előző példában.

A SAS-token az <resourceURI> aláírás-karakterláncban használt összes erőforráshoz előtaggal érvényes.

> [!NOTE]
> A Event Hubs hozzáférési jogkivonatot hoz majd a megosztott hozzáférési házirend használatával. További információ: [megosztott hozzáférés engedélyezési házirendje](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Aláírás (token) létrehozása egy házirendből 
A következő szakasz bemutatja, hogyan hozhat létre SAS-tokent a közös hozzáférésű aláírási szabályzatok használatával.

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>JAVA

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Event Hubs-közzétevők hitelesítése SAS használatával 
Egy esemény-közzétevő definiál egy virtuális végpontot az Event hub számára. A közzétevő csak akkor használható, ha üzeneteket küld az Event hubhoz, és nem fogad üzeneteket.

Az Event hub jellemzően egy közzétevőt alkalmaz egy ügyfélen. Az Event hub bármelyik közzétevője számára küldött összes üzenet az várólistán lévő belül van. A kiadók részletes hozzáférés-vezérlést tesznek lehetővé.

Minden Event Hubs ügyfélhez egy egyedi token van rendelve, amely fel van töltve az ügyfélre. A jogkivonatok úgy jönnek létre, hogy mindegyik egyedi jogkivonat különböző egyedi közzétevőhöz biztosít hozzáférést. A jogkivonatot birtokló ügyfelek csak egyetlen közzétevőnek küldhetnek, és nem rendelkezhetnek más közzétevővel. Ha több ügyfél ugyanazt a jogkivonatot használja, akkor mindegyik megosztja a közzétevőt.

Minden token SAS-kulccsal van társítva. Általában minden token ugyanazzal a kulccsal van aláírva. Az ügyfelek nem ismerik a kulcsot, ami megakadályozza az ügyfelek számára a gyártási jogkivonatokat. Az ügyfelek ugyanazon jogkivonatokon működnek, amíg lejárnak.

Ha például az engedélyezési szabályok hatókörét úgy szeretné meghatározni, hogy csak az Event Hubs küldésére vagy közzétételére legyen szükség, meg kell határoznia egy küldési engedélyezési szabályt. Ezt névtér szintjén lehet elvégezni, vagy részletesebb hatókört adhat egy adott entitásnak (Event hubok-példány vagy témakör). Az ilyen szemcsés eléréssel hatókörbe tartozó ügyfelet vagy alkalmazást Event Hubs közzétevőnek nevezzük. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy SAS-kulcsot a közzétenni kívánt entitáson a **küldési** hatókör hozzárendeléséhez. További információ: [megosztott hozzáférés engedélyezési házirendjei](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Az 1. lépés-ben generált kulcs használatával egy adott közzétevőhöz tartozó lejárati idővel rendelkező SAS-tokent hozhat létre.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Adja meg a jogkivonatot a közzétevő ügyfelének, amely csak az entitásnak küldhető el, és azt a közzétevőt, amely számára a jogkivonat hozzáférést biztosít.

    A jogkivonat lejárta után az ügyfél elveszíti a hozzáférést az entitáshoz való küldéshez vagy közzétételhez. 


> [!NOTE]
> Bár ez nem ajánlott, az eszközök olyan jogkivonatokkal is felhasználhatók, amelyek hozzáférést biztosítanak az Event hub-hoz vagy a névtérhez. Minden olyan eszköz, amelyik ezt a jogkivonatot tárolja, közvetlenül is küldhet üzeneteket az adott Event hub-nak. Emellett az eszköz nem blocklisted el az adott Event hubhoz való küldéstől.
> 
> A konkrét és a részletes hatóköröket mindig ajánlott megadni.

> [!IMPORTANT]
> A jogkivonatok létrehozása után minden ügyfél saját egyedi jogkivonattal van kiépítve.
>
> Amikor az ügyfél adatokat küld egy Event hubhoz, a kérelmét a jogkivonattal címkézi. Ha meg szeretné akadályozni, hogy a támadók lehallgatják és ellopják a tokent, akkor az ügyfél és az Event hub közötti kommunikációnak titkosított csatornán kell történnie.
> 
> Ha egy támadó ellopja a jogkivonatot, a támadó megszemélyesítheti az ügyfelet, amelynek a jogkivonatát ellopták. A közzétevő Blocklisting az ügyfél használhatatlanná válik, amíg nem kap egy másik közzétevőt használó új jogkivonatot.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Event Hubs ügyfelek hitelesítése SAS használatával 
A Event Hubs gyártók által generált adatokból felhasznált háttérbeli alkalmazások hitelesítéséhez Event Hubs jogkivonat-hitelesítéshez az szükséges, hogy az ügyfelek rendelkezzenek a **felügyeleti jogokkal vagy** a Event Hubs névtér vagy az Event hub-példányhoz vagy a témakörhöz rendelt **figyelési** jogosultságokkal. Az adatok felhasználása Event Hubs fogyasztói csoportok használatával történik. Míg a SAS-szabályzat részletes hatókört biztosít, ez a hatókör csak az entitás szintjén van meghatározva, és nem a fogyasztói szinten. Ez azt jelenti, hogy a névtér szintjén vagy az Event hub-példányon vagy a témakör szintjén megadott jogosultságok az adott entitás fogyasztói csoportjaira lesznek alkalmazva.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Engedélyezés SAS használatával](authenticate-shared-access-signature.md)
- [Engedélyezés az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával](authenticate-shared-access-signature.md)
- [További információ a Event Hubs](event-hubs-about.md)

Tekintse meg a következő kapcsolódó cikkeket:

- [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
- [Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)
