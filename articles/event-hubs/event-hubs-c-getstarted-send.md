---
title: 'Gyors útmutató: események küldése a C-Azure Event Hubs használatával'
description: 'Gyors útmutató: Ez a cikk bemutatja, hogyan hozhat létre olyan C-alkalmazást, amely eseményeket küld az Azure Event Hubsnak.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bfe1ca1a45f7b33d7431aed13446d8d72f79fb90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85315670"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Gyors útmutató: események küldése az Azure Event Hubs C használatával

## <a name="introduction"></a>Bevezetés
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy Event hubhoz a C-ben lévő Console-alkalmazás használatával. 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* C fejlesztési környezet. Ez az oktatóanyag feltételezi, hogy a GCC-verem egy Azure Linux rendszerű virtuális gépen, Ubuntu 14,04-mel van.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Hozzon létre egy Event Hubs névteret és egy Event hubot**. A [Azure Portal](https://portal.azure.com) használatával hozzon létre Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Szerezze be az Event hub elérési kulcsának értékét a következő cikk utasításait követve: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A hozzáférési kulcsot az oktatóanyag későbbi részében írt kódban használhatja. Az alapértelmezett kulcs neve: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Kód írása az üzenetek Event Hubsba való küldéséhez
Ebben a szakaszban bemutatjuk, hogyan írhat egy C alkalmazást az Event hub eseményeinek elküldéséhez. A kód a proton AMQP könyvtárat használja az [Apache csontos projektből](https://qpid.apache.org/). Ez hasonló ahhoz, hogy Service Bus várólistákat és témaköröket használja a C AMQP-ből, ahogy [az ebben a mintában](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)látható. További információt a [csontos proton dokumentációjában](https://qpid.apache.org/proton/index.html)talál.

1. A [csontos AMQP Messenger lapon](https://qpid.apache.org/proton/messenger.html)kövesse a csontos proton telepítésére vonatkozó utasításokat a környezettől függően.
2. A proton-függvénytár fordításához telepítse a következő csomagokat:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Töltse le a [csontos proton-könyvtárat](https://qpid.apache.org/proton/index.html), és bontsa ki, például:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Hozzon létre egy Build könyvtárat, fordítson és telepítsen:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. A munkakönyvtárában hozzon létre egy új fájlt a **Sender. c** néven a következő kóddal. Ne felejtse el lecserélni az SAS-kulcs/név, az Event hub-név és a névtér értékét. A korábban létrehozott **SendRule** tartozó kulcs URL-kódolású verzióját is helyettesítenie kell. [Itt](https://www.w3schools.com/tags/ref_urlencode.asp)URL-cím-kódolást is használhat.
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Fordítsa le a fájlt, feltéve, hogy a **GCC**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ez a kód az 1. kimenő időszakot használja az üzenetek kikényszerítéséhez a lehető leghamarabb. Azt javasoljuk, hogy az alkalmazás az átviteli sebesség növelése érdekében próbálkozzon a Batch-üzenetekkel. A [csontos AMQP Messenger oldalán](https://qpid.apache.org/proton/messenger.html) tájékozódhat arról, hogyan használhatja a csontos proton-függvénytárat ebben és más környezetekben, valamint azokon a platformokon, amelyekhez kötések vannak megadva (jelenleg a Perl, a PHP, a Python és a Ruby).

Futtassa az alkalmazást, hogy üzeneteket küldjön az Event hub-nak. 

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs szolgáltatásai és terminológiája](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
