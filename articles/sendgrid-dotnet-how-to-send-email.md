---
title: A SendGrid e-mail szolgáltatás (.NET) használata | Microsoft Docs
description: Megtudhatja, hogyan küldhet e-mailt az Azure SendGrid e-mail szolgáltatásával. A C# nyelven írt példák és a .NET API használata.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.custom: devx-track-dotnet
ms.openlocfilehash: ae816d2be592ab774500d1cfe8f2f6a7b7905b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98196554"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>E-mail küldése a SendGrid és az Azure használatával
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure SendGrid e-mail szolgáltatásával. A minták C nyelven íródtak \# , és a .NET Standard 1,3-es verzióit támogatják. A tárgyalt forgatókönyvek közé tartozik például az e-mailek létrehozása, az e-mail küldése, a mellékletek hozzáadása, valamint a levelezés és a nyomkövetés különböző beállításainak engedélyezése. További információt a SendGrid és az e-mailek küldéséről a [következő lépések][Next steps] című szakaszban talál.

## <a name="what-is-the-sendgrid-email-service"></a>Mi a SendGrid E-mail szolgáltatás?
A SendGrid egy [felhőalapú e-mail-szolgáltatás] , amely megbízható [tranzakciós e-mail-kézbesítést], skálázhatóságot és valós idejű elemzéseket biztosít, valamint rugalmas API-kat, amelyek egyszerűvé teszik az egyéni integrációt. Az általános SendGrid használati esetei a következők:

* Visszaigazolások vagy vásárlási megerősítések automatikus küldése az ügyfeleknek.
* Terjesztési listánk felügyelete az ügyfelek havi szórólapok és promóciók küldéséhez.
* Valós idejű metrikák gyűjtése olyan dolgokhoz, mint a blokkolt e-mailek és az ügyfelek engagement.
* Ügyfelekkel kapcsolatos kérdések továbbítása.
* A beérkező e-mailek feldolgozása.

