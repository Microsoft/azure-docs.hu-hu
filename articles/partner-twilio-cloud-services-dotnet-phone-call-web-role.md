---
title: Telefonhívás kezdeményezése a Twilio (.NET) szolgáltatásból | Microsoft Docs
description: Megtudhatja, hogyan tehet telefonhívást a Twilio API szolgáltatással az Azure-ban. A .NET-ben írt mintakód-minták.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95521121"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Telefonhívás kezdeményezése a Twilio használatával webes szerepkörben az Azure-ban
Ez az útmutató bemutatja, hogyan használható a Twilio az Azure-ban üzemeltetett weblapokról. Az eredményül kapott alkalmazás felszólítja a felhasználót, hogy hívja meg a megadott számot és üzenetet, ahogy az alábbi képernyőképen is látható.

![Azure Call űrlap a Twilio és a ASP.NET használatával][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Előfeltételek
A jelen témakörben található kód használatához a következőket kell tennie:

1. Twilio-fiók és hitelesítési jogkivonat beszerzése a [Twilio-konzolról][twilio_console]. A Twilio megkezdéséhez regisztráljon a következő címen: [https://www.twilio.com/try-twilio][try_twilio] . A díjszabást a következő címen értékelheti: [https://www.twilio.com/pricing][twilio_pricing] . További információ a Twilio által biztosított API-ról: [https://www.twilio.com/voice/api][twilio_api] .
2. Adja hozzá a *Twilio .net-függvénytárat* a webes szerepkörhöz. Lásd: **a Twilio-kódtárak hozzáadása a webes szerepkör-projekthez**, a témakör későbbi részében.

Ismernie kell egy alapszintű [webes szerepkör][azure_webroles_get_started]létrehozását az Azure-ban.

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Útmutató: webes űrlap létrehozása hívás kezdeményezéséhez
<a id="use_nuget"></a>A Twilio-kódtárak hozzáadása a webes szerepkör-projekthez:

1. Nyissa meg a megoldást a Visual Studióban.
2. Kattintson a jobb gombbal a **referenciák** elemre.
3. Kattintson a **NuGet-csomagok kezelése** elemre.
4. Kattintson az **online** lehetőségre.
5. A Keresés az interneten mezőbe írja be a következőt: *twilio*.
6. Kattintson a **telepítés** gombra a Twilio-csomagban.

A következő kód bemutatja, hogyan hozhat létre webes űrlapot a felhasználói adathívások lekéréséhez. Ebben a példában egy **TwilioCloud** nevű ASP.net webes szerepkör jön létre.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Útmutató: a hívás létrehozásához szükséges kód létrehozása
A következő kód, amely akkor lesz meghívva, amikor a felhasználó befejezi az űrlapot, létrehozza a hívási üzenetet, és létrehozza a hívást. Ebben a példában a kód az űrlapon lévő gomb OnClick eseménykezelőjában fut. (Használja a Twilio-fiókját és a hitelesítési jogkivonatot az alábbi kódhoz megadott helyőrző értékek helyett `accountSID` `authToken` .)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

A rendszer meghívja a hívást, és megjeleníti a Twilio-végpontot, az API-verziót és a hívási állapotot. Az alábbi képernyőfelvételen egy minta futtatásának kimenete látható.

![Azure-hívási válasz a Twilio és a ASP.NET használatával][twilio_dotnet_basic_form_output]

További információ a TwiML a következő címen érhető el: [https://www.twilio.com/docs/api/twiml][twiml] . A &lt; Say &gt; and other Twilio utasítással kapcsolatos további információk a következő címen találhatók: [https://www.twilio.com/docs/api/twiml/say][twilio_say] .

## <a name="next-steps"></a><a id="nextsteps"></a>Következő lépések
Ez a kód olyan alapszintű funkciókat mutat be, amelyek az Azure-ban egy ASP.NET webes szerepkör Twilio használatával rendelkeznek. Mielőtt éles környezetben üzembe helyezi az Azure-t, érdemes lehet további hibakezelés vagy más funkciókat hozzáadnia. Például:

* Webes űrlap helyett használhatja az Azure Blob Storage-t vagy egy Azure SQL Database példányt a telefonszámok és a hívás szövegének tárolására. További információ a Blobok Azure-beli használatáról: [Az Azure Blob Storage szolgáltatás használata a .net-ben][howto_blob_storage_dotnet]. További információ a SQL Database használatáról: a [Azure SQL Database használata .NET-alkalmazásokban][howto_sql_azure_dotnet].
* A paranccsal `RoleEnvironment.getConfigurationSettings` lekérheti a Twilio-fiók azonosítóját és a hitelesítési tokent a központi telepítés konfigurációs beállításaiból, nem pedig az űrlapon lévő értékek rögzített kódolásával. További információ az `RoleEnvironment` osztályról: [Microsoft. WindowsAzure. ServiceRuntime névtér][azure_runtime_ref_dotnet].
* Olvassa el a Twilio biztonsági irányelveit a következő címen: [https://www.twilio.com/docs/security][twilio_docs_security] .
* További információ a Twilio-ről: [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a><a name="seealso"></a>Lásd még
* [A Twilio használata a hang-és SMS-funkciókhoz az Azure-ban](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md