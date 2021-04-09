---
title: Bemeneti ellenőrzés – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Tudnivalók a Threat Modeling Tool bemeneti ellenőrzéséről. Tekintse meg a kockázatcsökkentő információkat és a kód megjelenítése példákat.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: c816fbad05831c83c891c70849986b38cb7fdbeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589542"
---
# <a name="security-frame-input-validation--mitigations"></a>Biztonsági keret: bemeneti ellenőrzés | Enyhítését 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Az XSLT-parancsfájlok letiltása az összes átalakításhoz nem megbízható stíluslapok használatával](#disable-xslt)</li><li>[Győződjön meg arról, hogy minden olyan oldal, amely tartalmazhatja a felhasználó által ellenőrizhető tartalmat, az automatikus MIME-elemzést választja ki](#out-sniffing)</li><li>[XML-entitás feloldásának megerősítése vagy letiltása](#xml-resolution)</li><li>[Az URL-címek szabványosítási ellenőrzését http.sys használó alkalmazások](#app-verification)</li><li>[Győződjön meg arról, hogy a megfelelő vezérlők vannak érvényben a fájlok felhasználóktól való fogadásakor](#controls-users)</li><li>[Győződjön meg arról, hogy a Type-Safe paraméterek használatban vannak a webalkalmazásban adateléréshez](#typesafe)</li><li>[Az MVC tömeges hozzárendelési biztonsági rések elkerülése érdekében használjon külön modell kötési osztályokat vagy kötési szűrői listát](#binding-mvc)</li><li>[Nem megbízható webes kimenet kódolása a renderelés előtt](#rendering)</li><li>[A bemeneti ellenőrzés és a szűrés végrehajtása az összes karakterlánc típusú modell tulajdonságainál](#typemodel)</li><li>[A tisztítást olyan űrlapmezők kell alkalmazni, amelyek az összes karaktert elfogadják, például Rich Text Editor](#richtext)</li><li>[Ne rendeljen DOM-elemeket olyan mosogatóhoz, amelyek nem rendelkeznek beépített kódolással](#inbuilt-encode)</li><li>[Az alkalmazáson belüli összes átirányítás ellenőrzése bezárult, vagy biztonságosan történik](#redirect-safe)</li><li>[A bemeneti ellenőrzés megvalósítása a vezérlő metódusok által elfogadott összes karakterlánc típusú paraméternél](#string-method)</li><li>[A reguláris kifejezések feldolgozásának felső korlátjának beállítása, hogy a DoS ne legyen rossz reguláris kifejezés miatt](#dos-expression)</li><li>[Kerülje a HTML. Raw használatát a borotva nézeteiben](#html-razor)</li></ul> | 
| **Adatbázis** | <ul><li>[Dinamikus lekérdezések használata a tárolt eljárásokban](#stored-proc)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a modell ellenőrzése a webes API-módszerekkel történik](#validation-api)</li><li>[A bemeneti ellenőrzés megvalósítása a webes API-metódusok által elfogadott összes karakterlánc típusú paraméternél](#string-api)</li><li>[Győződjön meg arról, hogy a típus-biztonságos paramétereket használják a webes API-ban az adatok eléréséhez](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Paraméteres SQL-lekérdezések használata Azure Cosmos DBhoz](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-bemenet ellenőrzése séma kötésével](#schema-binding)</li><li>[WCF – bemeneti ellenőrzés paraméter-ellenőrök használatával](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Az XSLT-parancsfájlok letiltása az összes átalakításhoz nem megbízható stíluslapok használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [XSLT-biztonság](/previous-versions/windows/desktop/ms763800(v=vs.85)), [XsltSettings. EnableScript tulajdonság](/dotnet/api/system.xml.xsl.xsltsettings.enablescript) |
| **Lépések** | Az XSLT támogatja a stíluslapokon belüli parancsfájlok használatát a `<msxml:script>` elemmel. Ez lehetővé teszi az egyéni függvények használatát egy XSLT-átalakításban. A szkript az átalakítást végző folyamat kontextusában lesz végrehajtva. Az XSLT-szkriptet le kell tiltani, ha nem megbízható környezetben van a nem megbízható kód végrehajtásának megakadályozása. *.Net használata esetén:* Az XSLT-parancsfájlok alapértelmezés szerint le vannak tiltva; azonban gondoskodnia kell arról, hogy a tulajdonságon keresztül ne legyen explicit módon engedélyezve `XsltSettings.EnableScript` .|

### <a name="example"></a>Példa 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
Ha az MSXML 6,0-et használja, az XSLT-parancsfájlok alapértelmezés szerint le vannak tiltva; gondoskodnia kell azonban arról, hogy az nem lett explicit módon engedélyezve az XML DOM Object tulajdonság AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
Ha az MSXML 5-öt vagy a-t használja, az XSLT-parancsfájlok alapértelmezés szerint engedélyezve vannak, ezért explicit módon le kell tiltani. Állítsa hamis értékre a AllowXsltScript XML DOM Object tulajdonságát. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Győződjön meg arról, hogy minden olyan oldal, amely tartalmazhatja a felhasználó által ellenőrizhető tartalmat, az automatikus MIME-elemzést választja ki

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [IE8 biztonsági rész V – átfogó védelem](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **Lépések** | <p>Minden olyan oldal esetében, amely tartalmazhatja a felhasználó által ellenőrizhető tartalmat, a HTTP-fejlécet kell használnia `X-Content-Type-Options:nosniff` . Ahhoz, hogy megfeleljen ennek a követelménynek, beállíthatja a szükséges fejléc oldalát csak azokhoz a lapokhoz, amelyek felhasználó által ellenőrizhető tartalmat tartalmazhatnak, vagy az alkalmazás összes lapjára vonatkozóan globálisan is megadható.</p><p>A webkiszolgálóról érkező minden fájltípushoz tartozik egy társított [MIME-típus](https://en.wikipedia.org/wiki/Mime_type) (más néven *Content-Type*), amely a tartalom természetét írja le (azaz a képet, a szöveget, az alkalmazást stb.).</p><p>Az X-Content-Type-Options fejléc egy HTTP-fejléc, amely lehetővé teszi a fejlesztők számára, hogy a tartalmuk ne legyenek MIME-szippantva. Ez a fejléc a MIME-Sniffing támadások enyhítésére szolgál. A fejléc támogatása az Internet Explorer 8 (IE8) szolgáltatásban lett hozzáadva</p><p>Csak az Internet Explorer 8 (IE8) felhasználói élvezhetik az X-Content-Type-Options előnyeit. Az Internet Explorer korábbi verziói jelenleg nem veszik figyelembe az X-Content-Type-Options fejlécet</p><p>Az Internet Explorer 8 (és újabb verziók) a MIME-szippantás letiltási funkciójának megvalósítására szolgáló egyetlen fő böngésző. Ha és ha más nagyobb böngészők (Firefox, Safari, Chrome) hasonló funkciókat valósítanak meg, a rendszer frissíti a javaslatot, hogy tartalmazza a böngészők szintaxisát is.</p>|

### <a name="example"></a>Példa
Ha az alkalmazás összes oldalához globálisan szeretné engedélyezni a szükséges fejlécet, a következők egyikét teheti: 

* Adja hozzá a fejlécet a web.config fájlban, ha az alkalmazást Internet Information Services (IIS) 7 üzemelteti 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* A fejléc hozzáadása a globális alkalmazás \_ BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Egyéni HTTP-modul implementálása 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* A szükséges fejlécet csak adott lapokon engedélyezheti, ha hozzáadja azt az egyes válaszokhoz: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>XML-entitás feloldásának megerősítése vagy letiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [XML-entitások bővítése](https://capec.mitre.org/data/definitions/197.html), [XML-megtagadás a szolgáltatással kapcsolatos támadások és védelem](/archive/msdn-magazine/2009/november/xml-denial-of-service-attacks-and-defenses), az [MSXML biztonsági áttekintése](/previous-versions/windows/desktop/ms754611(v=vs.85)), [ajánlott eljárások az MSXML-kód biztonságossá tételéhez](/previous-versions/windows/desktop/ms759188(v=vs.85)), [NSXMLParserDelegate protokoll-hivatkozás](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [külső hivatkozások feloldása](/dotnet/standard/data/xml/resolving-external-resources) |
| **Lépések**| <p>Bár a szolgáltatás nem széles körben használatos, az XML olyan funkciója, amely lehetővé teszi, hogy az XML-elemző kibontsa a saját dokumentumon belül vagy külső forrásokból definiált értékekkel rendelkező makró entitásokat. Előfordulhat például, hogy a dokumentum a "cégnév" nevű entitást definiálja a "Microsoft" értékkel, így minden alkalommal, amikor a " &companyname; " szöveg megjelenik a dokumentumban, a rendszer automatikusan lecseréli a Microsoft szövegére. Vagy a dokumentum definiálhat egy "MSFTStock" nevű entitást, amely egy külső webszolgáltatásra hivatkozik a Microsoft Stock aktuális értékének beolvasásához.</p><p>&MSFTStock;A dokumentumban bármikor megjelenik a "" üzenet, és a rendszer automatikusan lecseréli a jelenlegi tőzsdei árfolyamra. Azonban ez a funkció a szolgáltatásmegtagadási (DoS) feltételek létrehozására is felhasználható. Egy támadó több entitást is beágyazhat egy exponenciális expanziós XML-bomba létrehozására, amely az összes rendelkezésre álló memóriát felhasználja a rendszeren. </p><p>Azt is megteheti, hogy létrehoz egy külső hivatkozást, amely végtelen mennyiségű adatról küld vissza adatfolyamot, vagy egyszerűen lefagy a szálon. Ennek eredményeképpen minden csapatnak teljes mértékben le kell tiltania a belső és/vagy a külső XML-entitás feloldását, ha az alkalmazás nem használja, vagy manuálisan korlátozza a memória és az idő mennyiségét, amelyet az alkalmazás felhasználhat az entitás feloldásához, ha ez a funkció feltétlenül szükséges. Ha az alkalmazás nem igényli az entitások feloldását, tiltsa le azt. </p>|

### <a name="example"></a>Példa
A .NET-keretrendszer kódjához a következő módszereket használhatja:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Vegye figyelembe, hogy az alapértelmezett `ProhibitDtd` értéke `XmlReaderSettings` true (igaz), de `XmlTextReader` hamis. Ha a XmlReaderSettings-t használja, nem kell explicit módon beállítania a ProhibitDtd-t, de a biztonsági kedvéért ajánlott. Azt is vegye figyelembe, hogy a XmlDocument attribútummal osztály alapértelmezés szerint engedélyezi az entitások feloldását. 

### <a name="example"></a>Példa
Az entitások XmlDocuments letiltásához használja a Load metódus túlterhelését, `XmlDocument.Load(XmlReader)` és állítsa be a megfelelő tulajdonságokat a XmlReader argumentumban a felbontás letiltásához, ahogyan az a következő kódban látható: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Példa
Ha az entitások feloldásának letiltása nem lehetséges az alkalmazáshoz, az alkalmazás igényeinek megfelelően állítsa be az XmlReaderSettings. MaxCharactersFromEntities tulajdonságot ésszerű értékre. Ez korlátozza a potenciális exponenciális expanziós DoS-támadások hatását. A következő kód példát nyújt erre a megközelítésre: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Példa
Ha a beágyazott entitásokat fel kell oldania, de nem kell külső entitásokat feloldania, állítsa a XmlReaderSettings.Xmlresolver tulajdonságot NULL értékre. Például: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Vegye figyelembe, hogy a MSXML6 a ProhibitDTD értéke TRUE (a DTD-feldolgozás letiltása) alapértelmezés szerint. Az Apple OSX/iOS-kód esetében két XML-elemző használható: NSXMLParser és libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Az URL-címek szabványosítási ellenőrzését http.sys használó alkalmazások

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Az http.syst használó alkalmazásoknak az alábbi irányelveket kell követniük:</p><ul><li>Az URL-cím hosszának legfeljebb 16 384 karakternél (ASCII vagy Unicode) való korlátozása. A maximális URL-cím hossza az alapértelmezett Internet Information Services (IIS) 6-os beállításán alapul. A webhelyeknek a lehető legrövidebbre kell törekedniük, ha lehetséges</li><li>Használja a .NET-keretrendszer szabványos I/O-osztályait (például FileStream), mivel ezek kihasználják a .NET FX szabványosítási szabályait</li><li>Az ismert fájlnevek engedélyezési listájának explicit létrehozása</li><li>Az ismert fájltípusok explicit elutasítása nem fogja kiszolgálni az UrlScan elutasításait: exe, bat, cmd, com, HTW, Ida, IDQ, HTR, IDC, shtm [l], STM, Printer, ini, pol, dat Files</li><li>A következő kivételek elfogása:<ul><li>System. ArgumentException (az eszközök neveihez)</li><li>System. NotSupportedException (adatfolyamok esetén)</li><li>System. IO. FileNotFoundException (érvénytelen Escape-fájlnevek esetén)</li><li>System. IO. DirectoryNotFoundException (érvénytelen Escape-dirs)</li></ul></li><li>*Ne hívja meg* a Win32-fájlok I/O-API-jait. Érvénytelen URL-cím esetén a rendszer egy 400-es hibát ad vissza a felhasználónak, és naplózza a valós hibát.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Győződjön meg arról, hogy a megfelelő vezérlők vannak érvényben a fájlok felhasználóktól való fogadásakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Korlátlan](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)fájlfeltöltés, [fájl aláírása tábla](https://www.garykessler.net/library/file_sigs.html) |
| **Lépések** | <p>A feltöltött fájlok jelentős kockázatot jelentenek az alkalmazások számára.</p><p>A sok támadás első lépése, hogy a rendszer egy kódot kap a támadáshoz. Ezután a támadásnak csak meg kell találnia a kód végrehajtásának módját. A fájlfeltöltés használatával a támadó elvégezheti az első lépést. A nem korlátozott fájlfeltöltés következményei eltérőek lehetnek, beleértve a rendszer teljes átvételét, túlterhelt fájlrendszerét vagy adatbázisát, a támadásokat a háttérrendszer-rendszerekre, valamint az egyszerű megjelenítést.</p><p>Ez attól függ, hogy az alkalmazás mit tesz a feltöltött fájllal, és különösen hol tárolja. A fájlfeltöltés kiszolgálóoldali érvényesítése hiányzik. A fájlfeltöltés funkciójának megvalósításához a következő biztonsági ellenőrzéseknek kell megvalósulnia:</p><ul><li>Fájlkiterjesztés-ellenőrzés (csak az engedélyezett fájltípus érvényes készletét kell elfogadni)</li><li>Maximális fájlméret korlátja</li><li>A fájl nem tölthető fel a Webroot-ba; a helynek a nem rendszermeghajtón lévő könyvtárnak kell lennie</li><li>Az elnevezési konvenciót követni kell, hogy a feltöltött fájl neve véletlenszerű legyen, így elkerülhető legyen a fájlok felülírása.</li><li>A fájloknak a lemezre írás előtt meg kell vizsgálniuk a víruskeresőt</li><li>Győződjön meg arról, hogy a fájl neve és az egyéb metaadatok (például a fájl elérési útja) kártékony karakterekre vannak érvényesítve</li><li>Ha meg szeretné akadályozni, hogy a felhasználó feltöltsön egy álcázott fájlt (például egy exe-fájl feltöltését a txt kiterjesztésének módosításával), jelölje be a fájl formátumának aláírása jelölőnégyzetet.</li></ul>| 

### <a name="example"></a>Példa
A fájlformátum-aláírás ellenőrzésével kapcsolatos utolsó pontnál a részletekért tekintse meg az alábbi osztályt: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Győződjön meg arról, hogy a Type-Safe paraméterek használatban vannak a webalkalmazásban adateléréshez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Ha a parameters (paraméterek) gyűjteményt használja, az SQL a bemenetet literál értékként adja meg a végrehajtható kód helyett. A paraméterek gyűjteménye a bemeneti adatok típusának és hosszának korlátozására használható. A tartományon kívüli értékek kivételt váltanak ki. Ha nem használja a Type-Safe SQL-paramétereket, a támadók képesek lehetnek a szűretlen bemenetbe ágyazott injekciós támadások végrehajtására.</p><p>SQL-lekérdezések létrehozásakor használjon biztonságos paramétereket, hogy elkerülje a nem szűrt bevitelsel esetlegesen fellépő lehetséges SQL-injektálási támadásokat. A biztonságos paramétereket tárolt eljárásokkal és dinamikus SQL-utasításokkal is használhatja. A paramétereket literál értékként kezeli az adatbázis, nem pedig végrehajtható kódként. A paramétereket a típus és a hossz is ellenőrzi.</p>|

### <a name="example"></a>Példa 
A következő kód azt mutatja be, hogyan használhatók a biztonságos paraméterek a SqlParameterCollection egy tárolt eljárás meghívásakor. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
A fenti példában a bemeneti érték nem lehet hosszabb 11 karakternél. Ha az érték nem felel meg a paraméter által meghatározott típusnak vagy hossznak, a SqlParameter osztály kivételt jelez. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Az MVC tömeges hozzárendelési biztonsági rések elkerülése érdekében használjon külön modell kötési osztályokat vagy kötési szűrői listát

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Metaadat-attribútumok](/dotnet/api/system.componentmodel.dataannotations.metadatatypeattribute), [nyilvános kulcsú biztonsági rések és enyhítés](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [teljes körű útmutató a TÖMEGES hozzárendeléshez a ASP.net MVC-ben](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx) [első lépések az EF-sel az MVC használatával](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Lépések** | <ul><li>**Mikor érdemes megkeresni a túlzott közzétételi biztonsági réseket?-** A feladatátvétel során felmerülő biztonsági rések bármilyen helyet felhasználhatnak a felhasználói bemenetből származó modell-osztályok kötéséhez. Az MVC-hez hasonló keretrendszerek az egyéni .NET-osztályokban lévő felhasználói adathalmazokat, például a régi CLR-objektumokat (POCOs) is képviselik. Az MVC automatikusan kitölti ezeket a modell-osztályokat a kérelemből származó adatokkal, így kényelmes ábrázolást biztosít a felhasználói adatbevitelsel kapcsolatban. Ha ezek az osztályok olyan tulajdonságokat tartalmaznak, amelyeket a felhasználónak nem kell beállítania, az alkalmazás sebezhető lehet a túlzott közzétételi támadásokkal szemben, ami lehetővé teszi az alkalmazás által soha nem tervezett adatmennyiségek felhasználói vezérlését. Az MVC Model-kötésekhez hasonlóan az adatbázis-hozzáférési technológiák, például az objektum-vagy kapcsolati leképezések, például a Entity Framework gyakran támogatják a POCO-objektumok használatát az adatbázis-adathalmazok ábrázolásához. Ezek az adatmodell-osztályok ugyanazt a kényelmet nyújtják, mint az adatbázis-adatok MVC-vel való kezelése a felhasználói bevitel kezelése során. Mivel az MVC és az adatbázis egyaránt támogatja a hasonló modelleket, például a POCO-objektumokat, úgy tűnik, hogy az azonos osztályok mindkét célra való használata egyszerű. Ezzel a gyakorlattal nem lehet megőrizni a problémáinak elkülönítését, és ez egy olyan közös terület, ahol a nem szándékolt tulajdonságok a modell kötésének teszik lehetővé, így többek között a feladási támadásokat is.</li><li>**Miért érdemes a nem szűrt adatbázismodell-osztályokat paraméterként használni az MVC-műveletekhez?-** Mivel az MVC-modell kötése ehhez az osztályhoz is kötődik. Még ha az adatok nem jelennek meg a nézetben, a rosszindulatú felhasználók HTTP-kérést küldhetnek ezekkel az adatokkal, az MVC pedig szívesen kötődik, mivel a művelet azt mondja, hogy az adatbázis-osztály a felhasználói bevitelhez szükséges adatok formája.</li><li>**Miért érdemes érdekelni a modell kötéséhez használt alakzatot? –** A ASP.NET MVC-modell kötése a túlságosan széleskörű modellekkel lehetővé teszi, hogy egy alkalmazás több feladási támadást használjon. A túlzott közzététel lehetővé teheti a támadók számára, hogy megváltoztassák az alkalmazásadatok körét a fejlesztő által tervezett módon, például felülbírálják egy adott elem árát vagy a fiók biztonsági jogosultságait. Az alkalmazásoknak a műveletekre vonatkozó kötési modelleket (vagy a meghatározott engedélyezési tulajdonságokat tartalmazó listát) kell használniuk ahhoz, hogy explicit szerződést adjanak a nem megbízható bemenet számára a modell kötésének engedélyezéséhez.</li><li>**Külön kötési modellek vannak, csak a kód duplikálása?-** Nem, ez a probléma elkülönítése. Ha az adatbázis-modelleket újrahasznosítja a műveleti módszerekben, azt mondja, hogy az osztályban található bármely tulajdonság (vagy altulajdonság) a felhasználó által HTTP-kérelemben állítható be. Ha nem az a teendője, hogy az MVC-t szeretné elvégezni, akkor szükség van egy szűrési listára vagy egy különálló osztály alakzatra, amely azt mutatja, hogy a felhasználói adatbevitelből milyen adatok származhatnak.</li><li>**Ha a felhasználói adatbevitelhez külön kötési modellek vannak, akkor az összes adatom megjegyzése attribútumot kell duplikálni?-** Nem feltétlenül. A MetadataTypeAttribute az adatbázis-modell osztályban használhatja a modell kötési osztály metaadataihoz való hivatkozáshoz. Csak vegye figyelembe, hogy a MetadataTypeAttribute által hivatkozott típusnak a hivatkozó típus egy részhalmazának kell lennie (kevesebb tulajdonsággal rendelkezhet, de nem több).</li><li>**Az adatok visszahelyezése a felhasználói bemeneti modellek és az adatbázis-modellek között unalmas. Csak a reflexió használatával másolhatom az összes tulajdonságot? –** Igen. A kötési modellekben megjelenő tulajdonságok közül csak azokat a tulajdonságokat kell megadnia, amelyeket a felhasználói bevitel biztonsága érdekében határozott meg. Nincs olyan biztonsági ok, amely megakadályozza, hogy a rendszer átmásolja az összes olyan tulajdonságot, amely a közös két modell között létezik.</li><li>**Mi a helyzet [kötés (kizár = "â &euro; ¦")]. Használhatom külön kötési modellek helyett? –** Ez a megközelítés nem ajánlott. A (z) [kötés (kizárás = "β &euro; ¦")] használatával az új tulajdonság alapértelmezés szerint köthető. Ha új tulajdonságot ad hozzá, egy további lépésre van szüksége, hogy a rendszer ne legyen biztonságos, és ne kelljen alapértelmezetten megadnia a dolgokat. Attól függően, hogy a fejlesztő hogyan ellenőrzi a listát, minden alkalommal, amikor hozzáad egy tulajdonságot, kockázatos.</li><li>**A [kötés (include = "ă &euro; ¦")]] hasznos a szerkesztési műveletekhez?-** nem. [A kötés (Belefoglalás = "β &euro; ¦")] csak beszúrási stílusú műveletekhez használható (új adatok hozzáadása). FRISSÍTÉSI stílusú műveletekhez (meglévő adatok módosítása) használjon másik módszert, például külön kötési modelleket, vagy az engedélyezett tulajdonságok explicit listájának átadását UpdateModel vagy TryUpdateModel. A [kötés (include = "â &euro; ¦]]] attribútum egy szerkesztési művelethez való hozzáadása azt jelenti, hogy az MVC létrehoz egy objektumot, és csak a felsorolt tulajdonságokat állítja be, így az összes többi alapértelmezett értékük is megmarad. Ha az adatok megmaradnak, a rendszer teljes mértékben lecseréli a meglévő entitást, és visszaállítja a kihagyott tulajdonságok értékeit az alapértelmezett értékekre. Ha például a IsAdmin egy [kötés (include = "ă &euro; ¦")] attribútumból lett kihagyva egy szerkesztési művelethez, akkor minden olyan felhasználó, akinek a neve a művelettel lett szerkesztve, a isAdmin = false értékre lesz visszaállítva (bármely szerkesztett felhasználó elveszíti a rendszergazdai állapotot). Ha meg szeretné akadályozni bizonyos tulajdonságok frissítését, használja a fenti módszerek egyikét. Vegye figyelembe, hogy az MVC-eszközök egyes verziói a szerkesztési műveletekben a [kötés (include = "â ¦]]] nevű vezérlő osztályt hoznak létre, &euro; és azt is sugallják, hogy az adott listáról származó tulajdonságok eltávolításával elkerülhetők a túlzott közzétételi támadások. A fentiekben leírtak szerint azonban ez a megközelítés nem a kívánt módon működik, hanem visszaállítja a kihagyott tulajdonságok adatait az alapértelmezett értékekre.</li><li>**A létrehozási műveletekhez a (z) [bind (include = "β ¦")] használatával kapcsolatos kikötések nem &euro; külön kötési modellek helyett? –** igen. Első lépésként ez a módszer nem működik szerkesztési forgatókönyvek esetén, és két külön megközelítés fenntartását igényli az összes túlzott biztonsági rések enyhítésére. Másodszor, külön kötési modellek kényszerítik a felhasználói bevitelhez használt alakzat és az adatmegőrzéshez használt alakzat közötti eltérések elkülönítését, ami [bind (include = "â &euro; ¦")] Harmadszor, vegye figyelembe, hogy a [kötés (include = "â &euro; ¦")] csak a legfelső szintű tulajdonságokat tudja kezelni; nem engedélyezheti csak az altulajdonságok (például "details.Name") egyes részeit az attribútumban. Végül, és talán a legfontosabb, hogy a [bind (include = "â &euro; ¦)] használatával hozzáad egy extra lépést, amelyet minden alkalommal meg kell jegyeznünk, amikor az osztályt a modell kötéséhez használják. Ha egy új műveleti módszer közvetlenül az adatosztályhoz van kötve, és elfelejti a [kötés (include = "â &euro; ¦")] attribútum hozzáadását, akkor sebezhető lehet a túlzott közzétételi támadásokkal szemben, így a [kötés (include = "β &euro; ¦")] a megközelítés valamivel kevésbé biztonságos az alapértelmezett módon. Ha [kötés (include = "β &euro; ¦")] használatát használja, ügyeljen arra, hogy mindig jegyezze meg, hogy minden alkalommal, amikor az adatosztályok műveleti módszerként jelennek meg.</li><li>A **létrehozási műveletekhez, mi a helyzet a [kötés (include = "â &euro; ¦")] attribútumnak a modell osztályban való elhelyezésével? Nem ez a megközelítés megakadályozza, hogy az attribútumot minden egyes műveleti módszerre feljegyezze? –** Ez a megközelítés bizonyos esetekben működik. Ha a (z) [kötés (Belefoglalás = "β &euro; ¦")] értéket használja a modell típusához (nem az adott osztályt használó műveleti paramétereknél), akkor ne feledje, hogy ne kelljen megjegyeznünk a [kötés (include = "No =" " &euro; Ha közvetlenül az osztályban használja az attribútumot, a modell kötése érdekében az osztály egy különálló felületét hozza létre. Ez a megközelítés azonban csak egy modellhez tartozó kötési alakzat használatát teszi lehetővé modell osztályban. Ha egy műveleti metódusnak engedélyezni kell egy mezőhöz tartozó modell kötését (például egy csak rendszergazdai művelet, amely frissíti a felhasználói szerepköröket), és más műveleteknek is meg kell akadályoznia a mezőhöz való kötést, ez a megközelítés nem fog működni. Minden osztály csak egy modell kötési alakzattal rendelkezhet. Ha a különböző műveletekhez eltérő modell típusú kötési alakzatok szükségesek, akkor ezeket a különálló alakzatokat külön modell kötési osztályok vagy külön [bind (include = "β &euro; ¦")] attribútumok használatával kell elvégezniük a műveleti metódusokban.</li><li>**Mik azok a kötési modellek? Ugyanazok, mint a modellek megtekintése? -** Ez két kapcsolódó fogalom. A kötési modell kifejezés a művelet paramétereinek listájában használt modell osztályra hivatkozik (az MVC-modell kötése a műveleti metódusnak átadott alakzat). A kifejezés nézet modell a műveleti módszerből egy nézetbe átadott modell osztályra hivatkozik. A nézet-specifikus modell használata egy általános megközelítés, amely az adatok egy adott műveletből egy nézetbe való átadására szolgál. Ez az alakzat gyakran alkalmas a modell kötésére is, és a kifejezés nézet modellje mindkét helyen ugyanazt a modellt használhatja. Ahhoz, hogy pontosak legyenek, ez az eljárás kifejezetten a kötési modellekről beszél, a műveletnek átadott alakzatra koncentrálva, amely a tömeges hozzárendelések szempontjából fontos.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Nem megbízható webes kimenet kódolása a renderelés előtt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, Web Forms, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | A [helyek közötti parancsfájlok megakadályozása a ASP.net-ben](/previous-versions/msp-n-p/ff649310(v=pandp.10)), a [helyek közötti parancsfájlkezelés](https://cwe.mitre.org/data/definitions/79.html), az [XSS (Cross Site Scripting) megelőzése – Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Lépések** | A helyek közötti parancsfájlok (általában az XSS-ként rövidítve) a online szolgáltatások vagy bármely olyan alkalmazás/összetevő támadási vektora, amely a webről érkező adatokat használja. Az XSS biztonsági rései lehetővé tehetik, hogy a támadók egy sebezhető webalkalmazáson keresztül hajtsák végre a parancsfájlokat egy másik felhasználó számítógépén. A rosszindulatú parancsfájlok a cookie-k lopására használhatók, és egyéb módon nem módosíthatók az áldozat gépén a JavaScript használatával. Az XSS megakadályozása a felhasználói adatok érvényesítésével, a megfelelő formátum és a kódolás megkezdése előtt, hogy a weblap megjeleníthető legyen. A bemeneti ellenőrzés és a kimeneti kódolás a web Protection Library használatával végezhető el. A felügyelt kód (C \# , VB.net stb.) esetében használjon egy vagy több megfelelő kódolási módszert a web Protection (anti-XSS) könyvtárból attól függően, hogy a felhasználói bevitel milyen kontextusban nyilvánul meg:| 

### <a name="example"></a>Példa

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>A bemeneti ellenőrzés és a szűrés végrehajtása az összes karakterlánc típusú modell tulajdonságainál

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Érvényesítés hozzáadása](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), a [modell-adattípusok érvényesítése MVC-alkalmazásokban](/previous-versions/dd410404(v=vs.90)), [a ASP.net MVC-alkalmazások irányadó alapelvei](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Lépések** | <p>Az alkalmazásban való használat előtt az összes bemeneti paramétert ellenőrizni kell, hogy az alkalmazás védelmet biztosítson a rosszindulatú felhasználói adatbevitelek ellen. Érvényesítse a bemeneti értékeket a kiszolgálói oldalon található reguláris kifejezési érvényesítéssel, egy engedélyezési lista-ellenőrzési stratégia használatával. A nem fertőtlenített felhasználói bemenetek/a metódusoknak átadott paraméterek a kód injekciós biztonsági réseit okozhatják.</p><p>A webalkalmazások esetében a belépési pontok tartalmazhatnak űrlapmezőket, QueryStrings, cookie-kat, HTTP-fejléceket és webszolgáltatás-paramétereket is.</p><p>A következő bemeneti ellenőrzési ellenőrzéseket kell végrehajtani a modell kötése esetén:</p><ul><li>A modell tulajdonságait válaszban jegyzettel kell kiegészíteni, az engedélyezett karakterek fogadásához és a megengedett maximális hosszhoz.</li><li>A vezérlő módszereinek ModelState érvényességét kell végrehajtania</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>A tisztítást olyan űrlapmezők kell alkalmazni, amelyek az összes karaktert elfogadják, például Rich Text Editor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | Nem [biztonságos bemenet kódolása](/previous-versions/msp-n-p/ff647397(v=pandp.10)#paght000003_step3), [HTML-Sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Lépések** | <p>Azonosítsa az összes használni kívánt statikus jelölő címkét. Gyakori eljárás a biztonságos HTML-elemek `<b>` (például félkövér) és a `<i>` (dőlt) formázásának korlátozása.</p><p>Az adatírás előtt HTML-kódolással kódolja. Így a rosszindulatú parancsfájlok biztonsága úgy történik, hogy azt szövegként, nem pedig végrehajtható programkódként kezeli.</p><ol><li>A ASP.NET-kérelem ellenőrzésének letiltása a ValidateRequest = "false" attribútum hozzáadásával az \@ oldal irányelvéhez</li><li>A karakterlánc bemenetének kódolása a HtmlEncode metódussal</li><li>Használjon StringBuilder, és hívja meg a Replace metódust az engedélyezni kívánt HTML-elemek kódolásának szelektív eltávolításához.</li></ol><p>A hivatkozásban szereplő lap letiltja a ASP.NET kérelmek érvényesítését a beállítással `ValidateRequest="false"` . HTML – kódolja a bemenetet, és szelektíven lehetővé teszi, hogy a `<b>` `<i>` .net-kódtár HTML-tisztítás is használható legyen.</p><p>A HtmlSanitizer egy .NET-kódtár, amely az XSS-támadásokhoz vezethet a HTML-töredékek és a szerkezetek dokumentumainak tisztításához. AngleSharp használ a HTML és a CSS elemzéséhez, kezeléséhez és megjelenítéséhez. A HtmlSanitizer NuGet-csomagként telepíthetők, és a felhasználói bevitel a megfelelő HTML-vagy CSS-tisztítási módszerekkel is továbbítható a kiszolgáló oldalán. Vegye figyelembe, hogy a tisztítást biztonsági ellenőrzésként csak utolsó lehetőségként kell figyelembe venni.</p><p>A bemeneti ellenőrzés és a kimeneti kódolás jobb biztonsági szabályozásnak minősül.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Ne rendeljen DOM-elemeket olyan mosogatóhoz, amelyek nem rendelkeznek beépített kódolással

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | A JavaScript-függvények többsége alapértelmezés szerint nem végez kódolást. Ha nem megbízható bevitelt ad hozzá a DOM-elemekhez ezen függvények használatával, előfordulhat, hogy a helyek közötti parancsfájl (XSS) végrehajtást eredményez.| 

### <a name="example"></a>Példa
A következő példák nem biztonságosak: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Ne használja `innerHtml` ; helyette használja `innerText` . Hasonlóképpen, a helyett `$("#elm").html()` használja a `$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Az alkalmazáson belüli összes átirányítás ellenőrzése bezárult, vagy biztonságosan történik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [A OAuth 2,0 engedélyezési keretrendszere – nyitott átirányítók](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Lépések** | <p>A felhasználó által megadott helyre történő átirányítást igénylő alkalmazás-kialakításnak a lehetséges átirányítási célokat a helyek vagy tartományok előre meghatározott "biztonságos" listájára kell korlátoznia. Az alkalmazásban lévő összes átirányításnak zárt/biztonságosnak kell lennie.</p><p>Ehhez tegye a következőket:</p><ul><li>Az összes átirányítások azonosítása</li><li>Az egyes átirányítás megfelelő enyhítésének megvalósítása. A megfelelő enyhítések közé tartozik az átirányítást engedélyező lista vagy a felhasználó megerősítése. Ha egy webhelynek vagy szolgáltatásnak nyílt átirányítási biztonsági rése a Facebook/OAuth/OpenID Identity Providers szolgáltatást használja, a támadó ellophatja a felhasználó bejelentkezési jogkivonatát, és megszemélyesítheti azt. Ez az OAuth használata során fennálló kockázat, amely a 6749-as számú RFC "The OAuth 2,0 engedélyezési keretrendszer" című szakaszban található, 10,15-as "nyitott átirányítások"</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>A bemeneti ellenőrzés megvalósítása a vezérlő metódusok által elfogadott összes karakterlánc típusú paraméternél

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | A [modell adatai egy MVC-alkalmazásban](/previous-versions/dd410404(v=vs.90)), [a ASP.net MVC-alkalmazások irányadó alapelvei](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Lépések** | Az olyan metódusok esetében, amelyek csak egyszerű adattípust fogadnak el, és nem a modell argumentumként, a normál kifejezéssel végzett bemeneti ellenőrzést kell elvégezni. Itt a regex. IsMatch érvényes regex-mintával kell használni. Ha a bemenet nem felel meg a megadott reguláris kifejezésnek, a vezérlő nem folytathatja a műveletet, és az érvényesítési hibákra vonatkozó megfelelő figyelmeztetést kell megjeleníteni.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>A reguláris kifejezések feldolgozásának felső korlátjának beállítása, hogy a DoS ne legyen rossz reguláris kifejezés miatt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, Web Forms, MVC5, MVC6  |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [DefaultRegexMatchTimeout tulajdonság](/dotnet/api/system.web.configuration.httpruntimesection.defaultregexmatchtimeout) |
| **Lépések** | A helytelenül létrehozott reguláris kifejezésekkel szembeni szolgáltatásmegtagadási támadások megelőzése érdekében, amely sok visszalépést okoz, állítsa be a globális alapértelmezett időkorlátot. Ha a feldolgozási idő hosszabb időt vesz igénybe, mint a megadott felső korlát, időtúllépési kivételt eredményezne. Ha semmi sincs konfigurálva, az időtúllépés végtelen lesz.| 

### <a name="example"></a>Példa
A következő konfiguráció például egy RegexMatchTimeoutException fog dobni, ha a feldolgozás 5 másodpercnél több időt vesz igénybe: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Kerülje a HTML. Raw használatát a borotva nézeteiben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Lépés | A ASP.NET weblapok (Razor) automatikus HTML-kódolást végeznek. A beágyazott Code Nuggets (@ Blocks) által kinyomtatott összes karakterlánc automatikusan HTML-kódolású. A `HtmlHelper.Raw` metódus meghívásakor azonban olyan korrektúrát ad vissza, amely nem HTML kódolású. Ha `Html.Raw()` a segítő módszert használja, az megkerüli a borotva által biztosított automatikus kódolási védelmet.|

### <a name="example"></a>Példa
A következő egy nem biztonságos példa: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Csak akkor használja, `Html.Raw()` Ha nem kell megjelenítenie a jelölést. Ez a metódus implicit módon nem hajtja végre a kimeneti kódolást. Használjon más ASP.NET-segítőket például: `@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Dinamikus lekérdezések használata a tárolt eljárásokban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Az SQL-injektálási támadás biztonsági réseket használ a bemeneti ellenőrzés során, hogy tetszőleges parancsokat futtasson az adatbázisban. Ez akkor fordulhat elő, ha az alkalmazás a bemenet használatával dinamikus SQL-utasításokat hoz létre az adatbázis eléréséhez. Akkor is előfordul, ha a kód olyan tárolt eljárásokat használ, amelyek nyers felhasználói bevitelt tartalmazó karakterláncokat adtak át. Az SQL-injektálási támadás használatával a támadó tetszőleges parancsokat futtathat az adatbázisban. Az összes SQL-utasítást (beleértve a tárolt eljárások SQL-utasításait is) paraméternek kell lennie. A paraméteres SQL-utasítások olyan karaktereket fogadnak el, amelyek speciális jelentéssel rendelkeznek az SQL-hez (például egy idézet), problémák nélkül, mert erősen vannak beírva. |

### <a name="example"></a>Példa
A következő példa a nem biztonságos dinamikus tárolt eljárást szemlélteti: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Példa
A következő azonos tárolt eljárás biztonságosan implementálva van: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Győződjön meg arról, hogy a modell ellenőrzése a webes API-módszerekkel történik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Modell érvényesítése a ASP.NET web API-ban](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépések** | Amikor egy ügyfél adatokat küld egy webes API-nak, a feldolgozás előtt kötelező ellenőrizni az adatokat. Olyan ASP.NET webes API-k esetében, amelyek bemenetként fogadják el a modelleket, adatjegyzeteket használhat a modelleken az érvényesítési szabályok beállításához a modell tulajdonságaiban.|

### <a name="example"></a>Példa
A következő kód ugyanezt mutatja be: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Példa
Az API-vezérlők műveleti metódusában a modell érvényességét explicit módon be kell jelölni az alábbiak szerint: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>A bemeneti ellenőrzés megvalósítása a webes API-metódusok által elfogadott összes karakterlánc típusú paraméternél

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, MVC 5, MVC 6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | A [modell adatai egy MVC-alkalmazásban](/previous-versions/dd410404(v=vs.90)), [a ASP.net MVC-alkalmazások irányadó alapelvei](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Lépések** | Az olyan metódusok esetében, amelyek csak egyszerű adattípust fogadnak el, és nem a modell argumentumként, a normál kifejezéssel végzett bemeneti ellenőrzést kell elvégezni. Itt a regex. IsMatch érvényes regex-mintával kell használni. Ha a bemenet nem felel meg a megadott reguláris kifejezésnek, a vezérlő nem folytathatja a műveletet, és az érvényesítési hibákra vonatkozó megfelelő figyelmeztetést kell megjeleníteni.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Győződjön meg arról, hogy a típus-biztonságos paramétereket használják a webes API-ban az adatok eléréséhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Ha a parameters (paraméterek) gyűjteményt használja, az SQL a bemenetet literál értékként adja meg a végrehajtható kód helyett. A paraméterek gyűjteménye a bemeneti adatok típusának és hosszának korlátozására használható. A tartományon kívüli értékek kivételt váltanak ki. Ha nem használja a Type-Safe SQL-paramétereket, a támadók képesek lehetnek a szűretlen bemenetbe ágyazott injekciós támadások végrehajtására.</p><p>SQL-lekérdezések létrehozásakor használjon biztonságos paramétereket, hogy elkerülje a nem szűrt bevitelsel esetlegesen fellépő lehetséges SQL-injektálási támadásokat. A biztonságos paramétereket tárolt eljárásokkal és dinamikus SQL-utasításokkal is használhatja. A paramétereket literál értékként kezeli az adatbázis, nem pedig végrehajtható kódként. A paramétereket a típus és a hossz is ellenőrzi.</p>|

### <a name="example"></a>Példa
A következő kód azt mutatja be, hogyan használhatók a biztonságos paraméterek a SqlParameterCollection egy tárolt eljárás meghívásakor. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
A fenti példában a bemeneti érték nem lehet hosszabb 11 karakternél. Ha az érték nem felel meg a paraméter által meghatározott típusnak vagy hossznak, a SqlParameter osztály kivételt jelez. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Paraméteres SQL-lekérdezések használata Cosmos DBhoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az SQL-paraméterezés bejelentése Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Lépések** | Habár a Azure Cosmos DB csak a csak olvasási lekérdezéseket támogatja, az SQL-injektálás továbbra is lehetséges, ha a lekérdezések a felhasználói adatbevitelsel való összefűzéssel jönnek létre. Előfordulhat, hogy a felhasználó olyan adatokhoz fér hozzá, amelyek nem érhetők el ugyanabban a gyűjteményben a rosszindulatú SQL-lekérdezések készítésével. Paraméteres SQL-lekérdezések használata, ha a lekérdezések felhasználói bevitel alapján vannak kiépítve. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>WCF-bemenet ellenőrzése séma kötésével

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff647820(v=pandp.10)) |
| **Lépések** | <p>Az érvényesítés hiánya különböző típusú injekciós támadásokhoz vezet.</p><p>Az üzenet-ellenőrzés a WCF-alkalmazás védelmének egyik védelmi vonalát jelöli. Ezzel a megközelítéssel érvényesíti az üzeneteket sémák használatával a WCF szolgáltatási műveleteinek rosszindulatú ügyfél általi támadás általi biztosításához. Ellenőrizze, hogy az ügyfél által fogadott összes üzenet kártékony szolgáltatás általi támadás ellen védett-e. Az üzenet-ellenőrzés lehetővé teszi az üzenetek érvényesítését, amikor a műveletek üzenet-vagy adategyezményeket használnak, ami nem végezhető el a paraméterek ellenőrzésekor. Az üzenet-ellenőrzés lehetővé teszi, hogy ellenőrzési logikát hozzon létre a sémákban, így nagyobb rugalmasságot biztosít, és csökkenti a fejlesztési időt. A sémák újra felhasználhatók a szervezeten belüli különböző alkalmazásokban, az adatábrázolásra vonatkozó szabványok létrehozásával. Emellett az üzenetek ellenőrzése lehetővé teszi a műveletek védelme, ha összetettebb adattípusokat használnak az üzleti logikát képviselő szerződésekkel együtt.</p><p>Az üzenet-ellenőrzés végrehajtásához először olyan sémát kell létrehoznia, amely a szolgáltatás műveleteit és a műveletek által felhasznált adattípusokat jelöli. Ezután létrehoz egy .NET-osztályt, amely egy egyéni ügyfél-üzenetblokk és egy egyéni kézbesítő-üzenet ellenőrét implementálja a szolgáltatásnak küldött/fogadott üzenetek ellenőrzéséhez. Ezt követően egyéni végponti viselkedést kell megvalósítani, hogy az üzenetek ellenőrzése az ügyfélen és a szolgáltatáson is engedélyezve legyen. Végül egy egyéni konfigurációs elemet is megvalósíthat az osztályban, amely lehetővé teszi, hogy elérhetővé tegye a kiterjesztett egyéni végpont viselkedését a szolgáltatás vagy az ügyfél konfigurációs fájljában. "</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF – bemeneti ellenőrzés paraméter-ellenőrök használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff647875(v=pandp.10)) |
| **Lépések** | <p>A bemenet és az adatok érvényesítése a WCF-alkalmazás védelmének egyik fontos védelmi vonalát jelenti. A WCF szolgáltatási műveletekben elérhető összes paramétert ellenőriznie kell a szolgáltatás rosszindulatú ügyfél általi támadása elleni védelem érdekében. Ezzel szemben az ügyfél által fogadott összes visszatérési értéket is ellenőriznie kell, hogy egy rosszindulatú szolgáltatás megóvja az ügyfelet a támadástól</p><p>A WCF különböző bővíthetőségi pontokat biztosít, amelyek lehetővé teszik a WCF futásidejű viselkedésének testreszabását egyéni bővítmények létrehozásával. Az üzenet-ellenőrök és a paraméter-ellenőrök két, az ügyfél és a szolgáltatás közötti adatátvitel nagyobb mértékű szabályozására szolgáló bővíthetőségi mechanizmus. A bemeneti ellenőrzéshez paraméter-ellenőröket kell használnia, és csak akkor használja az üzenőfalak használatát, ha meg kell vizsgálnia a szolgáltatáson belül és kívülről áramló teljes üzenetet.</p><p>A bemeneti ellenőrzés végrehajtásához hozzon létre egy .NET-osztályt, és hozzon létre egy egyéni paraméter-ellenőrzőt a szolgáltatás műveleteire vonatkozó paraméterek érvényesítéséhez. Ezután egy egyéni végponti viselkedést fog megvalósítani az érvényesítés engedélyezéséhez az ügyfélen és a szolgáltatáson is. Végül egy egyéni konfigurációs elemet fog megvalósítani az osztályban, amely lehetővé teszi, hogy elérhetővé tegye a kiterjesztett egyéni végpont viselkedését a szolgáltatás vagy az ügyfél konfigurációs fájljában.</p>|