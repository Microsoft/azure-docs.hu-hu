---
title: Long-Runningi műveletek lekérdezése | Microsoft Docs
description: A Azure Media Services olyan API-kat biztosít, amelyek a kérelmek küldését küldik Media Services számára (például egy csatorna létrehozása, elindítása, leállítása vagy törlése), ezek a műveletek hosszú ideig futnak. Ez a témakör bemutatja, hogyan lehet lekérdezni a hosszan futó műveleteket.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7926f7aaa427d49d13cab5e13f5153bcd22e5898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013822"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Élő közvetítés továbbítása Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés

A Microsoft Azure Media Services olyan API-kat kínál, amelyek kérelmeket küldenek Media Servicesnak a műveletek elindításához (például: létrehozás, indítás, Leállítás vagy törlés). Ezek a műveletek hosszú ideig futnak.

A Media Services .NET SDK olyan API-kat biztosít, amelyek elküldik a kérést, és megvárja, amíg a művelet befejeződik (belsőleg, az API-k bizonyos időközönként lekérdezik a művelet előrehaladását). Például a csatorna hívásakor. A Start () függvény a metódust a csatorna elindítása után adja vissza. Használhatja az aszinkron verziót is: várakozási csatorna. StartAsync () (a feladat-alapú aszinkron mintázattal kapcsolatos információkért lásd: [Koppintson](./media-services-mes-schema.md)). Azok az API-k, amelyek műveleti kérelmet küldenek, majd lekérdezik az állapotot, amíg a művelet befejeződik, "lekérdezési módszereknek" nevezzük. Ezeket a metódusokat (különösen az aszinkron verziót) a gazdag ügyfélalkalmazások és/vagy állapot-nyilvántartó szolgáltatások esetében ajánlott használni.

Vannak olyan helyzetek, amikor egy alkalmazás nem várhat hosszú ideig futó HTTP-kérést, és manuálisan szeretné lekérdezni a művelet folyamatát. Egy tipikus példa egy állapot nélküli webszolgáltatást használó böngésző: Ha a böngésző a csatorna létrehozására kéri, a webszolgáltatás hosszan futó műveletet kezdeményez, és visszaadja a művelet AZONOSÍTÓját a böngészőnek. A böngésző ezután megkérheti a webszolgáltatást, hogy a művelet állapotát az azonosító alapján kapja meg. A Media Services .NET SDK olyan API-kat biztosít, amelyek ehhez a forgatókönyvhöz hasznosak. Ezeket az API-kat "nem lekérdezési módszereknek" nevezzük.
A "nem lekérdezési módszerek" a következő elnevezési mintával rendelkeznek:*OperationName* művelet küldése (például SendCreateOperation). A *OperationName* művelet-metódusok küldése a **IOperation** objektumot visszaküldi. a visszaadott objektum a művelet nyomon követéséhez használható adatokat tartalmaz. A Send *OperationName* OperationAsync metódusok visszatérési **feladata \<IOperation>**.

Jelenleg a következő osztályok támogatják a nem lekérdezési módszereket:  **Channel**, **streamvégpontok** és **program**.

A műveleti állapot lekérdezéséhez használja a **getoperation hívásnál** metódust a **OperationBaseCollection** osztályban. A művelet állapotának vizsgálatához használja a következő időközöket: **csatorna** -és **streamvégpontok** műveletek esetén, 30 másodpercet használjon; a **program** műveleteihez használjon 10 másodpercet.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel.

## <a name="example"></a>Példa

A következő példa egy **ChannelOperations** nevű osztályt határoz meg. Ez az osztály-definíció lehet a webszolgáltatás-osztály definíciójának kiindulási pontja. Az egyszerűség kedvéért az alábbi példák a metódusok nem aszinkron verzióit használják.

A példa azt is bemutatja, hogyan használják az ügyfél ezt az osztályt.

### <a name="channeloperations-class-definition"></a>ChannelOperations osztály definíciója

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>Az ügyfél kódja

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]