További információért látogasson el [https://sendgrid.com](https://sendgrid.com) vagy SendGrid [C# Library][sendgrid-csharp] GitHub-tárházát.

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Hivatkozás a SendGrid .NET-osztály könyvtárára
A [SendGrid NuGet csomag](https://www.nuget.org/packages/Sendgrid) a legegyszerűbb módja a SendGrid API beszerzésének, valamint az alkalmazás minden függőséggel való konfigurálásának. A NuGet egy Visual Studio-bővítmény, amely a Microsoft Visual Studio 2015-es vagy újabb verziójában található, és megkönnyíti a könyvtárak és eszközök telepítését és frissítését.

> [!NOTE]
> A NuGet telepítéséhez, ha a Visual Studio 2015-nál korábbi verzióját futtatja, látogasson el [https://www.nuget.org](https://www.nuget.org) a következőre, és kattintson a **NuGet telepítése** gombra.
>
>

A SendGrid NuGet-csomag az alkalmazásban való telepítéséhez tegye a következőket:

1. Kattintson az **új projekt** elemre, és válasszon ki egy **sablont**.

   ![Új projekt létrehozása][create-new-project]
2. A **megoldáskezelő** kattintson a jobb gombbal a **hivatkozások** elemre, majd kattintson a **NuGet-csomagok kezelése** elemre.

   ![SendGrid NuGet csomag][SendGrid-NuGet-package]
3. Keresse meg a **SendGrid** , és válassza ki a **SendGrid** elemet az eredmények listájában.
4. Válassza ki a Nuget csomag legújabb stabil verzióját a legördülő menüből, hogy együttműködjön a jelen cikkben bemutatott objektummodell és API-k használatával.

   ![SendGrid-csomag][sendgrid-package]
5. A telepítés befejezéséhez **kattintson a telepítés gombra** , majd a párbeszédpanel bezárásához.

A SendGrid .NET-osztály könyvtára **SendGrid** néven ismert. A következő névtereket tartalmazza:

* **SendGrid** a SendGrid API-val való kommunikációhoz.
* **SendGrid. helpers. post** for Helper metódusok az e-mailek küldését megadó SendGridMessage-objektumok egyszerű létrehozásához.

Adja hozzá a következő kód névtér-deklarációkat a C#-fájlok tetejéhez, amelyeken programozott módon szeretné elérni a SendGrid e-mail szolgáltatást.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>Útmutató: E-mail létrehozása
E-mail-üzenet létrehozásához használja a **SendGridMessage** objektumot. Az üzenet-objektum létrehozása után megadhatja a tulajdonságokat és a metódusokat, beleértve az e-mail feladóját, az e-mail címzettjét, valamint az e-mail tárgyát és törzsét.

Az alábbi példa bemutatja, hogyan hozhat létre egy teljesen feltöltött e-mail-objektumot:

```csharp
var msg = new SendGridMessage();

msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

var recipients = new List<EmailAddress>
{
    new EmailAddress("jeff@example.com", "Jeff Smith"),
    new EmailAddress("anna@example.com", "Anna Lidman"),
    new EmailAddress("peter@example.com", "Peter Saddow")
};
msg.AddTos(recipients);

msg.SetSubject("Testing the SendGrid C# Library");

msg.AddContent(MimeType.Text, "Hello World plain text!");
msg.AddContent(MimeType.Html, "<p>Hello World!</p>");
```

További információ a **SendGrid** típus által támogatott összes tulajdonságról és metódusról: [SendGrid-csharp][sendgrid-csharp] a githubon.

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése
E-mail-üzenet létrehozása után elküldheti azt a SendGrid API-jával. Azt is megteheti, hogy használhatja [. A NET beépített könyvtára][NET-library].

Az e-mailek küldéséhez meg kell adnia a SendGrid API-kulcsát. Ha további információkra van szüksége az API-kulcsok konfigurálásával kapcsolatban, tekintse meg a SendGrid API Keys [dokumentációját][documentation].

Ezeket a hitelesítő adatokat a Azure Portalon keresztül is tárolhatja, ha az Alkalmazásbeállítások elemre kattint, és hozzáadja a kulcs/érték párokat az alkalmazás beállításai területen.

![Azure-alkalmazás beállításai][azure_app_settings]

Ezután a következőképpen érheti el őket:

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

Az alábbi példák bemutatják, hogyan küldhet e-mail-üzenetet a SendGrid web API-val egy konzolszoftver használatával.

```csharp
using System;
using System.Threading.Tasks;
using SendGrid;
using SendGrid.Helpers.Mail;

namespace Example
{
    internal class Example
    {
        private static void Main()
        {
            Execute().Wait();
        }

        static async Task Execute()
        {
            var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
            var client = new SendGridClient(apiKey);
            var msg = new SendGridMessage()
            {
                From = new EmailAddress("test@example.com", "DX Team"),
                Subject = "Hello World from the SendGrid CSharp SDK!",
                PlainTextContent = "Hello, Email!",
                HtmlContent = "<strong>Hello, Email!</strong>"
            };
            msg.AddTo(new EmailAddress("test@example.com", "Test User"));
            var response = await client.SendEmailAsync(msg);
        }
    }
}
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Útmutató: e-mailek küldése az ASP .NET Core API-ból a MailHelper osztály használatával

Az alábbi példa arra szolgál, hogy egyetlen e-mailt küldjön több személynek az ASP .NET Core API-ból a `MailHelper` névtér osztály használatával `SendGrid.Helpers.Mail` . Ebben a példában az ASP .NET Core 1,0-et használjuk. 

Ebben a példában az API-kulcsot a fájl tárolja, `appsettings.json` amely felülbírálható a Azure Portal a fenti példákban látható módon.

A fájl tartalmának a következőhöz `appsettings.json` hasonlóan kell kinéznie:

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  },
  "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

Először hozzá kell adnia az alábbi kódot a `Startup.cs` .net Core API projekt fájljában. Erre azért van szükség, hogy az API- `SENDGRID_API_KEY` `appsettings.json` vezérlő függőségi injektálásával hozzáférhessen a fájlhoz. Az `IConfiguration` illesztőfelület a vezérlő konstruktorában lehet befecskendezni, miután hozzáadta azt az `ConfigureServices` alábbi metódushoz. A fájl tartalma a `Startup.cs` következőhöz hasonlóan néz ki a szükséges kód hozzáadása után:

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

A vezérlőben az interfész beinjektálása után a `IConfiguration` `CreateSingleEmailToMultipleRecipients` osztály metódusával `MailHelper` egyetlen e-mailt küldhet több címzettnek. A metódus egy nevű további logikai paramétert fogad el `showAllRecipients` . Ezzel a paraméterrel szabályozhatja, hogy az e-mail címzettjei láthatják-e az e-mailek fejlécének címzett szakaszát. A vezérlőhöz tartozó mintakód az alábbihoz hasonló lehet: 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using SendGrid;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Configuration;

namespace SendgridMailApp.Controllers
{
    [Route("api/[controller]")]
    public class NotificationController : Controller
    {
       private readonly IConfiguration _configuration;

       public NotificationController(IConfiguration configuration)
       {
         _configuration = configuration;
       }

       [Route("SendNotification")]
       public async Task PostMessage()
       {
          var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
          var client = new SendGridClient(apiKey);
          var from = new EmailAddress("test1@example.com", "Example User 1");
          List<EmailAddress> tos = new List<EmailAddress>
          {
              new EmailAddress("test2@example.com", "Example User 2"),
              new EmailAddress("test3@example.com", "Example User 3"),
              new EmailAddress("test4@example.com","Example User 4")
          };

          var subject = "Hello world email from Sendgrid ";
          var htmlContent = "<strong>Hello world with HTML content</strong>";
          var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
          var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
          var response = await client.SendEmailAsync(msg);
      }
   }
}
```

## <a name="how-to-add-an-attachment"></a>Útmutató: melléklet hozzáadása
A mellékletek hozzáadhatók egy üzenethez a **AddAttachment** metódus meghívásával és a csatolni kívánt fájl nevének és Base64-kódolásának minimális megadásával. Több mellékletet is megadhat, ha ezt a metódust egyszer hívja meg minden csatolni kívánt fájlhoz vagy a **AddAttachments** metódus használatával. Az alábbi példa egy melléklet hozzáadását mutatja be egy üzenethez:

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Útmutató: a levelezési beállítások használata a láblécek, a követés és az elemzés engedélyezéséhez
A SendGrid további e-mail-funkciókat biztosít a levelezési beállítások és a követési beállítások használatával. Ezek a beállítások hozzáadhatók egy e-mail-üzenethez, amely lehetővé teszi bizonyos funkciók, például a követés, a Google Analytics, az előfizetés nyomon követése és így tovább. Az alkalmazások teljes listáját a [Beállítások dokumentációjában][settings-documentation]találja.

Az alkalmazások a **SendGridMessage** osztály részeként megvalósított metódusok használatával **SendGrid** e-mail-üzenetekre. Az alábbi példák bemutatják a láblécet, majd a szűrők követése elemre kattintanak:

Az alábbi példák bemutatják a láblécet, majd a szűrők követése elemre kattintanak:

### <a name="footer-settings"></a>Lábléc beállításai

```csharp
    msg.SetFooterSetting(
        true,
        "Some Footer HTML",
        "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>Kattintson a nyomon követés gombra

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: további SendGrid-szolgáltatások használata
A SendGrid számos API-t és webhookot kínál, amelyek segítségével további funkciókat alkalmazhat az Azure-alkalmazásban. További részletekért tekintse meg a [SENDGRID API-referenciát][SendGrid API documentation].

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, kövesse az alábbi hivatkozásokat további információért.

* SendGrid C \# programkönyvtár: [SendGrid-csharp][sendgrid-csharp]
* SendGrid API-dokumentáció: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/api-reference/
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[felhőalapú e-mail szolgáltatás]: https://sendgrid.com/solutions
[tranzakciós e-mail kézbesítés]: https://sendgrid.com/use-cases/transactional-email

