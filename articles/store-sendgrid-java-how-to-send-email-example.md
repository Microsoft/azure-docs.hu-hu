---
title: Store-sendgrid-Java-How-to-Send-e-mail – példa
description: E-mail küldése a SendGrid-ből Java-ban Azure-környezetben. Az eredményül kapott alkalmazás felszólítja a felhasználót az e-mail-értékekre.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.custom: devx-track-java
ms.openlocfilehash: 7a857b887b53ccb2288507742ca52d72d90ff548
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214354"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>E-mailek küldése a SendGrid a Java használatával Azure-beli üzemelő példányban
Az alábbi példa bemutatja, hogyan küldhet e-maileket az Azure-ban üzemeltetett weblapokról a SendGrid használatával. Az eredményül kapott alkalmazás felszólítja a felhasználót az e-mail-értékekre, ahogy az alábbi képernyőképen is látható.

![E-mail űrlap][emailform]

Az eredményül kapott e-mail a következő képernyőképhez hasonlóan fog kinézni.

![E-mail üzenet][emailsent]

A jelen témakörben található kód használatához a következőket kell tennie:

1. Szerezze be a javax. mail tégelyeket, például: <https://www.oracle.com/technetwork/java/javamail/index.html> .
2. Adja hozzá a tégelyeket a Java Build elérési útjához.
3. Ha az Eclipse-t használja a Java-alkalmazás létrehozásához, a SendGrid-kódtárakat felveheti az alkalmazás telepítési fájljába (WAR) az Eclipse üzembe helyezési funkciójának használatával. Ha nem az Eclipse-t használja a Java-alkalmazás létrehozásához, győződjön meg arról, hogy a kódtárak a Java-alkalmazáshoz tartozó Azure-szerepkörbe tartoznak, és az alkalmazás osztályának elérési útjába kerülnek.

Az e-mail elküldéséhez saját SendGrid-felhasználónevét és jelszavát is meg kell adnia. A SendGrid használatának megkezdéséhez tekintse meg az [e-mailek küldése a SendGrid használatával Java-ból](store-sendgrid-java-how-to-send-email.md)című témakört.

Ezen kívül az [Azure-hoz készült Hello World alkalmazás létrehozása az Eclipse-ben](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app), illetve a Java-alkalmazások Azure-ban való üzemeltetésének egyéb módszereivel, ha nem az Eclipse-t használja, kifejezetten ajánlott.

## <a name="create-a-web-form-for-sending-email"></a>Webes űrlap létrehozása e-mailek küldéséhez
A következő kód bemutatja, hogyan hozhat létre webes űrlapot az e-mailek küldéséhez szükséges felhasználói adatok lekéréséhez. A tartalom szempontjából a JSP-fájl neve **emailform.jsp**.

```html
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email form</title>
</head>
<body>
  <p>Fill in all fields and click <b>Send this email</b>.</p>
  <br/>
  <form action="sendemail.jsp" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input type="text" size=50 name="emailTo">
        </td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input type="text" size=50 name="emailFrom">
        </td>
      </tr>
      <tr>
        <td>Subject:</td>
        <td><input type="text" size=100 name="emailSubject" value="My email subject">
        </td>
      </tr>
      <tr>
        <td>Text:</td>
        <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
        </td>
      </tr>
      <tr>
        <td>SendGrid user name:</td>
        <td><input type="text" name="sendGridUser">
        </td>
      </tr>
      <tr>
        <td>SendGrid password:</td>
        <td><input type="password" name="sendGridPassword">
        </td>
      </tr>
      <tr>
        <td colspan=2><input type="submit" value="Send this email">
        </td>
      </tr>
    </table>
  </form>
  <br/>
</body>
</html>
```

## <a name="create-the-code-to-send-the-email"></a>A kód létrehozása az e-mail elküldéséhez
A következő kód, amely akkor lesz meghívva, amikor az űrlapot a emailform.jsp-ben végezte el, létrehozza az e-mail-üzenetet, és elküldi azt. A tartalom szempontjából a JSP-fájl neve **sendemail.jsp**.

```java
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email processing happens here</title>
</head>
<body>
    <b>This is my send mail page.</b><p/>
  <%

  final String sendGridUser = request.getParameter("sendGridUser");
  final String sendGridPassword = request.getParameter("sendGridPassword");

  class SMTPAuthenticator extends Authenticator
  {
    public PasswordAuthentication getPasswordAuthentication()
    {
        String username = sendGridUser;
        String password = sendGridPassword;

        return new PasswordAuthentication(username, password);   
    }
  }
  try
  {

      // The SendGrid SMTP server.
      String SMTP_HOST_NAME = "smtp.sendgrid.net";

      Properties properties;

      properties = new Properties();

      // Specify SMTP values.
      properties.put("mail.transport.protocol", "smtp");
      properties.put("mail.smtp.host", SMTP_HOST_NAME);
      properties.put("mail.smtp.port", 587);
      properties.put("mail.smtp.auth", "true");

      // Display the email fields entered by the user. 
      out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
      out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
      out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
      out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

      // Create the authenticator object.
      Authenticator authenticator = new SMTPAuthenticator();

      // Create the mail session object.
      Session mailSession;
      mailSession = Session.getDefaultInstance(properties, authenticator);

      // Display debug information to stdout, useful when using the
      // compute emulator during development.
      mailSession.setDebug(true);

      // Create the message and message part objects.
      MimeMessage message;
      Multipart multipart;
      MimeBodyPart messagePart; 

      message = new MimeMessage(mailSession);

      multipart = new MimeMultipart("alternative");
      messagePart = new MimeBodyPart();
      messagePart.setContent(request.getParameter("emailText"), "text/html");
      multipart.addBodyPart(messagePart);            

      // Specify the email To, From, Subject and Content. 
      message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
      message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
      message.setSubject(request.getParameter("emailSubject")); 
      message.setContent(multipart);

      // Uncomment the following if you want to add a footer.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

      // Uncomment the following if you want to enable click tracking.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

      Transport transport;
      transport = mailSession.getTransport();
      // Connect the transport object.
      transport.connect();
      // Send the message.
      transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
      // Close the connection.
      transport.close();

    out.println("<p>Email processing completed.</p>");

  }
  catch (Exception e)
  {
      out.println("<p>Exception encountered: " + 
                        e.getMessage()     +
                        "</p>");   
  }
%>

</body>
</html>
```

Az e-mailek elküldése mellett emailform.jsp a felhasználó eredményét is megadja; ilyen például a következő képernyőkép:

![E-mail eredményének küldése][emailresult]

## <a name="next-steps"></a>Következő lépések
Telepítse az alkalmazást a Compute Emulatorre, és egy böngészőben futtassa emailform.jsp-t, írja be az értékeket az űrlapba, kattintson az **E-mail küldése** elemre, majd tekintse meg az eredményeket sendemail.jsp.

Ez a kód azt mutatja be, hogy miként használható a SendGrid az Azure-ban Java-ban. Mielőtt éles környezetben üzembe helyezi az Azure-t, érdemes lehet további hibakezelés vagy más funkciókat hozzáadnia. Például: 

* Az Azure Storage-Blobok vagy a SQL Database használatával e-mail-címeket és e-mail-üzeneteket tárolhat webes űrlap helyett. További információ az Azure Storage-Blobok javában való használatáról: [a blob Storage szolgáltatás használata Java-ból](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). További információ a Java-SQL Database használatáról: [SQL Database használata a javában](./azure-sql/database/connect-query-java.md).
* További információ a Java-SendGrid használatáról: [az e-mailek küldése a SendGrid a Java használatával](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg