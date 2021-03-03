---
title: Azure Service Bus hozzáférés-vezérlés közös hozzáférési aláírásokkal
description: A Service Bus hozzáférés-vezérlésének áttekintése a közös hozzáférési aláírások használatával – áttekintés, a SAS-engedélyezéssel kapcsolatos részletek Azure Service Bus.
ms.topic: article
ms.date: 01/19/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d210da4b653a20dd273dfce723f0bf9d5dbf743b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737817"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Service Bus hozzáférés-vezérlés közös hozzáférési aláírásokkal

Ez a cikk a *közös hozzáférésű aláírásokat* (SAS) ismerteti, hogyan működnek, és hogyan használhatók platform-független módon.

Az SAS az engedélyezési szabályokon alapuló Service Bushoz fér hozzá. Ezek a névterek vagy üzenetküldési entitások (Üzenetsor vagy témakör) szerint vannak konfigurálva. Az engedélyezési szabályok névvel rendelkeznek, bizonyos jogokkal vannak társítva, és egy pár titkosítási kulcsot is végrehajt. A szabály nevét és kulcsát a Service Bus SDK-n vagy a saját kódjában használhatja SAS-token létrehozásához. Az ügyfél ezután átadhatja a jogkivonatot a Service Busnak, hogy igazolja a kért művelet engedélyezését.

