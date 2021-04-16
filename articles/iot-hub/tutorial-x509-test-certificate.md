---
title: Oktatóanyag – Az X.509-tanúsítványok azon képességének tesztelése, amely lehetővé teszi az eszközök hitelesítését egy Azure IoT Hub | Microsoft Docs
description: Oktatóanyag – X.509-tanúsítványok tesztelése az Azure IoT Hub
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
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379434"
---
# <a name="tutorial-testing-certificate-authentication"></a>Oktatóanyag: Tanúsítványhitelesítés tesztelése

Az alábbi C#-példakód használatával tesztelheti, hogy a tanúsítvány hitelesítheti-e az eszközt a IoT Hub. Vegye figyelembe, hogy a tesztkód futtatása előtt a következőket kell megtennie:

* Hozzon létre egy legfelső szintű hitelesítésszolgáltatói vagy alárendelt CA-tanúsítványt.
* Töltse fel a hitelesítésszolgáltatói tanúsítványt a IoT Hub.
* Igazolja, hogy rendelkezik a CA-tanúsítvánnyal.
* Adjon hozzá egy eszközt a IoT Hub.
* Hozzon létre egy eszköztanúsítványt ugyanazokkal az eszközazonosítóval, mint az eszközét.

## <a name="code-example"></a>Példakód

Az alábbi példakód bemutatja, hogyan hozhat létre egy C#-alkalmazást az IoT Hubhoz regisztrált X.509-eszköz szimulálására. A példa hőmérséklet- és páratartalom-értékeket küld a szimulált eszközről a hubnak. Ebben az oktatóanyagban csak az eszközalkalmazást fogjuk létrehozni. Az olvasókra marad a feladat, hogy létrehozták a IoT Hub-szolgáltatásalkalmazást, amely válaszokat küld a szimulált eszköz által küldött eseményekre.

1. Nyissa Visual Studio, válassza **a Create a new project (Új** projekt létrehozása) lehetőséget, majd a Console App (.NET-keretrendszer) (Konzolalkalmazás **(.NET-keretrendszer)** projektsablont. Kattintson a **Tovább** gombra.

1. A **Configure your new project (Új projekt konfigurálása) mezőben** adja a *simulateX509Device nevet* a projektnek, majd válassza a Create **(Létrehozás) lehetőséget.**

   ![X.509-eszközprojekt létrehozása a Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. A Megoldáskezelő kattintson a jobb gombbal a **SimulateX509Device projektre,** majd válassza a **Manage NuGet Packages (NuGet-csomagok kezelése) lehetőséget.**

1. A **NuGet-Csomagkezelő** válassza a **Tallózás lehetőséget,** keresse meg és válassza a **Microsoft.Azure.Devices.Client lehetőséget.** Válassza a **Telepítés** gombot.

   ![EszközOLDALI SDK NuGet-csomag hozzáadása a Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Ez a lépés letölti és telepíti az Azure IoT eszközoldali SDK NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá vonatkozó hivatkozást is.

    Adja meg és futtassa a következő kódot:

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