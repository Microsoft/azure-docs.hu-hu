---
title: Azure API Management házirend-kifejezések | Microsoft Docs
description: Ismerje meg az Azure API Management házirend-kifejezéseit. Tekintse át a példákat, és tekintse meg a további elérhető erőforrásokat
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: aec1967f0652e18c4a24ca258c14a103355b22af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219315"
---
# <a name="api-management-policy-expressions"></a>API Management házirend-kifejezések
Ez a cikk a házirend-kifejezések szintaxisát ismerteti a C# 7-es verziójában. Mindegyik kifejezés rendelkezik hozzáféréssel a implicit módon megadott [környezeti](api-management-policy-expressions.md#ContextVariables) változóhoz, valamint a .NET-keretrendszer engedélyezett [részhalmazához](api-management-policy-expressions.md#CLRTypes) .

További információk:

- Ismerje meg, hogyan adhat meg környezeti információkat a háttér-szolgáltatáshoz. Használja a [lekérdezési karakterlánc beállítása paramétert](api-management-transformation-policies.md#SetQueryStringParameter) , és adja meg a HTTP-fejléc házirendjeit az információk [megadásához](api-management-transformation-policies.md#SetHTTPheader) .
- Tekintse meg, hogyan használhatja a JWT-szabályzat [érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) beállítást a jogkivonatok jogcímein alapuló műveletekhez való hozzáférés előzetes engedélyezéséhez.
- A szabályzatok kiértékelésének és az értékelések eredményeinek megtekintéséhez tekintse meg az [API-ellenőrök](./api-management-howto-api-inspector.md) nyomkövetésének használatát ismertető témakört.
- Tekintse meg, hogyan használhat kifejezéseket a [Get from cache](api-management-caching-policies.md#GetFromCache) és a [Store to cache](api-management-caching-policies.md#StoreToCache) policy házirendekkel a API Management válasz gyorsítótárazásának konfigurálásához. Állítsa be azt az időtartamot, amely megfelel a háttérrendszer válasz-gyorsítótárazásának, amelyet a biztonsági mentést végző szolgáltatás `Cache-Control` irányelve meghatároz.
- Lásd: tartalom szűrésének végrehajtása. Távolítsa el az adatelemeket a háttértől kapott válaszból a [vezérlési folyamat](api-management-advanced-policies.md#choose) és a [szövegtörzs](api-management-transformation-policies.md#SetBody) -szabályzatok használatával.
- A házirend-utasítások letöltéséhez tekintse meg az [API-Management-Samples/policies GitHub-](https://github.com/Azure/api-management-samples/tree/master/policies) tárházat.


## <a name="syntax"></a><a name="Syntax"></a> Szintaxis
Az egyutasításos kifejezések a ben vannak `@(expression)` , ahol a egy `expression` jól formázott C# Expression utasítás.

A többszörös kimutatási kifejezések a-ben vannak bezárva `@{expression}` . A többutasításos kifejezéseken belül minden kód elérési útnak egy `return` utasítással kell végződnie.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> Példák

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Használat
A kifejezések bármely API Management [házirendben](api-management-policies.md) attribútumértékként vagy szöveges értékként használhatók (kivéve, ha a házirend-hivatkozás másként nem határoz meg).

> [!IMPORTANT]
> Ha házirend-kifejezéseket használ, csak korlátozott mértékben ellenőrzi a házirend-kifejezéseket, ha a házirend meg van határozva. A kifejezéseket az átjáró futásidőben hajtja végre, a házirend-kifejezések által generált kivételek futásidejű hibát eredményeznek.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> A .NET-keretrendszerben engedélyezett típusok házirend-kifejezésekben
A következő táblázat a .NET-keretrendszer típusait és azok tagjait sorolja fel, amelyek a házirend-kifejezésekben engedélyezettek.

|Típus|Támogatott tagok|
|--------------|-----------------------|
|Newtonsoft.Jsbekapcsolva. Formázási|Mind|
|Newtonsoft.Json.JsonConvert|Serializeobject elem, DeserializeObject|
|Newtonsoft.Jsbekapcsolva. LINQ. Extensions|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JArray|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JConstructor|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JContainer|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JObject|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JProperty|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JRaw|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JToken|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JTokenType|Mind|
|Newtonsoft.Jsbekapcsolva. LINQ. JValue|Mind|
|System. Array|Mind|
|System. BitConverter|Mind|
|System. Boolean|Mind|
|System. byte|Mind|
|System. char|Mind|
|System. Collections. Generic. Dictionary<TKey, TValue>|Mind|
|System. Collections. Generic. HashSet\<T>|Mind|
|System. Collections. Generic. ICollection illesztőfelületet\<T>|Mind|
|System. Collections. Generic. IDictionary<TKey, TValue>|Mind|
|System. Collections. Generic. IEnumerable\<T>|Mind|
|System. Collections. Generic. IEnumerator\<T>|Mind|
|System. Collections. Generic. IList\<T>|Mind|
|System. Collections. Generic. IReadOnlyCollection\<T>|Mind|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Mind|
|System. Collections. Generic. ISet\<T>|Mind|
|System. Collections. Generic. KeyValuePair<TKey, TValue>|Mind|
|System. Collections. Generic. list\<T>|Mind|
|System. Collections. Generic. üzenetsor\<T>|Mind|
|System. Collections. Generic. stack\<T>|Mind|
|System. Convert|Mind|
|System. DateTime|(Konstruktor), Hozzáadás, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, dátum, nap, DayOfWeek, DayOfYear, DaysInMonth, óra, IsDaylightSavingTime, IsLeapYear, MaxValue, ezredmásodperc, perc, MinValue, hónap, most, elemzés, második, kivonás, kullancsok, TimeOfDay, ma, ToString, UtcNow, év|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Mind|
|System. decimális|Mind|
|System. Double|Mind|
|System. Exception|Mind|
|System. GUID|Mind|
|System. Int16|Mind|
|System. Int32|Mind|
|System. Int64|Mind|
|System. IO. StringReader|Mind|
|System. IO. StringWriter|Mind|
|System. LINQ. enumerable|Mind|
|System. Math|Mind|
|System. MidpointRounding|Mind|
|System .net. websegédprogram|Mind|
|System. Nullható|Mind|
|System. Random|Mind|
|System. sbyte érték|Mind|
|System. Security. kriptográfia. AsymmetricAlgorithm|Mind|
|System. Security. kriptográfia. titkosítási|Mind|
|System. Security. kriptográfia. HashAlgorithm|Mind|
|System. Security. kriptográfia. HashAlgorithmName|Mind|
|System. Security. kriptográfia. HMAC|Mind|
|System. Security. kriptográfia. HMACMD5|Mind|
|System. Security. kriptográfia. HMACSHA1|Mind|
|System. Security. kriptográfia. HMACSHA256|Mind|
|System. Security. kriptográfia. HMACSHA384|Mind|
|System. Security. kriptográfia. HMACSHA512|Mind|
|System. Security. kriptográfia. KeyedHashAlgorithm|Mind|
|System. Security. kriptográfia. MD5|Mind|
|System. Security. kriptográfia. OID|Mind|
|System. Security. kriptográfia. PaddingMode|Mind|
|System. Security. kriptográfia. RNGCryptoServiceProvider|Mind|
|System. Security. kriptográfia. RSA|Mind|
|System. Security. kriptográfia. RSAEncryptionPadding|Mind|
|System. Security. kriptográfia. RSASignaturePadding|Mind|
|System. Security. kriptográfia. SHA1|Mind|
|System. Security. kriptográfia. SHA1Managed|Mind|
|System. Security. kriptográfia. SHA256|Mind|
|System. Security. kriptográfia. SHA256Managed|Mind|
|System. Security. kriptográfia. SHA384|Mind|
|System. Security. kriptográfia. SHA384Managed|Mind|
|System. Security. kriptográfia. SHA512|Mind|
|System. Security. kriptográfia. SHA512Managed|Mind|
|System. Security. kriptográfia. SymmetricAlgorithm|Mind|
|System. Security. kriptográfia. X509Certificates. PublicKey|Mind|
|System. Security. kriptográfia. X509Certificates. RSACertificateExtensions|Mind|
|System. Security. kriptográfia. X509Certificates. X500DistinguishedName|Name|
|System. Security. kriptográfia. X509Certificates. X509|Mind|
|System. Security. kriptográfia. X509Certificates. X509certificate2)|Mind|
|System. Security. kriptográfia. X509Certificates. X509ContentType|Mind|
|System. Security. kriptográfia. X509Certificates. X509NameType|Mind|
|System. Single|Mind|
|System. String|Mind|
|System. StringComparer|Mind|
|System. Stringcomparison argumentummal|Mind|
|System. StringSplitOptions|Mind|
|System. Text. Encoding|Mind|
|System. Text. RegularExpressions. Capture|Index, hossz, érték|
|System. Text. RegularExpressions. CaptureCollection|Darabszám, elem|
|System. Text. RegularExpressions. Group|Rögzítés, sikeres|
|System. Text. RegularExpressions. GroupCollection|Darabszám, elem|
|System. Text. RegularExpressions. Match|Üres, csoportok, eredmény|
|System. Text. RegularExpressions. regex|(Konstruktor), IsMatch, egyezés, egyezés, csere, felszabadulás, felosztás|
|System. Text. RegularExpressions. RegexOptions|Mind|
|System. Text. StringBuilder|Mind|
|System. TimeSpan|Mind|
|System. TimeZone|Mind|
|System. TimeZoneInfo. AdjustmentRule|Mind|
|System. TimeZoneInfo. TransitionTime|Mind|
|System. TimeZoneInfo|Mind|
|System. rekord|Mind|
|System. UInt16|Mind|
|System. UInt32|Mind|
|System. UInt64|Mind|
|System. URI|Mind|
|System. UriPartial|Mind|
|System.Xml. LINQ. Extensions|Mind|
|System.Xml. LINQ. XAttribute|Mind|
|System.Xml. LINQ. XCData|Mind|
|System.Xml. LINQ. XComment|Mind|
|System.Xml. LINQ. XContainer|Mind|
|System.Xml. LINQ. XDeclaration|Mind|
|System.Xml. LINQ. XDocument|Az összes, a kivételével: Load|
|System.Xml. LINQ. XDocumentType|Mind|
|System.Xml. LINQ. XElement|Mind|
|System.Xml. LINQ. XName|Mind|
|System.Xml. LINQ. XNamespace|Mind|
|System.Xml. LINQ. XNode|Mind|
|System.Xml. LINQ. XNodeDocumentOrderComparer|Mind|
|System.Xml. LINQ. XNodeEqualityComparer|Mind|
|System.Xml. LINQ. XObject|Mind|
|System.Xml. LINQ. XProcessingInstruction|Mind|
|System.Xml. LINQ. XText|Mind|
|System.Xml.XmlNodeType|Mind|

## <a name="context-variable"></a><a name="ContextVariables"></a> Környezeti változó
Egy nevű változó `context` implicit módon elérhető minden házirend- [kifejezésben](api-management-policy-expressions.md#Syntax). A tagjai a szolgáltatással kapcsolatos információkat biztosítanak `\request` . Az összes `context` tag csak olvasható.

|Környezeti változó|Engedélyezett metódusok, tulajdonságok és paraméterek értékei|
|----------------------|-------------------------------------------------------|
|összefüggésben|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Üzembe helyezés](#ref-context-deployment)<br /><br /> Eltelt idő: TimeSpan időköz az időbélyegző és az aktuális idő értéke között<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Művelet](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [Kérés](#ref-context-request)<br /><br /> Kérelemazonosító: GUID – egyedi kérelem azonosítója<br /><br /> [Válasz](#ref-context-response)<br /><br /> [Előfizetés](#ref-context-subscription)<br /><br /> Időbélyeg: dátum és idő – a kérés fogadásakor időpontra<br /><br /> Nyomkövetés: bool – jelzi, hogy a nyomkövetés be van-e kapcsolva <br /><br /> [Felhasználó](#ref-context-user)<br /><br /> [Változók](#ref-context-variables): IReadOnlyDictionary<sztring, objektum><br /><br /> void nyomkövetés (üzenet: karakterlánc)|
|<a id="ref-context-api"></a>összefüggésben. API|Azonosító: karakterlánc<br /><br /> IsCurrentRevision: bool<br /><br />  Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Változat: karakterlánc<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verzió: karakterlánc |
|<a id="ref-context-deployment"></a>összefüggésben. Telepítési|Régió: karakterlánc<br /><br /> Szolgáltatásnév: karakterlánc<br /><br /> Tanúsítványok: IReadOnlyDictionary<sztring, X509certificate2)>|
|<a id="ref-context-lasterror"></a>összefüggésben. LastError|Forrás: karakterlánc<br /><br /> Ok: karakterlánc<br /><br /> Üzenet: karakterlánc<br /><br /> Hatókör: karakterlánc<br /><br /> Szakasz: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> PolicyId: karakterlánc<br /><br /> További információ a környezetről. LastError lásd: [hibakezelés](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>összefüggésben. Művelet|Azonosító: karakterlánc<br /><br /> Metódus: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> UrlTemplate: karakterlánc|
|<a id="ref-context-product"></a>összefüggésben. Termék|API-k: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Állapot: Enum ProductState {NotPublished, published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>összefüggésben. Kérelem|Törzs: [IMessageBody](#ref-imessagebody) , vagy `null` Ha a kérelem nem rendelkezik törzstel.<br /><br /> Tanúsítvány: System. Security. kriptográfia. X509Certificates. X509certificate2)<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> IP-cím: karakterlánc<br /><br /> MatchedParameters: IReadOnlyDictionary<karakterlánc, karakterlánc><br /><br /> Metódus: karakterlánc<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL-cím: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>karakterlánc-környezet Request. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: string)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt kérelmek fejlécének értékét adja vissza, vagy `defaultValue` Ha a fejléc nem található.|
|<a id="ref-context-response"></a>összefüggésben. Válasz|Törzs: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: karakterlánc|
|<a id="ref-context-response-headers"></a>karakterlánc-környezet Response. headers. GetValueOrDefault (headerName: karakterlánc, defaultValue: karakterlánc)|headerName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> A vesszővel tagolt válasz fejlécének értékét adja vissza, vagy `defaultValue` Ha a fejléc nem található.|
|<a id="ref-context-subscription"></a>összefüggésben. Előfizetés|CreatedDate: dátum és idő<br /><br /> EndDate: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kulcs: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> PrimaryKey: karakterlánc<br /><br /> Értesítésiközpont: karakterlánc<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>összefüggésben. Felhasználói|E-mail: karakterlánc<br /><br /> FirstName: karakterlánc<br /><br /> Csoportok: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Azonosító: karakterlánc<br /><br /> Identitások: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: sztring<br /><br /> Megjegyzés: karakterlánc<br /><br /> RegistrationDate: dátum és idő|
|<a id="ref-iapi"></a>IApi|Azonosító: karakterlánc<br /><br /> Name: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Protokollok: IEnumerable<karakterlánc\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Azonosító: karakterlánc<br /><br /> Name: karakterlánc|
|<a id="ref-imessagebody"></a>IMessageBody|Mint<T \> (preserveContent: bool = false): where t: string, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> A `context.Request.Body.As<T>` és `context.Response.Body.As<T>` metódusok egy adott típusban lévő kérelem és válaszüzenet törzsének olvasására szolgálnak `T` . Alapértelmezés szerint a metódus az eredeti üzenettörzs-adatfolyamot használja, és a visszatérés után elérhetetlenné teszi. Annak elkerülése érdekében, hogy a metódus a test stream egy példányán működjön, állítsa a paramétert a következőre: `preserveContent` `true` . [Itt](api-management-transformation-policies.md#SetBody) láthat egy példát.|
|<a id="ref-iurl"></a>IUrl|Gazdagép: karakterlánc<br /><br /> Elérési út: karakterlánc<br /><br /> Port: int<br /><br /> [Lekérdezés](#ref-iurl-query): IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> QueryString: karakterlánc<br /><br /> Séma: karakterlánc|
|<a id="ref-iuseridentity"></a>IUserIdentity|Azonosító: karakterlánc<br /><br /> Szolgáltató: karakterlánc|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Fejléc: karakterlánc<br /><br /> Lekérdezés: karakterlánc|
|<a id="ref-iurl-query"></a>string IUrl. Query. GetValueOrDefault (queryParameterName: karakterlánc, defaultValue: karakterlánc)|queryParameterName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt lekérdezési paraméterek értékét adja vissza, vagy `defaultValue` Ha a paraméter nem található.|
|<a id="ref-context-variables"></a>T környezet. Változók. GetValueOrDefault<T \> (variableName: karakterlánc, defaultValue: t)|variableName: karakterlánc<br /><br /> defaultValue: T<br /><br /> A változó értéket adja vissza a típus értékre, `T` vagy `defaultValue` Ha a változó nem található.<br /><br /> Ez a metódus kivételt jelez, ha a megadott típus nem egyezik a visszaadott változó tényleges típusával.|
|BasicAuthCredentials AsBasic (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes HTTP alapszintű hitelesítési engedélyezési kérelem fejlécének értékét tartalmazza, a metódus egy típusú objektumot ad vissza, `BasicAuthCredentials` ellenkező esetben a metódus null értéket ad vissza.|
|bool TryParseBasic (bemenet: Ez a karakterlánc, eredmény: out BasicAuthCredentials)|bemenet: karakterlánc<br /><br /> eredmény: kimenő BasicAuthCredentials<br /><br /> Ha a bemeneti paraméter egy érvényes HTTP alapszintű hitelesítési engedélyezési értéket tartalmaz a kérelem fejlécében, `true` és az eredmény paraméter egy típusú értéket tartalmaz, `BasicAuthCredentials` ellenkező esetben a metódus visszaadja a értéket `false` .|
|BasicAuthCredentials|Password (jelszó): karakterlánc<br /><br /> UserId: karakterlánc|
|JWT AsJwt (bemenet: Ez a karakterlánc)|bemenet: karakterlánc<br /><br /> Ha a bemeneti paraméter érvényes JWT token értéket tartalmaz, a metódus egy típusú objektumot ad vissza, `Jwt` ellenkező esetben a metódus visszatér `null` .|
|bool TryParseJwt (bemenet: Ez a karakterlánc, eredmény: out JWT)|bemenet: karakterlánc<br /><br /> eredmény: kimenő JWT<br /><br /> Ha a bemeneti paraméter érvényes JWT jogkivonat-értéket tartalmaz, a metódus visszaadja, `true` és a Result paraméter egy típusú értéket tartalmaz, `Jwt` ellenkező esetben a metódus visszaadja `false` .|
|JWT|Algoritmus: karakterlánc<br /><br /> Célközönségek: IEnumerable<sztring\><br /><br /> Jogcímek: IReadOnlyDictionary<karakterlánc, karakterlánc [] ><br /><br /> Expirationtime tulajdonságok: DateTime?<br /><br /> Azonosító: karakterlánc<br /><br /> Kiállító: karakterlánc<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Tárgy: karakterlánc<br /><br /> Típus: karakterlánc|
|karakterlánc JWT. jogcím. GetValueOrDefault (claimName: karakterlánc, defaultValue: karakterlánc)|claimName: karakterlánc<br /><br /> defaultValue: karakterlánc<br /><br /> Vesszővel tagolt jogcím-értékeket ad vissza, vagy `defaultValue` Ha a fejléc nem található.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm)|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] titkosítás (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemenet – titkosítatlan szöveges szöveg<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Titkosított egyszerű szöveges értéket ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: karakterlánc, kulcs: byte [], IV: bájt [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG – szimmetrikus titkosítási algoritmus neve<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm)|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />Szöveges karakterláncot ad vissza.|
|byte [] visszafejtés (bemenet: Ez a bájt [], ALG: System. Security. kriptográfia. SymmetricAlgorithm, kulcs: byte [], IV: byte [])|bemeneti – Cypher szöveg, amelyet vissza kell fejteni<br /><br />ALG-titkosítási algoritmus<br /><br />kulcs-titkosítási kulcs<br /><br />IV – inicializálási vektor<br /><br />Szöveges karakterláncot ad vissza.|
|bool VerifyNoRevocation (bemenet: Ez a System. Security. kriptográfia. X509Certificates. X509certificate2))|Egy X. 509 lánc érvényesítését végzi a tanúsítvány visszavonási állapotának ellenőrzése nélkül.<br /><br />bemeneti-tanúsítvány objektum<br /><br />Azt adja vissza `true` , hogy az ellenőrzés sikeres- `false` e; ha az ellenőrzés sikertelen.|


## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Szabályzatminták](./policy-reference.md)
