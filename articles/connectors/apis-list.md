---
title: Az Azure Logic Apps összekötői
description: Az összekötők munkafolyamatok automatizálása az Azure Logic Apps, beleértve a beépített, felügyelt, a helyszíni integrációs fiók és vállalati összekötők
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: e008d9fd2734af6a355771c321ecaea9150bcc33
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722992"
---
# <a name="connectors-for-azure-logic-apps"></a>Az Azure Logic Apps összekötői

Összekötők gyors hozzáférést biztosít az Azure Logic Apps események, adatok és műveleteket más alkalmazások, szolgáltatások, rendszerek, protokollok és platformok között. Az összekötők a logic Apps használatával létrehozott és már rendelkezik az adatok a feladatok végrehajtásához a felhőbeli és helyszíni alkalmazások funkcióinak kiterjesztése.

Miközben a Logic Apps-ajánlatok [~ több mint 200 összekötőt](https://docs.microsoft.com/connectors), ez a cikk bemutatja a népszerű és gyakrabban használt összekötők alkalmazások ezrei, és több millió olyan végrehajtás által az adatok és információk feldolgozása sikeresen használt. Keresse meg a teljes listája megtalálható az összekötők és minden összekötőhöz kapcsolódó információk, például a triggereket, műveletek és -korlátok, tekintse át az összekötő referenciaoldalait alatt [összekötők áttekintése](https://docs.microsoft.com/connectors). Ezenfelül bővebben [triggereket és műveleteket](#triggers-actions).

> [!NOTE]
> Egy szolgáltatás vagy az API-t, amely nem rendelkezik az összekötő integrálása közvetlenül meghívja a szolgáltatást egy például a HTTP protokollon keresztül, vagy hozzon létre egy [egyéni összekötő](#custom).

Az összekötők beépített triggereket és műveleteket vagy felügyelt összekötőkként érhetők el:

* [**Built-ins**](#built-ins): Ezek a beépített műveletek és eseményindítók a "natív" Azure Logic Apps és logic apps, egyéni ütemezések szerint futtatni, kommunikálni más végpontok, fogadni és válaszolni a kérelmekre és az Azure functions, az Azure API Apps (webalkalmazások), a saját API-hívás létrehozásához a felügyelt és a közzétett Azure API Management és a beágyazott logic apps, kérelmek fogadására. Is használhatja a beépített műveletek, amelyek segítségével rendszerezheti és a logikai alkalmazás munkafolyamat vezérlőelemet, és is dolgozhat az adatokkal.

* **Felügyelt összekötők**: Üzembe helyezett, és a Microsoft felügyeli, ezeket az összekötőket biztosítanak, eseményindítók és műveletek eléréséhez a cloud services, a helyszíni rendszerek és egyaránt, beleértve az Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, a SharePoint és egyéb. Egyes összekötők kifejezetten-vállalatközi (B2B) kommunikáció forgatókönyvek támogatásához, és igényelnek- [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely kapcsolódik a logikai alkalmazást. Előtt bizonyos összekötők használatával, akkor előfordulhat, hogy először hozzon létre az Azure Logic Apps által felügyelt kapcsolatok. 

  Például ha használja a Microsoft BizTalk Server, a logic apps csatlakozhat és a BizTalk Server használatával kommunikálnak a [BizTalk Server a helyszíni összekötő](#on-premises-connectors). 
  Ezután bővítése vagy BizTalk-hez hasonló műveletek végrehajtása a logic apps használatával a [integrációsfiók-összekötők](#integration-account-connectors).

  Összekötők besorolt Standard vagy Enterprise. 
  [Vállalati összekötők](#enterprise-connectors) hozzáférést a vállalati rendszerek, például SAP, IBM MQ-val és IBM 3270 díjfizetés mellett. Annak megállapításához, hogy egy összekötő Standard vagy Enterprise, tekintse meg a technikai részleteket az egyes összekötők referenciájának oldalát a [összekötők áttekintése](https://docs.microsoft.com/connectors). 
  
  Is azonosíthatja összekötők használatával kategóriára, bár egyes összekötők több kategóriát is adatbázisközi. 
  SAP például egy vállalati összekötők és a egy a helyszíni összekötőhöz:

  |   |   |
  |---|---|
  | [**A felügyelt API-összekötők**](#managed-api-connectors) | Például az Azure Blob Storage, Office 365, Dynamics, a Power BI, onedrive vállalati verzió, Salesforce, a SharePoint online-hoz és sok más szolgáltatásokat használó logikai alkalmazásokat hozhat létre. |
  | [**A helyszíni összekötők**](#on-premises-connectors) | Miután telepítette, és állítsa be a [a helyszíni adatátjáró][gateway-doc], ezek az összekötők segítenek a logic apps hozzáférést a helyszíni rendszerek például az SQL Server, SharePoint Server, Oracle DB, fájlmegosztásokat és mások. |
  | [**Integrációsfiók-összekötők**](#integration-account-connectors) | Rendelkezésre álló létrehozásakor és a egy integrációs fiókban, ezek az összekötők átalakító kell fizetnie, és XML érvényességének ellenőrzése, kódolása és dekódolandó egybesimított fájlok, és vállalatok – feldolgozni (B2B) rendelkező üzenetek AS2, EDIFACT és X12 protokollok. |
  |||

> [!NOTE]
> A teljes listája megtalálható az összekötők és minden összekötőhöz kapcsolódó információk, például a műveletek és triggerek, amelyek határozza meg az OpenAPI (korábbi nevén Swagger) leírása, valamint bármely korlátok alapján a teljes listát megtalálja a [összekötők áttekintése ](/connectors/). Díjszabási információkért tekintse meg a [Logic Apps díjszabását](https://azure.microsoft.com/pricing/details/logic-apps/) és a [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Built-ins

A Logic Apps biztosít beépített eseményindítók és műveletek, ütemezésalapú munkafolyamatokat hozhat létre segít kommunikálni más alkalmazások és szolgáltatások, a vezérlő a logic apps, a munkafolyamat és kezelése adatok módosítására vagy a logic apps.

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][schedule-icon]<br/>**ütemezés**][recurrence-doc] | – A logikai alkalmazás egy megadott ütemezés szerint fut, az összetett ismétlődések, és az alapszintű a **ismétlődési** eseményindító. <p>– A logikai alkalmazás szüneteltetheti az adott időre az **késleltetés** művelet. <p>– A logikai alkalmazás szüneteltetheti a megadott dátumig és az időt a **késleltetés eddig** művelet. | [![API-ikon][http-icon]<br/>**HTTP**][http-doc] | HTTP-eseményindítók és a műveletek a HTTP, a HTTP + Swagger, és a HTTP + Webhook keresztül bármilyen végponttal kommunikálhat. | 
| [![API-ikon][http-request-icon]<br/>**kérése**][http-request-doc] | – Győződjön meg arról, a logikai alkalmazás használata hívható más alkalmazások vagy szolgáltatások, az erőforrás-események Event Grid eseményindító vagy válaszok az Azure Security Center riasztásainak eseményindítón a **kérelem** eseményindító. <p>-Küldése válaszok egy alkalmazáshoz vagy szolgáltatáshoz a **válasz** művelet. | [![API-ikon][batch-icon]<br/>**Batch**][batch-doc] | -A kötegekben-üzenetek feldolgozása a **üzenetek Batch** eseményindító. <p>-Hívás a logic apps, amelyeken a meglévő batch-eseményindítók az **kötegelendő üzenetek küldése** művelet. | 
| [![API-ikon][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | Hívja meg az Azure functions, a logic apps-ről futó egyéni kódrészletek (C# vagy node.js nyelven). | [![API-ikon][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Hívja meg a saját API-k közzététele és kezelése az Azure API Management által meghatározott eseményindítót és műveletet. | 
| [![API-ikon][azure-app-services-icon]<br/>**Azure App Servicesben**][azure-app-services-doc] | Hívja meg az Azure API Apps vagy az Azure App Service szolgáltatásban üzemeltetett webalkalmazások. Eseményindítók és műveletek határozzák meg ezeket az alkalmazásokat jelenik meg például az első osztályú eseményindítók és műveletek Swagger részét képezi. | [![API-ikon][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Egyéb, amelyek elindítják a kérelem-eseményindítóval rendelkező logikai alkalmazások meghívása. | 
||||| 

### <a name="control-workflow"></a>Vezérlési munkafolyamat

Az alábbiakban a beépített műveletek munkatársaként és a műveletek a logikai alkalmazás munkafolyamat szabályozása:

|   |   |   |   | 
|---|---|---|---| 
| [![Beépített ikon][condition-icon]<br/>**feltétel**][condition-doc] | Olyan feltétel értékelése és futtatási különböző műveleteket, hogy a feltétel értéke true vagy FALSE (hamis). | [![Beépített ikon][for-each-icon]</br>**minden**][for-each-doc] | Az azonos műveleteket hajthat végre egy tömb összes elemét. | 
| [![Beépített ikon][scope-icon]<br/>**hatókör**][scope-doc] | Csoporthoz való műveletek *hatókörök*, amely a saját állapotának lekérése, és a műveletek a hatókör futtatásának befejezése után. | [![Beépített ikon][switch-icon]</br>**kapcsoló**][switch-doc] | Csoporthoz való műveletek *esetek*, amely rendelt egyedi értékeket, kivéve az alapértelmezett esetben. Csak az adott eset, amelynek hozzárendelt értéke megegyezik az eredmény egy kifejezés, objektumot vagy token futtassa. Ha nincs egyezés, futtassa az alapértelmezett esetben. | 
| [![Beépített ikon][terminate-icon]<br/>**leállítása**][terminate-doc] | Állítsa le az aktívan futó logikaialkalmazás-munkafolyamat. | [![Beépített ikon][until-icon]<br/>**mindaddig, amíg**][until-doc] | Ismételje meg a műveleteket, amíg a megadott feltétel teljesül, vagy bizonyos állapota megváltozott. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Adatok módosítására vagy kezelésére

Az alábbiakban a beépített műveleteket a kimeneti adatok és a formátumok használata:  

|   |   | 
|---|---| 
| [![Beépített ikon][data-operations-icon]<br/>**Adatműveletek**][data-operations-doc] | Hajtsa végre az adatokkal műveleteket: <p>- **Compose**: Hozzon létre egy egyetlen kimeneti több bemenet a különféle fenyegetési típusokat. <br>- **CSV-táblázat létrehozása**: Hozzon létre egy vesszővel tagolt (CSV) tábla JSON-objektumok egy tömb. <br>- **HTML-táblázat létrehozása**: Hozzon létre egy HTML-táblázat egy tömb JSON-objektumokkal. <br>- **Tömb szűrése**: Hozzon létre egy tömb a megadott feltételeket teljesítő egy másik tömb elemeinek. <br>- **Csatlakozás**: Hozzon létre egy karakterláncot egy tömb összes eleme, és azok az elemek elválasztás a megadott elválasztó. <br>- **JSON elemzése**: A tulajdonságok és azok értékei JSON-tartalom felhasználóbarát-jogkivonat készítése esetleges felhasználhassa azokat a tulajdonságokat a munkafolyamatban. <br>- **Válassza ki**: Hozzon létre egy tömb JSON-objektumok elemet vagy egy másik tömbben lévő értékek átalakítása és azok az elemek megadott tulajdonságokkal. | 
| ![Built-in Icon][date-time-icon]<br/>**Dátum-idő** | Időbélyeggel rendelkező műveletek végrehajtása: <p>- **Hozzáadás időhöz**: Adja hozzá a megadott számú egységet egy időbélyegző. <br>- **Időzóna konvertálása**: A forrásidőzóna időbélyeg konvertálása időzónában. <br>- **Aktuális idő**: Az aktuális timestamp vissza karakterláncként. <br>- **Jövőbeli időpont beolvasása**: Vissza az aktuális timestamp plusz a megadott időegység. <br>- **Múltbeli időpont beolvasása**: A megadott időegység mínusz az aktuális időbélyeget adja vissza. <br>- **Kivonás időből**: Az időbélyeg mértékegységét számos kivonása. |
| [![Beépített ikon][variables-icon]<br/>**változók**][variables-doc] | Hajtsa végre a műveleti változókkal: <p>- **Hozzáfűzés tömbváltozóhoz**: INSERT egy értéket, az utolsó elem a tömbben egy változó tárolja. <br>- **Hozzáfűzni kívánt karakterlánc-változóhoz**: INSERT egy értéket egy változó tárolja karakterlánc utolsó karaktereként. <br>- **Változó értékének csökkentése**: Csökkentse egy változót egy állandó értékkel. <br>- **Változó értékének növelése**: Növelje egy változót egy állandó értékkel. <br>- **Változó inicializálása**: Hozzon létre egy változót, és az adattípus és a kezdeti érték deklarálja. <br>- **Változó beállítása**: Rendeljen egy másik értéket a meglévő változókat. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>A felügyelt API-összekötők

A további népszerű összekötők automatizálhatja a feladatokat, folyamatok és munkafolyamatok ezen szolgáltatások vagy a rendszerek a következők:

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Aszinkron üzenetekkel, munkamenetek és a leggyakrabban használt összekötőt a Logic Apps-előfizetések kezelése. | [![API-ikon][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | Csatlakozzon az SQL Server, a helyszínen vagy a felhőben, az Azure SQL Database, így-rekordok kezelése, a tárolt eljárások futtatása, vagy hajtsa végre a lekérdezéseket. | 
| [![API-ikon][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Csatlakozhat az Office 365 e-mail-fiókjába, így létrehozása és kezelése az e-mailek, feladatok, naptárbeli események és értekezletek, névjegyek, a kérelmektől és több. | [![API-ikon][azure-blob-storage-icon]<br/>**Azure Blob<br/>Storage**][azure-blob-storage-doc] | A tárfiók kapcsolódni, így hozhat létre és kezelheti a blob tartalma. | 
| [![API-ikon][sftp-icon]<br/>**SFTP**][sftp-doc] | Csatlakozhat SFTP-kiszolgálókat az internetről is elérheti, így működhet együtt a fájlokról és mappákról. | [![API-ikon][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Csatlakozzon a SharePoint online-hoz, fájlok, a mellékleteket, mappákat és további kezelését. | 
| [![API-ikon][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online-hoz**][dynamics-365-doc] | Csatlakozhat a Dynamics 365-fiók létrehozása és kezelése a rekordokat, cikkek és további. | [![API-ikon][ftp-icon]<br/>**FTP**][ftp-doc] | Csatlakozhat FTP-kiszolgálókat az internetről is elérheti, így működhet együtt a fájlokról és mappákról. | 
| [![API-ikon][salesforce-icon]<br/>**Salesforce-ban**][salesforce-doc] | Salesforce-fiókjához csatlakozhat, így hozhat létre és kezelheti például a rekordokat, feladatok, objektumok és egyéb elemek. | [![API-ikon][twitter-icon]<br/>**Twitter**][twitter-doc] | Twitter-fiókja kapcsolódni, így kezelheti a tweeteket, követők, az ütemterv és egyéb. A tweetek mentése SQL, az Excel vagy SharePoint. | 
| [![API-ikon][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Felhasználását, és tegye közzé az eseményeket egy Eseményközpontba. A logikai alkalmazás az Event hubs használatával például lekérheti kimenetét, és küldje el, amelyek kimenete egy valós idejű elemzési szolgáltatónak. | [![API-ikon][azure-event-grid-icon]<br/>**Azure Event**</br>**rács**][azure-event-grid-doc] | Azure-erőforrások vagy külső erőforrások változásakor egy Event Grid, például által közzétett események figyelése | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Helyszíni összekötők 

Íme néhány gyakran használt összekötőket biztosítanak az adatokhoz és erőforrásokhoz való hozzáférés a helyszíni rendszerekben. Mielőtt létrehozna egy kapcsolat a helyszíni rendszerre, előbb futtatnia kell [letöltése, telepítése és beállítása egy helyszíni adatátjárót][gateway-doc]. Ezt az átjárót egy biztonságos kommunikációs csatornát biztosít anélkül, hogy a szükséges hálózati infrastruktúra beállításához kellene. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API-ikon][biztalk-server-icon]<br/>**BizTalk**</br> **Kiszolgáló** | [![API-ikon][file-system-icon]<br/>**fájl</br> rendszer**][file-system-doc] | [![API-ikon][ibm-db2-icon]<br/>**IBM DB2-höz**][ibm-db2-doc] | [![API-ikon][ibm-informix-icon]<br/>**IBM** </br> **Informix**][ibm-informix-doc] | ![API-ikon][mysql-icon]<br/>**MySQL** | 
| [![API-ikon][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![API-ikon][postgre-sql-icon]<br/>**PostgreSQL** | [![API-ikon][sharepoint-server-icon]<br/>**SharePoint</br> kiszolgáló**][sharepoint-server-doc] | [![API-ikon][sql-server-icon]<br/>**SQL</br> kiszolgáló**][sql-server-doc] | ![API-ikon][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői 

Az alábbiakban a vállalatközi (B2B) megoldások fejlesztése a logikai alkalmazások létrehozásakor és kell fizetnie az összekötőket egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), amely keresztül az Enterprise Integration Pack (EIP) az Azure-ban érhető el. Ezt a fiókot hozhat létre és tárolja a B2B összetevők, például a kereskedelmi partnerek, szerződések, térképek, sémák, tanúsítványok és így tovább. Ezek az összetevők használatához az integrációs fiók a logic apps társítani. Ha jelenleg használja a BizTalk Server, ezeket az összekötőket tűnhet ismerős már.

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][as2-icon]<br/>**AS2</br> dekódolás**][as2-doc] | [![API-ikon][as2-icon]<br/>**AS2</br> kódolás**][as2-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> dekódolás**][edifact-decode-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> kódolás**][edifact-encode-doc] | 
| [![API-ikon][flat-file-decode-icon]<br/>**Egybesimított fájl</br> dekódolás**][flat-file-decode-doc] | [![API-ikon][flat-file-encode-icon]<br/>**Egybesimított fájl</br> kódolás**][flat-file-encode-doc] | [![API-ikon][integration-account-icon]<br/>**integrációs<br/>fiók**][integration-account-doc] | [![API-ikon][liquid-icon]<br/>**Liquid**</br>**átalakítások**][json-liquid-transform-doc] | 
| [![API-ikon][x12-icon]<br/>**X12</br> dekódolás**][x12-decode-doc] | [![API-ikon][x12-icon]<br/>**X12</br> kódolás**][x12-encode-doc] | [![API icon][xml-transform-icon]<br/>**XML**</br>**transforms**][xml-transform-doc] | [![API-ikon][xml-validate-icon]<br/>**XML <br/>érvényesítése**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Vállalati összekötők

A logic apps vállalati rendszerek, például az SAP- és IBM MQ-val érhető el:

|   |   |   | 
|---|---|---| 
| [![API-ikon][ibm-3270-icon]<br/>**IBM 3270**][ibm-3270-doc] | [![API-ikon][ibm-mq-icon]<br/>**IBM MQ-val**][ibm-mq-doc] | [![API-ikon][sap-icon]<br/>**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Eseményindítók és műveletek - további információ

Összekötők biztosíthat *eseményindítók*, *műveletek*, vagy mindkettőt. A *eseményindító* első lépése az összes logikai alkalmazás, általában adja meg az eseményt, amely az eseményindító aktiválódik, és a logikai alkalmazás futtatásának megkezdése. Az FTP-összekötő például egy eseményindítót, amely elindítja a logikai alkalmazás "Amikor egy fájl hozzáadásakor és módosításakor" rendelkezik. Egyes eseményindítók rendszeresen ellenőrizze a megadott eseményt, és adatokat, és majd értesíti, ha azt észleli, hogy a megadott esemény vagy az adatok. Más eseményindítók várja, de instantly amikor egy adott esemény történik, vagy amikor új adatok érhetők el. Eseményindítók is adják át a szükséges adatokat a logikai alkalmazáshoz. A logikai alkalmazás olvashat és az adatokat a munkafolyamaton keresztül.
Például a Twitter-összekötő rendelkezik-e egy eseményindítót a logikai alkalmazás munkafolyamatba "Egy új tweet közzétételekor", amelyet átad a tweet tartalmát. 

Miután egy eseményindítót, az Azure Logic Apps létrehozza a logikai alkalmazás egy példányát, és elindul a *műveletek* a logikai alkalmazás munkafolyamat. A műveletek olyan lépéseket hajtsa végre az eseményindítót, és a logikai alkalmazás munkafolyamat feladatok végrehajtására. Például hozzon létre egy logikai alkalmazást, amely a vásárlói adatokat olvas be egy SQL-adatbázis és feldolgozni az adatokat a későbbi műveletekben. 

Az alábbiakban az általános típusú eseményindítókat, amelyek az Azure Logic Apps biztosítja:

* *Ismétlődési trigger*: Ez az eseményindító egy megadott ütemezés szerint fut, és szorosan kapcsolódó egy adott szolgáltatás vagy a rendszer nem.

* *Lekérdezési eseményindító*: Ez az eseményindító rendszeresen lekérdezi, egy adott szolgáltatás vagy a rendszer ellenőrzi az új adatok, és hogy egy adott esemény történt, a megadott ütemezés alapján. Ha új adatok érhetők el, vagy az adott esemény történt, az eseményindító hoz létre, és a logikai alkalmazást, amely most már használhatja az adatok bemeneti átadott egy új példányát futtatja.

* *Leküldéses eseményindítók*: Ez az eseményindító vár, és figyeli az új adatok, vagy adott esemény történik. Amikor új adatok érhetők el vagy az esemény történik, az eseményindító hoz létre, és a logikai alkalmazást, amely most már használhatja az adatok bemeneti átadott új példányát futtatja.

<a name="custom"></a>

## <a name="connector-configuration"></a>Összekötő-konfiguráció

Egyes összekötők eseményindítókat és műveleteket biztosít a saját tulajdonságok konfigurálását. Számos összekötő is megkövetelheti, hogy először hoz létre egy *kapcsolat* a célszolgáltatás vagy a rendszer, és adja meg hitelesítő adatok vagy egyéb konfigurációs adatait, mielőtt használhatná egy eseményindítót vagy műveletet a logikai alkalmazásban. Például engedélyeznie kell a Twitter-fiókja adatainak eléréséhez, vagy az Ön nevében tegyen egy kapcsolatot. 

Az összekötők OAuth protokollt használó-kapcsolat létrehozásával azt jelenti, hogy bejelentkezik a szolgáltatásba, például az Office 365-höz, a Salesforce vagy a github webhelyen, ahol a hozzáférési jogkivonat titkosítása és biztonságos módon tárolja az Azure titkoskód-tárolót. Más összekötők, például az FTP- és SQL-kapcsolat konfigurációs részleteket, például a kiszolgáló címe, felhasználónév és jelszó szükséges. E kapcsolat konfigurációs adatait is titkosítást kapnak, és biztonságosan tárolja. 

Kapcsolatok hozzáférhet a célszolgáltatás vagy a rendszer mindaddig, amíg a szolgáltatás vagy a rendszer lehetővé teszi. Az Azure Active Directory (AD) OAuth kapcsolatok, például az Office 365 és Dynamics, használó szolgáltatások Azure Logic Apps frissíti a hozzáférési jogkivonatok határozatlan időre. Előfordulhat, hogy más szolgáltatások korlátait mennyi ideig Azure Logic Apps használható jogkivonat frissítése nélkül. Bizonyos műveleteket általában érvényteleníteni az összes hozzáférési jogkivonatok, például a jelszó módosítását.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Egyéni API-k és összekötők

Hívja az API-k, amelyek egyéni kóddal, vagy nem érhetők el beépített összekötőkként, kiterjesztheti a Logic Apps-platformot [egyéni API-alkalmazások létrehozásának](../logic-apps/logic-apps-create-api-app.md). Emellett [egyéni összekötők létrehozása](../logic-apps/custom-connector-overview.md) a *bármely* REST vagy a SOAP-alapú API-k, amelyek API-k számára elérhetővé minden olyan logikai alkalmazást az Azure-előfizetésében.
Ahhoz, hogy egyéni API-alkalmazások és összekötők bárki használhatja az Azure nyilvános, is [küldje el a Microsofthoz tanúsítás céljából](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Küldje el, vagy szavazzon ötleteit az Azure Logic Apps és összekötők, látogasson el a [Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Keresse meg a [összekötők teljes listája](https://docs.microsoft.com/connectors)
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [A logic apps egyéni összekötők létrehozása](https://docs.microsoft.com/connectors/custom-connectors/)
* [Egyéni API-k létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Kezelését, közzétételéhez az API-k Azure API Management szolgáltatáspéldány létrehozása"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Logikai appok integrálása App Service API Apps-alkalmazásokkal"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Üzenetek feldolgozásával, csoportok, vagy kötegek"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Olyan feltétel értékelése és futtatási különböző műveleteket, hogy a feltétel értéke true vagy FALSE (hamis)"
[delay-doc]: ./connectors-native-delay.md "Késleltetett műveletek végrehajtása"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Az azonos műveleteket hajthat végre egy tömb összes elemét"
[http-doc]: ./connectors-native-http.md "HTTP-hívások indítása HTTP-összekötővel"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[http-response-doc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[http-swagger-doc]: ./connectors-native-http-swagger.md "HTTP-hívások indítása HTTP + Swagger összekötővel"
[http-webook-doc]: ./connectors-native-webhook.md "HTTP-webhook-műveletek és eseményindítók hozzáadása a logic apps"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[query-doc]: ./connectors-native-query.md "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[recurrence-doc]:  ./connectors-native-recurrence.md "Ismétlődő műveletek kiváltása logikai alkalmazásokhoz"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Műveletek után a műveletek csoport futása befejeződik, a saját állapotának lekérése csoportokba rendezése" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Rendezheti a műveletek az esetekről, melyek rendelt egyedi értékeket. Csak az az eset, amelynek értéke megegyezik az eredmény egy kifejezés, objektumot vagy token futtassa. Ha nincs egyezés, futtassa az alapértelmezett esetben"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Állítsa le a Mégse gombra a logikai alkalmazás egy aktívan futó munkafolyamathoz"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ismételje meg a műveleteket, amíg a megadott feltétel teljesül, vagy bizonyos állapota megváltozott"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Végezhetnek adatműveleteket, például a tömb szűrése vagy CSV és HTML-táblázat létrehozása"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Műveletek elvégzését a változókat, például az inicializálás, set, növekmény, csökkentési, és a hozzáfűzni kívánt karakterlánc- vagy tömb változó"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Azure-erőforrások vagy külső erőforrások változásakor egy Event Grid, például által közzétett események figyelése"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Csatlakozhat az Azure Event Hubs szolgáltatáshoz. Eseményeket fogadhat és küldhet a logikai alkalmazások és az Event Hubs között"
[box-doc]: ./connectors-create-api-box.md "Csatlakozhat a Boxhoz. Fájlokat tölthet fel, kérhet le, törölhet, listázhat, és egyéb műveleteket is végrehajthat"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Csatlakozhat a Dropboxhoz. Fájlokat tölthet fel, kérhet le, törölhet, listázhat, és egyéb műveleteket is végrehajthat"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Csatlakozhat a Dynamics CRM Online szolgáltatáshoz, és használhatja a CRM Online adatait"
[facebook-doc]: ./connectors-create-api-facebook.md "Csatlakozhat a Facebookhoz. Bejegyzéseket tehet közzé az idővonalon, laptartalmakat kérhet le, és egyéb műveleteket is végrehajthat"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftp-doc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[github-doc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "A Google Naptárhoz csatlakozhat, és felügyelheti a naptárat."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Csatlakozhat a Google Drive-hoz, és használhatja annak adatait"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Google táblázatok kapcsolódni, így módosíthatja a táblázatait"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Csatlakozhat a Google Teendőkhöz, ahol különböző teendőit kezelheti"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Csatlakozhat az IBM Nagyszámítógépek 3270 alkalmazások"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Csatlakozhat az IBM DB2-höz a felhőben vagy a helyszínen. Sorokat frissíthet, táblákat kérhet le, és egyéb műveleteket is végrehajthat"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Csatlakozhat az Informixhez a felhőben vagy a helyszínen. Sorokat olvashat el, táblákat listázhat, és egyéb műveleteket is végrehajthat"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Csatlakozás helyszíni IBM MQ-val vagy az üzenetek küldése és fogadása az Azure-ban"
[instagram-doc]: ./connectors-create-api-instagram.md "Csatlakozhat az Instagramhoz. Eseményeket indíthat vagy reagálhat rájuk"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Csatlakozhat MailChimp-fiókjához. Kezelheti és automatizálhatja e-mailjeit"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Csatlakozhat a Microsoft Translatorhoz. Szövegeket fordíthat, nyelveket ismerhet fel, és egyéb műveleteket is végrehajthat" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Csatlakozhat Office 365-fiókjához. E-maileket küldhet és fogadhat, kezelheti a naptárát és névjegyeit, és egyéb műveleteket is végrehajthat"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Videoadatokat, videólistákat és csatornákat kérhet le, valamint Office 365-videók lejátszási URL-címeit"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Csatlakozhat személyes Microsoft OneDrive-fiókjához. Fájlokat tölthet fel, törölhet, listázhat, és egyéb műveleteket is elvégezhet"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Csatlakozhat vállalati Microsoft OneDrive-fiókjához. Fájlokat tölthet fel, törölhet, listázhat, és egyéb műveleteket is elvégezhet"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozhat egy Oracle-adatbázishoz, ahol többek között sorokat adhat hozzá, szúrhat be és törölhet"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Csatlakozhat Outlook-postaládájához. Kezelheti az e-mailjeit, naptárait, névjegyeit és egyéb tartalmait"
[project-online-doc]: ./connectors-create-api-projectonline.md "Csatlakozhat a Microsoft Project Online-hoz. Kezelheti a projektjeit, feladatait, erőforrásait és egyéb tartalmait"
[rss-doc]: ./connectors-create-api-rss.md "Hírcsatorna-elemek közzététele és lekérése, illetve műveletek kiváltása, ha egy új elemet tesznek közzé egy RSS-hírcsatornán."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Csatlakozhat Salesforce-fiókjához. Kezelheti fiókjait, az érdeklődőket, üzleti lehetőségeit és egyéb elemeket"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Csatlakozhat a SendGridhez. E-mail küldése és címzettlisták kezelésére"
[sftp-doc]: ./connectors-create-api-sftp.md "Csatlakozhat SFTP-fiókjához. Fájlokat tölthet fel, kérhet le, törölhet, és egyéb műveleteket is végrehajthat"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Csatlakozhat a helyszíni SharePoint-kiszolgálóhoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Csatlakozhat a SharePoint Online-hoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[slack-doc]: ./connectors-create-api-slack.md "Csatlakozhat a Slackhez, és üzeneteket tehet közzé a Slack-csatornákon"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP-kiszolgáló csatlakozik, és mellékleteket tartalmazó e-mailben"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Csatlakozás az Azure SQL Database vagy SQL Server. SQL-adatbázistáblában szereplő bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet."
[trello-doc]: ./connectors-create-api-trello.md "Csatlakozhat a Trellóhoz. Kezelheti a projektjeit és bármit és bárkivel megszervezhet"
[twilio-doc]: ./connectors-create-api-twilio.md "Csatlakozhat a Twilióhoz. Üzeneteket küldhet, lekérheti az elérhető számokat, kezelheti a bejövő hívások telefonszámait, és egyéb műveleteket is végrehajthat"
[twitter-doc]: ./connectors-create-api-twitter.md "Csatlakozhat a Twitterhez. Idővonal-tartalmakat fogadhat, tweeteket tehet közzé, és egyéb műveleteket is végrehajthat"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Csatlakozhat a Wunderlisthez. Kezelheti a feladatait és teendőlistáit, szinkronizálhatja különböző tartalmait, és egyéb műveleteket is végrehajthat"
[yammer-doc]: ./connectors-create-api-yammer.md "Csatlakozhat a Yammerhez. Üzenetek tehet közzé, új üzeneteket fogadhat, és egyéb műveleteket is végrehajthat"
[youtube-doc]: ./connectors-create-api-youtube.md "Csatlakozhat a YouTube-hoz. Kezelheti a videóit és csatornáit"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Megismerheti a vállalati integrációs AS2-t."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Megismerheti a vállalati integrációs EDIFACT-dekódolást."
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Megismerheti a vállalati integrációs EDIFACT-kódolást."
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Megismerheti a vállalati integrációs egybesimított fájlt."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Megismerheti a vállalati integrációs egybesimított fájlt."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Sémákat, térképeket, partnereket és egyebeket kereshet ki az integrációs fiókjában"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Megismerheti a Liquiddel végzett JSON-átalakításokat"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Megismerheti a vállalati integrációs X12-t."
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Megismerheti a vállalati integrációs X12-dekódolást."
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Megismerheti a vállalati integrációs X12-kódolást."
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Megismerheti a vállalati integrációs átalakításokat."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Megismerheti a vállalati integrációs XML-hitelesítést."

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
