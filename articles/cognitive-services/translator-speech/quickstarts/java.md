---
title: 'Gyors útmutató: Translator Speech API Java'
titlesuffix: Azure Cognitive Services
description: Információ és kódminták segítségével ismerkedhet meg a Translator Speech API használatának alapjaival.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bdf207fadd71d843e4ee72a645e787d3d50c12f5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966427"
---
# <a name="quickstart-translator-speech-api-with-java"></a>Gyors útmutató: Translator Speech API Javával
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ez a cikk bemutatja, hogyan használhatja a Translator Speech API-t egy .wav fájlban elhangzó beszéd lefordításához.

## <a name="prerequisites"></a>Előfeltételek

A kód lefordításához és futtatásához a [JDK 7 vagy 8](https://aka.ms/azure-jdks) telepítése szükséges. Ha van kedvence, használhat Java IDE-t vagy egy szövegszerkesztőt is.

A következő fájlokra lesz szüksége.
- [javax.websocket-api-1.1.jar (vagy újabb)](https://mvnrepository.com/artifact/javax.websocket/javax.websocket-api)
- [jetty-http-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-http)
- [jetty-io-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-io)
- [jetty-util-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-util)
- [websocket-api-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-api)
- [websocket-client-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-client)
- [websocket-common-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-common)
- [javax-websocket-client-impl-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/javax-websocket-client-impl)
- [jetty-client-9.4.11.v20180605.jar (vagy újabb)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-client)

A „speak.wav” fájlnak ugyanabban a mappában kell lennie, mint az alábbi kódból fordított végrehajtható fájlnak. Ennek a .wav fájlnak standard PCM, 16 bites, 16 kHz-es, mono formátumúnak kell lennie. Ilyen .wav fájlt a [Text to Speech API-tól](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) szerezhet be.

Rendelkeznie kell egy [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) és a **Microsoft Translator Speech API-val**. Egy fizetős előfizetői kulcsra van szüksége az [Azure-irányítópultról](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Beszéd fordítása

A következő kód beszédet fordít le egyik nyelvről a másikra.

1. Hozzon létre egy új Java-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

Config.java:

```java
import java.util.*;

import javax.websocket.ClientEndpointConfig;

public class Config extends ClientEndpointConfig.Configurator {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    static String key = "ENTER KEY HERE";

    @Override
    public void beforeRequest (Map<String,List<String>> headers) {
        headers.put("Ocp-Apim-Subscription-Key", Arrays.asList (key));
    }
}
```

Client.java:

```java
import java.io.*;
import java.net.*;
import java.util.*;

import javax.websocket.ClientEndpoint;
import javax.websocket.CloseReason;
import javax.websocket.ContainerProvider;
import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.WebSocketContainer;

import org.eclipse.jetty.client.*;
import org.eclipse.jetty.http.*;
import org.eclipse.jetty.io.*;
import org.eclipse.jetty.util.*;
import org.eclipse.jetty.websocket.api.*;
import org.eclipse.jetty.websocket.client.io.*;
import org.eclipse.jetty.websocket.common.scopes.*;

/* Useful reference links:
    https://docs.oracle.com/javaee/7/api/javax/websocket/Session.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/RemoteEndpoint.Basic.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
    https://www.oracle.com/technetwork/articles/java/jsr356-1937161.html
*/

@ClientEndpoint(configurator = Config.class)
public class Client {
    static String host = "wss://dev.microsofttranslator.com";
    static String path = "/speech/translate";
    static String params = "?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa";
    static String uri = host + path + params;

    static String input_path = "speak.wav";
    static String output_path = "speak2.wav";

    Session session = null;

    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        System.out.println ("Connected.");
        SendAudio ();
    }

    @OnMessage
    public void onTextMessage(String message, Session session){
        System.out.println ("Text message received.");
        System.out.println (message);
    }

/*
Use the following signature to receive the message in parts:
    public void onBinaryMessage(byte[] buffer, boolean last, Session session)
Use the following signature to receive the entire message:
    public void onBinaryMessage(byte[] buffer, Session session)
See:
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
*/
    @OnMessage
    public void onBinaryMessage(byte[] buffer, Session session) {
        try {
            System.out.println ("Binary message received.");
            FileOutputStream stream = new FileOutputStream(output_path);
            stream.write(buffer);
            stream.close();
            System.out.println ("Message contents written to file.");
            Close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    @OnError
    public void onError(Session session, Throwable e) {
        System.out.println ("Error message received: " + e.getMessage ());
    }

    @OnClose
    public void myOnClose (CloseReason reason) {
        System.out.println ("Close message received: " + reason.getReasonPhrase());
    }

    public void SendAudio() {
        try {
            File file = new File(input_path);
            FileInputStream stream = new FileInputStream (file);
            byte buffer[] = new byte[(int)file.length()];
            stream.read(buffer);
            stream.close();
            sendMessage (buffer);
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void sendMessage(byte[] message) {
        try {
            System.out.println ("Sending audio.");
            OutputStream stream = this.session.getBasicRemote().getSendStream();
            stream.write (message);
/* Make sure the audio file is followed by silence.
 * This lets the service know that the audio file is finished.
 * At 32 bytes per millisecond, this is 10 seconds of silence. */
            System.out.println ("Sending silence.");
            byte silence[] = new byte[320000];
            stream.write (silence);
            stream.flush();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Connect () throws Exception {
        try {
            System.out.println("Connecting.");
            WebSocketContainer container = ContainerProvider.getWebSocketContainer();
/* The response message might exceed the default max message size of 65536. */
            container.setDefaultMaxBinaryMessageBufferSize(131072);
/* Some code samples show container.connectToServer as returning a Session, but this seems to be false. */
            container.connectToServer(this, new URI(uri));
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Close () throws Exception {
        try {
            System.out.println("Closing connection.");
            this.session.close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }
}
```

Speak.java:

```java
/*
Download javax.websocket-api-1.1.jar (or newer) from:
    http://central.maven.org/maven2/javax/websocket/javax.websocket-api/1.1/
Download jetty-http-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-http
Download jetty-io-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-io
Download jetty-util-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-util
Download websocket-api-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-api
Download websocket-client-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-client
Download websocket-common-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-common
Download javax-websocket-client-impl-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/javax-websocket-client-impl
Download jetty-client-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-client

Compile and run with:
    javac Config.java -cp .;javax.websocket-api-1.1.jar
    javac Client.java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar
    javac Speak.java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar
    java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar Speak
*/

import java.lang.Thread;

public class Speak {
    public static void main(String[] args) {
        try {
            Client client = new Client ();
            client.Connect ();
            // Wait for the reply.
            Thread.sleep (5000);
            System.out.println ("Press Enter to exit the application at any time.");
            System.in.read();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Beszéd fordításának válasza**

A „speak2.wav” fájl létrejötte sikeres eredményt jelent. Ez a fájl tartalmazza a „speak.wav” fájlban elhangzó szavak fordítását.

[Vissza a tetejére](#HOLTop)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Translator Speech – oktatóanyag](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Lásd még

[A Translator Speech áttekintése](../overview.md)
[API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
