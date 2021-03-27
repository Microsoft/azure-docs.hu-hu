---
title: Oktatóanyag – X. 509 tanúsítványok tesztelési képessége az eszközök Azure-IoT Hub való hitelesítéséhez | Microsoft Docs
description: Oktatóanyag – az X. 509 tanúsítványok tesztelése az Azure-IoT Hub való hitelesítéshez
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 91eea344914120a396ba9465ec504a37f5844d4e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630674"
---
# <a name="tutorial-testing-certificate-authentication"></a>Oktatóanyag: tanúsítvány-hitelesítés tesztelése

A következő C#-kóddal ellenőrizheti, hogy a tanúsítvány hitelesítheti-e az eszközt a IoT Hub. Vegye figyelembe, hogy a teszt kód futtatása előtt el kell végeznie a következőket:

* Hozzon létre egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT vagy alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt.
* Töltse fel a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a IoT Hubba.
* Igazolja, hogy rendelkezik a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal.
* Adjon hozzá egy eszközt a IoT Hubhoz.
* Hozzon létre egy eszköz-tanúsítványt ugyanazzal az eszköz-AZONOSÍTÓval, mint az eszközön.

## <a name="code-example"></a>Példa programkódra

A következő mintakód bemutatja, hogyan hozhat létre C#-alkalmazást az IoT hub-hoz regisztrált X. 509 eszköz szimulálásához. A példa a szimulált eszköz hőmérséklet-és páratartalom-értékeit a hubhoz küldi. Ebben az oktatóanyagban csak az eszköz alkalmazást fogjuk létrehozni. Az olvasók feladata, hogy létrehozza a IoT Hub szolgáltatásalkalmazás létrehozását, amely válaszokat küld a szimulált eszköz által küldött eseményekre.

1. Nyissa meg a Visual studiót, válassza az **új projekt létrehozása** lehetőséget, majd válassza a **Console app (.NET-keretrendszer)** projekt sablonját. Kattintson a **Tovább** gombra.

1. Az **új projekt konfigurálása** lapon nevezze el a projekt *SimulateX509Device*, majd válassza a **Létrehozás** lehetőséget.

   ![X. 509 eszköz projekt létrehozása a Visual Studióban](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Megoldáskezelő kattintson a jobb gombbal a **SimulateX509Device** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.

1. A **NuGet csomagkezelő eszközben** válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices. Client** elemet. Válassza a **Telepítés** gombot.

   ![Device SDK NuGet-csomag hozzáadása a Visual Studióban](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Ez a lépés letölti, telepíti és hozzáadja az Azure IoT Device SDK NuGet csomagra és annak függőségeire mutató hivatkozást.

    Írja be és futtassa a következő kódot:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```