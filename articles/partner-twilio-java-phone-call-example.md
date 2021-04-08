---
title: Telefonhívás kezdeményezése a Twilio (Java) szolgáltatásból | Microsoft Docs
description: Megtudhatja, hogyan tehet telefonhívást egy weboldalról egy Java-alkalmazásban az Azure-ban a Twilio használatával.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: fdaa2eeef58599cbff8dbf7daa34f9a30f31b9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548760"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Telefonhívás kezdeményezése a Twilio használatával az Azure-beli Java-alkalmazásokban
Az alábbi példa bemutatja, hogyan használható a Twilio az Azure-ban üzemeltetett weblapokról. Az eredményül kapott alkalmazás felszólítja a felhasználót a telefonhívások értékeire, ahogy az alábbi képernyőképen is látható.

![Azure Call űrlap a Twilio és a Java használatával][twilio_java]

A jelen témakörben található kód használatához a következőket kell tennie:

1. Twilio-fiók és hitelesítési jogkivonat beszerzése. A Twilio megkezdéséhez értékelje a díjszabást a következő címen: [https://www.twilio.com/pricing][twilio_pricing] . Itt regisztrálhat [https://www.twilio.com/try-twilio][try_twilio] . További információ a Twilio által biztosított API-ról: [https://www.twilio.com/api][twilio_api] .
2. Szerezze be a Twilio JAR-t. A [https://github.com/twilio/twilio-java][twilio_java_github] -ben letöltheti a GitHub-forrásokat, és létrehozhat saját jar-t, vagy letöltheti az előre elkészített jar-t (függőségekkel vagy anélkül).
   A témakörben található kód az előre elkészített TwilioJava-3.3.8-with-függőségei JAR használatával lett írva.
3. Adja hozzá a JAR-t a Java Build elérési útjához.
4. Ha az Eclipse-t használja a Java-alkalmazás létrehozásához, a Twilio JAR-t az alkalmazás telepítési fájljában (WAR) is használja az Eclipse üzembe helyezési szolgáltatásával. Ha nem használja az Eclipse-et a Java-alkalmazás létrehozásához, győződjön meg arról, hogy a Twilio JAR a Java-alkalmazással megegyező Azure-szerepkörbe tartozik, és az alkalmazás osztályának elérési útjába kerül.
5. Győződjön meg arról, hogy a hitesítésszolgáltatói-tároló tartalmazza a Equifax biztonságos hitelesítésszolgáltató tanúsítványát MD5 ujjlenyomattal 67: CB: 9D: C0:13:24:8A: 82:9B: B2:17:1E: D1:1B: EC: D4 (a sorozatszám 35: DE: F4: CF és az SHA1 ujjlenyomata D2:32:09: AD: 23: d3:14:23:21:74: E4:0D: 7F: 9D: 62:13:97:86:63:3A). Ez a hitelesítésszolgáltató (CA) tanúsítványa a [https://api.twilio.com][twilio_api_service] szolgáltatáshoz, amelyet a rendszer Twilio API-k használatakor hív meg.

Emellett a [Hello World alkalmazásnak a Azure Toolkit for Eclipse használatával történő létrehozásával][azure_java_eclipse_hello_world], illetve a Java-alkalmazások Azure-ban való üzemeltetésével kapcsolatos egyéb módszerekkel való ismerete kifejezetten ajánlott.

## <a name="create-a-web-form-for-making-a-call"></a>Webes űrlap létrehozása hívás kezdeményezéséhez
A következő kód bemutatja, hogyan hozhat létre webes űrlapot a felhasználói adathívások lekéréséhez. Ebben a példában egy új, **TwilioCloud** nevű dinamikus webes projekt lett létrehozva, és **callform.jsa p** JSP-fájlként lett hozzáadva.

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Automated call form</title>
    </head>
    <body>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <br/>
        <form action="makecall.jsp" method="post">
            <table>
                <tr>
                    <td>To:</td>
                    <td><input type="text" size=50 name="callTo" value="" />
                    </td>
                </tr>
                <tr>
                    <td>From:</td>
                    <td><input type="text" size=50 name="callFrom" value="" />
                    </td>
                </tr>
                <tr>
                    <td>Call message:</td>
                    <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
                    </td>
                </tr>
                <tr>
                    <td colspan=2><input type="submit" value="Make this call" />
                    </td>
                </tr>
            </table>
        </form>
        <br/>
    </body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>A kód létrehozása a hívás elvégzéséhez
A következő kód, amely akkor lesz meghívva, amikor a felhasználó befejezi callform.jsp által megjelenített űrlapot, létrehozza a hívási üzenetet, és létrehozza a hívást. Ebben a példában a JSP-fájl neve **makecall.jsp** , és hozzá lett adva a **TwilioCloud** projekthez. (Használja a Twilio-fiókját és a hitelesítési tokent a **accountSID** és a **aad** tartozó helyőrző értékek helyett az alábbi kódban.)

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
import="java.util.*"
import="com.twilio.*"
import="com.twilio.sdk.*"
import="com.twilio.sdk.resource.factory.*"
import="com.twilio.sdk.resource.instance.*"
pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
<%
try 
{
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Instantiate an instance of the Twilio client.     
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to retrieve the CallFactory.
    Account account = client.getAccount();

    // Display the client endpoint. 
    out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

    // Display the API version.
    String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
    out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

    // Retrieve the values entered by the user.
    String callTo = request.getParameter("callTo");  
    // The Outgoing Caller ID, used for the From parameter,
    // must have previously been verified with Twilio.
    String callFrom = request.getParameter("callFrom");
    String userText = request.getParameter("callText");

    // Replace spaces in the user's text with '%20', 
    // to make the text suitable for a URL.
    userText = userText.replace(" ", "%20");

    // Create a URL using the Twilio message and the user-entered text.
    String Url="https://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=" + userText;

    // Display the message URL.
    out.println("<p>");
    out.println("The URL is " + Url);
    out.println("</p>");

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", callFrom);
    params.put("To", callTo);
    params.put("Url", Url);

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);
    out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
} 
catch (TwilioRestException e) 
{
    out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
catch (Exception e) 
{
    out.println("<p>Exception encountered: " + e.getMessage() + "");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
%>
    </body>
</html>
```

A hívás elvégzése mellett makecall.jsp a Twilio-végpontot, az API-verziót és a hívási állapotot jeleníti meg. Ilyen például a következő képernyőkép:

![Azure-hívási válasz a Twilio és a Java használatával][twilio_java_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
Az alkalmazás futtatásának magas szintű lépései a következők: ezeknek a lépéseknek [a részletei a Azure Toolkit for Eclipse használatával Hello World alkalmazás létrehozása][azure_java_eclipse_hello_world]című témakörben találhatók.

1. Exportálja a TwilioCloud-HÁBORÚt az Azure **AppRoot** mappába. 
2. Módosítsa a **Startup. cmd** fájlt a TWILIOCLOUD-háború kicsomagolásához.
3. Fordítsa le az alkalmazást a Compute Emulator.
4. Indítsa el a telepítést a Compute Emulator.
5. Nyisson meg egy böngészőt, és futtassa a parancsot `http://localhost:8080/TwilioCloud/callform.jsp` .
6. Adja meg az értékeket az űrlapon, kattintson a **Hívás kezdeményezése** elemre, majd tekintse meg az eredményeket makecall.jsp.

Ha készen áll az Azure-ba való üzembe helyezésre, fordítsa újra a felhőbe történő üzembe helyezést, telepítse az Azure-ba, és futtassa a http://*your_hosted_name*. cloudapp.net/TwilioCloud/callform.jsp böngészőt a böngészőben (adja meg az értékét *your_hosted_name*).

## <a name="next-steps"></a>Következő lépések
Ez a kód olyan alapszintű funkciókat mutat be, amelyek az Azure-ban Java Twilio-t használnak. Mielőtt éles környezetben üzembe helyezi az Azure-t, érdemes lehet további hibakezelés vagy más funkciókat hozzáadnia. Például:

* Webes űrlap helyett az Azure Storage-Blobok vagy a SQL Database használatával tárolhatók a telefonszámok és a hívás szövege. További információ az Azure Storage-Blobok javában való használatáról: [a blob Storage szolgáltatás használata Java-ból][howto_blob_storage_java]. 
* A **RoleEnvironment. getConfigurationSettings** használatával lekérheti a Twilio-fiók azonosítóját és a hitelesítési tokent a központi telepítés konfigurációs beállításaiból ahelyett, hogy az értékeket a makecall.jsp-ben rögzítette. További információ a **RoleEnvironment** osztályról: [Az Azure Service runtime library használata a JSP-ben][azure_runtime_jsp].
* A makecall.jsp-kód egy Twilio által megadott URL-címet rendel hozzá [https://twimlets.com/message][twimlet_message_url] az **URL-** változóhoz. Ez az URL-cím egy Twilio Markup Language (TwiML) választ tartalmaz, amely tájékoztatja a Twilio a hívás folytatásáról. Például a visszaadott TwiML tartalmazhatnak **&lt; &gt; egy olyan** beszédet, amely a hívás címzettjének küldött szöveget eredményezi. A Twilio által biztosított URL-cím használata helyett saját szolgáltatást építhet ki a Twilio kérelmére való reagáláshoz. További információkért lásd: a [Twilio használata a hang-és SMS-képességekhez a javában][howto_twilio_voice_sms_java]. További információ a TwiML-ról: [https://www.twilio.com/docs/api/twiml][twiml] , és további információ a **&lt; &gt; Say** and other Twilio műveletekről: [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Olvassa el a Twilio biztonsági irányelveit a következő címen: [https://www.twilio.com/docs/security][twilio_docs_security] .

További információ a Twilio-ről: [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Lásd még:
* [A Twilio használata a hang-és SMS-funkciókhoz Java-ban][howto_twilio_voice_sms_java]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