> [!NOTE]
> Azure Service Bus támogatja a Service Bus névterek és az entitások hozzáférésének engedélyezését Azure Active Directory (Azure AD) használatával. Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a közös hozzáférésű aláírások (SAS) számára. Az Azure AD-ben nincs szükség a jogkivonatok tárolására a kódban, és kockázatos biztonsági réseket.
>
> A Microsoft azt javasolja, hogy ha lehetséges, az Azure AD-t használja a Azure Service Bus alkalmazásaihoz. További információért tekintse át a következő cikkeket:
> - [Azure Active Directory használatával hitelesítheti és engedélyezheti az alkalmazást Azure Service Bus entitások eléréséhez](authenticate-application.md).
> - [Felügyelt identitás hitelesítése Azure Active Directory használatával Azure Service Bus erőforrások eléréséhez](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>SAS áttekintése

A közös hozzáférésű aláírások egy egyszerű tokeneket használó jogcím-alapú engedélyezési mechanizmus. A SAS használatával a kulcsok soha nem lesznek átadva a huzalon. A kulcsok a szolgáltatás által később ellenőrizhető információk titkosítására szolgálnak. Az SAS a Felhasználónév és a jelszó sémához hasonló módon használható, ahol az ügyfél azonnal rendelkezik egy engedélyezési szabály nevével és egy hozzá tartozó kulccsal. Az SAS az összevont biztonsági modellhez hasonlóan is használható, ahol az ügyfél időkorlátos és aláírt hozzáférési jogkivonatot kap a biztonsági jogkivonat szolgáltatástól anélkül, hogy az aláírási kulcs birtokában lenne.

A Service Bus SAS-hitelesítése olyan [megosztott hozzáférés-engedélyezési szabályokkal](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) van konfigurálva, amelyek hozzáférési jogosultságokkal rendelkeznek, valamint az elsődleges és másodlagos titkosítási kulcsok párja. A kulcsok 256 bites értékek Base64-ábrázolásban. Megadhatja a szabályokat a névtér szintjén [, Service Bus-továbbítókat,](../azure-relay/relay-what-is-it.md) [várólistákat](service-bus-messaging-overview.md#queues)és [témaköröket](service-bus-messaging-overview.md#topics).

A [közös hozzáférésű aláírási](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) jogkivonat tartalmazza a kiválasztott engedélyezési szabály nevét, az elérni kívánt erőforrás URI-ját, a lejárati pillanatot, valamint a HMAC-sha256 titkosítási aláírást, amely a kiválasztott engedélyezési szabály elsődleges vagy másodlagos titkosítási kulcsa alapján lett kiszámítva.

## <a name="shared-access-authorization-policies"></a>Megosztott hozzáférés engedélyezési házirendjei

Minden Service Bus névtérnek és minden Service Bus entitásnak van egy közös hozzáférési engedélyezési szabályzata, amely szabályokból áll. A névtér szintjén lévő szabályzat a névtéren belüli összes entitásra vonatkozik, függetlenül az egyes házirend-konfigurációtól.

Minden egyes engedélyezési házirend-szabályhoz három információt kell eldöntenie: **név**, **hatókör** és **jogosultságok**. A **név** csak az; a hatókörön belüli egyedi név. A hatókör elég egyszerű: Ez a kérdéses erőforrás URI-ja. Service Bus névtér esetében a hatókör a teljes tartománynév (FQDN), például: `https://<yournamespace>.servicebus.windows.net/` .

A házirend-szabály által biztosított jogok a következőket foglalhatják magukban:

* Send (küldés) – meghatalmazza a jogot arra, hogy üzeneteket küldjön az entitásnak
* "Listen" (figyelő) – a jogosult a fogadásra (Üzenetsor, előfizetések) és az összes kapcsolódó üzenet kezelésére
* "Manage" – a jogosult a névtér topológiájának kezelésére, beleértve az entitások létrehozását és törlését.

A "kezelés" jogosultság magában foglalja a "Send" és a "Receive" jogosultságot.

A névtér vagy az entitások házirendje legfeljebb 12 közös hozzáférési engedélyezési szabályt tud tárolni, amelyek három szabálykészlet számára biztosítanak helyet, amelyek mindegyike rendelkezik az alapszintű jogokkal, valamint a Küldés és a figyelés kombinációjával. Ez a korlát azt hangsúlyozza, hogy a SAS-szabályzat tárolója nem a felhasználó-vagy szolgáltatásfiók-tároló. Ha az alkalmazásnak felhasználói vagy szolgáltatásbeli identitások alapján kell hozzáférést biztosítania Service Bushoz, akkor olyan biztonsági jogkivonat-szolgáltatást kell létrehoznia, amely az SAS-tokeneket a hitelesítés és a hozzáférés-ellenőrzések után bocsátja ki.

Az engedélyezési szabályokhoz egy *elsődleges kulcs* és egy *másodlagos kulcs* van rendelve. Ezek kriptográfiai szempontból erős kulcsok. Ne veszítse el őket, vagy ne szivárogjon rájuk – mindig a [Azure Portal][Azure portal]lesznek elérhetők. A generált kulcsok bármelyikét használhatja, és bármikor újragenerálhatja őket. Ha újra létrehoz vagy módosít egy kulcsot a házirendben, az adott kulcson alapuló, korábban kiadott jogkivonatok azonnal érvénytelenné válnak. Az ilyen jogkivonatok alapján létrehozott folyamatban lévő kapcsolatok azonban továbbra is működni fognak, amíg a jogkivonat le nem jár.

Service Bus névtér létrehozásakor a rendszer automatikusan létrehoz egy **RootManageSharedAccessKey** nevű házirend-szabályt a névtérhez. Ez a szabályzat a teljes névtérre vonatkozó engedélyeket kezeli. Javasoljuk, hogy ezt a szabályt rendszergazdai **főfiókként** kezelje, és ne használja az alkalmazásban. A portálon, a PowerShell vagy az Azure CLI használatával további szabályzatokat hozhat létre a névtér **configure (Konfigurálás** ) lapján.

## <a name="best-practices-when-using-sas"></a>A SAS használatával kapcsolatos ajánlott eljárások
Ha közös hozzáférési aláírásokat használ az alkalmazásokban, a következő két lehetséges kockázattal kell tisztában lennie:

- Ha egy SAS kiszivárgott, azt bárki felhasználhatja, aki beolvassa azt, ami potenciálisan veszélyeztetheti a Service Bus erőforrásait.
- Ha egy ügyfélalkalmazás számára megadott SAS lejár, és az alkalmazás nem tud beolvasni egy új SAS-t a szolgáltatásból, akkor az alkalmazás funkciói akadályozva lehetnek.

A közös hozzáférési aláírások használatára vonatkozó alábbi javaslatok segíthetnek a kockázatok enyhítésében:

- **Ha szükséges, az ügyfelek automatikusan megújítják az SAS**-t, ha szükséges: az ügyfeleknek a lejárat előtt is meg kell újítaniuk a sas-t, hogy az újrapróbálkozások ideje ne legyen elérhető. Ha a SAS-t kis számú azonnali, rövid életű művelethez kívánja használni, amelyeket a lejárati időszakon belül el kell végezni, akkor előfordulhat, hogy az SAS-t nem kell megújítani. Ha azonban olyan ügyfele van, amely az SAS-n keresztül rutinul kéri a kérelmeket, akkor a lejárati lehetőség a lejátszásra kerül. A legfontosabb szempont, hogy az SAS-nek rövid életűnek kell lennie (ahogy azt korábban már említettük) annak biztosításához, hogy az ügyfél kellő időben megújítsa a megújítást (a sikeres megújítás előtt lejáró SAS miatti megszakítás elkerülése érdekében).
- Ügyeljen **a sas indítási időpontjára**: Ha **most** beállítja az SAS kezdési idejét, majd az óra eldöntése miatt (a különböző gépektől függően a jelenlegi idő eltérése), a hibák időnként megfigyelhetők az első pár percben. Általában úgy állítsa be a kezdési időpontot, hogy legalább 15 perccel korábbi legyen. Vagy ne állítsa be egyáltalán, hogy minden esetben azonnal érvényes lesz. Ugyanez általában a lejárati időre is vonatkozik. Ne feledje, hogy akár 15 perces órajelet is megvizsgálhat a kérések bármelyik irányában. 
- **Legyen egyedi az elérni kívánt erőforrással**: biztonsági szempontból ajánlott a minimálisan szükséges jogosultságokat biztosítani a felhasználónak. Ha egy felhasználónak csak olvasási hozzáférésre van szüksége egyetlen entitáshoz, akkor olvasási hozzáféréssel kell rendelkeznie az adott entitáshoz, és nem kell olvasási/írási/törlési hozzáférést adni az összes entitáshoz. Emellett segít csökkenteni a károkat abban az esetben, ha egy SAS biztonsága sérül, mert az SAS a támadó kezében kevesebb árammal rendelkezik.
- **Ne mindig az SAS-t használja**: időnként az adott művelethez kapcsolódó kockázatok meghaladják az SAS előnyeit Event Hubs. Ilyen műveletekhez hozzon létre egy olyan középszintű szolgáltatást, amely az üzleti szabályok érvényesítése, hitelesítése és naplózása után a Event Hubs ír.
- **Mindig https használata**: sas létrehozása vagy terjesztése mindig HTTPS használatával. Ha a SAS átadása HTTP-n keresztül történik, és a rendszer elvégzi a lehallgatást, az ember által a középső csatolást végző támadó elolvashatja az SAS-t, majd ugyanúgy használhatja azt, mint amennyire a kívánt felhasználó rendelkezhet, esetleg veszélyezteti a bizalmas adatokat, vagy engedélyezheti az adatsérülést a rosszindulatú felhasználó számára.

## <a name="configuration-for-shared-access-signature-authentication"></a>A közös hozzáférésű aláírások hitelesítésének konfigurációja

A [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) szabályt Service Bus névterek, várólisták vagy témakörök esetében is konfigurálhatja. A [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Service Bus-előfizetésre való konfigurálása jelenleg nem támogatott, de a névtéren vagy témakörben konfigurált szabályokkal biztonságossá teheti az előfizetésekhez való hozzáférést. Az eljárást bemutató munkaminta esetében tekintse meg a [közös hozzáférésű aláírás (SAS) hitelesítés használata Service Bus előfizetések](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) mintáját.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Ebben az ábrán a *manageRuleNS*, a *SendRuleNS* és a *listenRuleNS* engedélyezési szabályok mind a "Q1", mind a "T1" témakörre érvényesek, míg a *listenRuleQ* és a *sendRuleQ* csak a Q1-es és a sendRuleT-re vonatkozik, és a  csak a (

## <a name="generate-a-shared-access-signature-token"></a>Közös hozzáférésű aláírási jogkivonat létrehozása

Minden olyan ügyfél létrehozhat egy SAS-jogkivonatot, amely egy engedélyezési szabály nevének és az egyik aláíró kulcsnak a nevéhez fér hozzá. A jogkivonat a következő formátumú sztringek létrehozásával jön létre:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

- `se` -A jogkivonat lejárati ideje azonnali. Az egész szám, amely a `00:00:00 UTC` (z) 1970. január 1-jén (UNIX-kor), a jogkivonat lejárati idejének lejárta óta.
- `skn` – Az engedélyezési szabály neve.
- `sr` – Az elérni kívánt erőforrás URL-kódolású URI-ja.
- `sig` -URL-kódolású HMACSHA256 aláírása. A kivonat kiszámítása a következő pszeudo-kódhoz hasonlóan néz ki, és a nyers bináris kimenet Base64 értékét adja vissza.

    ```
    urlencode(base64(hmacsha256(urlencode('https://<yournamespace>.servicebus.windows.net/') + "\n" + '<expiry instant>', '<signing key>')))
    ```

Íme egy példa C#-kód a SAS-token generálásához:

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

> [!IMPORTANT]
> Az SAS-token különböző programozási nyelveken való generálására példákat az [sas-jogkivonat létrehozása](/rest/api/eventhub/generate-sas-token)című témakörben talál. 


A jogkivonat a nem kivonatos értékeket tartalmazza, így a címzett újra kiszámíthatja a kivonatot ugyanazzal a paraméterekkel, és ellenőrizheti, hogy a kiállító rendelkezik-e érvényes aláíró kulccsal.

Az erőforrás URI-ja annak a Service Bus-erőforrásnak a teljes URI azonosítója, amelyhez hozzáférést igényelnek. Például: `http://<namespace>.servicebus.windows.net/<entityPath>` vagy `sb://<namespace>.servicebus.windows.net/<entityPath>` ;, azaz, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3` . 

**Az URI-nak [százalékos kódolással](/dotnet/api/system.web.httputility.urlencode)kell rendelkeznie.**

Az aláíráshoz használt megosztott hozzáférés-engedélyezési szabályt az URI által megadott entitáson vagy annak egyik hierarchikus szülője szerint kell konfigurálni. Például vagy az `http://contoso.servicebus.windows.net/contosoTopics/T1` `http://contoso.servicebus.windows.net` előző példában.

Az SAS-token érvényes minden olyan erőforráshoz, amelyet a `<resourceURI>` -ben használt `signature-string` .


## <a name="regenerating-keys"></a>Kulcsok újragenerálása

Javasoljuk, hogy rendszeresen újragenerálja a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektumban használt kulcsokat. Az elsődleges és a másodlagos kulcs tárolóhelyei úgy vannak, hogy a kulcsok fokozatos elforgatása is megtörténjen. Ha az alkalmazás általában az elsődleges kulcsot használja, akkor az elsődleges kulcsot a másodlagos kulcs tárolóhelyére másolhatja, és csak ezután generálhatja újra az elsődleges kulcsot. Az új elsődleges kulcs értéke ezután konfigurálható az ügyfélalkalmazások között, amelyek továbbra is hozzáférnek a régi elsődleges kulcs használatával a másodlagos tárolóhelyen. Az összes ügyfél frissítése után újragenerálhatja a másodlagos kulcsot, hogy végül kivonja a régi elsődleges kulcsot.

Ha ismeri vagy gyanítja, hogy a kulcs biztonsága sérül, és vissza kell vonnia a kulcsokat, újra létrehozhatja a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) és [értesítésiközpont](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) , és új kulcsokkal helyettesítheti azokat. Ez az eljárás érvényteleníti a régi kulcsokkal aláírt összes jogkivonatot.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Közös hozzáférésű aláírások hitelesítése Service Bus

A következő témakörben ismertetett forgatókönyv magában foglalja az engedélyezési szabályok konfigurálását, az SAS-tokenek létrehozását és az ügyfél-engedélyezést.

Egy Service Bus alkalmazás mintája, amely bemutatja a konfigurációt, és SAS-hitelesítést használ, lásd: [közös hozzáférésű aláírások hitelesítése Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/ManagingEntities/SASAuthorizationRule)használatával.

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Megosztott hozzáférés engedélyezési szabályainak elérése egy entitáson

A Service Bus .NET-keretrendszer könyvtáraival a megfelelő [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)a [engedélyezési szabályok](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) -gyűjteményen keresztül érheti el a [Microsoft. ServiceBus. Messaging. SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektumot, amely egy Service Bus-várólistán vagy-témakörön van konfigurálva.

A következő kód bemutatja, hogyan adhat hozzá egy üzenetsor engedélyezési szabályait.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Közös hozzáférésű aláírás engedélyezésének használata

Az Azure .NET SDK-t és a Service Bus .NET-kódtárakat használó alkalmazások a [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) osztályon keresztül HASZNÁLHATJÁK az SAS-engedélyezést. A következő kód a jogkivonat-szolgáltató használatát mutatja be üzenetek küldésére egy Service Bus üzenetsor számára. Az itt látható használat alternatívájaként egy korábban kiadott tokent is átadhat a jogkivonat-szolgáltató gyári metódusának.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

A jogkivonat-szolgáltatót közvetlenül is használhatja a jogkivonatok kiadására a más ügyfelek számára.

A kapcsolati karakterláncok tartalmazhatnak egy szabály nevét (*SharedAccessKeyName*) és a szabály kulcsát (*SharedAccessKey*) vagy egy korábban kiállított tokent (*SharedAccessSignature*). Ha ezek szerepelnek a kapcsolati sztringet fogadó vagy előállító metódusnak átadott kapcsolati sztringben, a rendszer automatikusan létrehozza és feltölti az SAS-jogkivonat-szolgáltatót.

Vegye figyelembe, hogy az SAS-hitelesítés Service Bus-továbbítókkal való használatához a Service Bus névtérben konfigurált SAS-kulcsokat is használhat. Ha explicit módon létrehoz egy továbbítót a névtérben ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) egy [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), akkor csak az adott továbbítóra vonatkozóan állíthatja be a sas-szabályokat. Az SAS-engedélyezés Service Bus-előfizetésekkel való használatához Service Bus névtérben vagy témakörben konfigurált SAS-kulcsokat használhat.

## <a name="use-the-shared-access-signature-at-http-level"></a>A közös hozzáférésű aláírás használata (HTTP-szinten)

Most, hogy már tudja, hogyan hozhat létre közös hozzáférési aláírásokat a Service Busban lévő összes entitáshoz, készen áll egy HTTP-bejegyzés végrehajtására:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Ne feledje, ez mindenre működik. Létrehozhat SAS-t egy üzenetsor, témakör vagy előfizetés számára.

Ha SAS-tokent ad a küldőnek vagy az ügyfélnek, nem rendelkezik közvetlenül a kulccsal, és nem tudja visszafordítani a kivonatot a beszerzéséhez. Így Ön szabályozhatja, hogy mire férhet hozzá, és hogy mennyi ideig tart. Fontos megjegyezni, hogy ha módosítja a házirend elsődleges kulcsát, akkor az abból létrehozott megosztott hozzáférési aláírások érvénytelenítve lesznek.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>A közös hozzáférésű aláírás használata (AMQP szinten)

Az előző szakaszban megtudhatta, hogyan használhatja az SAS-tokent egy HTTP POST-kérelemmel az adatoknak a Service Bus való küldéséhez. Amint tudja, hozzáférhet a Service Bushoz a Advanced Message Queueing Protocol (AMQP) használatával, amely a teljesítmény szempontjából előnyben részesített protokoll, és számos forgatókönyv esetén használható. Az SAS-token AMQP-mel való használata a 2013-es [AMQP Claim-Based Security 1,0-es verziójában](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) van leírva, de az Azure által jelenleg támogatott.

Mielőtt megkezdené az adatküldést a Service Busba, a közzétevőnek az SAS-jogkivonatot egy **$CBS** nevű, jól DEFINIÁLt AMQP-csomópontra kell küldenie egy AMQP (a szolgáltatás által az összes sas-token beszerzéséhez és ellenőrzéséhez használt "speciális" üzenetsorként jelenik meg). A közzétevőnek meg kell adnia a **ReplyTo** MEZŐT a AMQP üzenetben; Ez az a csomópont, amelyben a szolgáltatás a jogkivonatok érvényesítésének eredményeképpen válaszol a közzétevőnek (egyszerű kérelem/válasz minta a közzétevő és a szolgáltatás között). Ez a válasz-csomópont "menet közben" jön létre, amely a "távoli csomópont dinamikus létrehozására" szól a AMQP 1,0 specifikációban leírtak szerint. Miután ellenőrizte, hogy az SAS-jogkivonat érvényes-e, a közzétevő mehet előre, és megkezdheti az adatküldést a szolgáltatásnak.

A következő lépések bemutatják, hogyan küldheti el az SAS-tokent a AMQP protokollal a [AMQP.net Lite](https://github.com/Azure/amqpnetlite) Library használatával. Ez akkor hasznos, ha nem használhatja a (z) C platformon fejleszthető hivatalos Service Bus SDK-t (például WinRT, .NET kompakt keretrendszer, .NET Micro Framework és mono) \# . Természetesen ez a könyvtár hasznos lehet annak megértésében, hogyan működik a jogcímek biztonsága a AMQP szinten, ahogy látta, hogyan működik a HTTP-szinten (HTTP POST-kéréssel és az "engedélyezés" fejlécben küldött SAS-tokenrel). Ha nincs szüksége ilyen mélyre az AMQP-ről, a hivatalos Service Bus SDK-val .NET-keretrendszerbeli alkalmazásokkal is elvégezheti a használatát.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

A `PutCbsToken()` metódus fogadja a *kapcsolat* (AMQP-kapcsolati osztály példányát, amelyet a [AMQP .net Lite Library](https://github.com/Azure/amqpnetlite)adott meg), amely a szolgáltatás TCP-kapcsolatát és a *sasToken* paramétert jelöli, amely a küldendő sas-jogkivonat.

> [!NOTE]
> Fontos, hogy a kapcsolat **SASL hitelesítési mechanizmussal** legyen létrehozva névtelen (és nem az alapértelmezett egyszerű felhasználónévvel és jelszóval, amelyet akkor használ, ha nem kell ELKÜLDENI az SAS-jogkivonatot).
>
>

Ezután a közzétevő két AMQP-hivatkozást hoz létre az SAS-token küldéséhez és a válasz fogadásához (jogkivonat-érvényesítési eredmény) a szolgáltatástól.

A AMQP-üzenet tulajdonságok készletét és további információkat tartalmaz, mint egy egyszerű üzenet. Az SAS-token az üzenet törzse (a konstruktorának használatával). A **"ReplyTo"** tulajdonság értéke a csomópont neve, amely a fogadó hivatkozáson keresztül fogadja az érvényesítési eredményt (ha szeretné, módosíthatja a nevét, és a szolgáltatás dinamikusan létrehozza azt). A szolgáltatás az utolsó három alkalmazást/egyéni tulajdonságot használja arra, hogy jelezze, milyen műveletet kell végrehajtania. A CBS-draft specifikációjának megfelelően a **művelet neve** ("Put-token"), a **jogkivonat típusa** (ebben az esetben a `servicebus.windows.net:sastoken` ) és annak a **célközönségnek a neve** , amelyre a jogkivonat vonatkozik (a teljes entitás).

Miután elküldte az SAS-tokent a küldő hivatkozáson, a közzétevőnek el kell olvasnia a választ a fogadó hivatkozáson. A válasz egy egyszerű AMQP üzenet, amely egy **"Status-Code"** nevű Application tulajdonságot tartalmaz, amely ugyanazokat az értékeket tartalmazza, mint a http-állapotkód.

## <a name="rights-required-for-service-bus-operations"></a>Service Bus műveletekhez szükséges jogosultságok

A következő táblázat a Service Bus erőforrásokon végzett különféle műveletekhez szükséges hozzáférési jogosultságokat mutatja be.

| Művelet | Jogcím szükséges | Jogcím hatóköre |
| --- | --- | --- |
| **Névtér** | | |
| Engedélyezési szabály konfigurálása névtérben |Kezelés |Bármely névtér címe |
| **Szolgáltatás beállításjegyzéke** | | |
| Privát szabályzatok enumerálása |Kezelés |Bármely névtér címe |
| Névtér figyelésének megkezdése |Figyelés |Bármely névtér címe |
| Üzenetek küldése egy figyelőnek egy névtérben |Küldés |Bármely névtér címe |
| **Üzenetsor** | | |
| Üzenetsor létrehozása |Kezelés |Bármely névtér címe |
| Üzenetsor törlése |Kezelés |Bármely érvényes várólista-címe |
| Várólisták enumerálása |Kezelés |/$Resources/Queues |
| A várólista leírásának beolvasása |Kezelés |Bármely érvényes várólista-címe |
| A várólista engedélyezési szabályának konfigurálása |Kezelés |Bármely érvényes várólista-címe |
| Küldés a várólistába |Küldés |Bármely érvényes várólista-címe |
| Üzenetek fogadása egy várólistából |Figyelés |Bármely érvényes várólista-címe |
| Üzenetek megszakítása vagy befejezése az üzenet betekintés-zárolási módban való fogadása után |Figyelés |Bármely érvényes várólista-címe |
| Üzenet késleltetése későbbi lekéréshez |Figyelés |Bármely érvényes várólista-címe |
| Üzenet kézbesítetlen levelek |Figyelés |Bármely érvényes várólista-címe |
| Az üzenetsor-munkamenethez társított állapot beolvasása |Figyelés |Bármely érvényes várólista-címe |
| Az üzenetsor-munkamenethez társított állapot beállítása |Figyelés |Bármely érvényes várólista-címe |
| A későbbi kézbesítésre vonatkozó üzenet beütemezett időpontja például: [ScheduleMessageAsync ()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Figyelés | Bármely érvényes várólista-címe
| **Témakör** | | |
| Üzenettémakör létrehozása |Kezelés |Bármely névtér címe |
| Témakör törlése |Kezelés |Bármely érvényes témakör címe |
| Témakörök enumerálása |Kezelés |/$Resources/topics |
| A témakör leírásának beolvasása |Kezelés |Bármely érvényes témakör címe |
| Egy témakör engedélyezési szabályának konfigurálása |Kezelés |Bármely érvényes témakör címe |
| Küldés a következő témakörbe |Küldés |Bármely érvényes témakör címe |
| **Előfizetés** | | |
| Előfizetés létrehozása |Kezelés |Bármely névtér címe |
| Előfizetés törlése |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Előfizetések számbavétele |Kezelés |.. /myTopic/Subscriptions |
| Előfizetés lekérése – Leírás |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Üzenetek megszakítása vagy befejezése az üzenet betekintés-zárolási módban való fogadása után |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| Üzenet késleltetése későbbi lekéréshez |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| Üzenet kézbesítetlen levelek |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| A témakör-munkamenethez társított állapot beolvasása |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| Témakör-munkamenethez társított állapot beállítása |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| **Szabályok** | | |
| Szabály létrehozása |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Szabály törlése |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Szabályok számbavétele |Kezelés vagy figyelés |.. /myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Következő lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [How to use Service Bus Queues](service-bus-dotnet-get-started-with-queues.md) (A Service Bus-üzenetsorok használata)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
