---
title: OPC-közzétevő konfigurálása – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az OPC-közzétevőt az OPC UA-csomópont-adatváltozások, az OPC UA-események közzétételére és a telemetria formátum megadására.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 405fa04a3b6af7f0c87888aa48aba9bdffeaf282
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646195"
---
# <a name="configure-opc-publisher"></a>Az OPC Publisher konfigurálása

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Az OPC-közzétevőt az alábbiak megadására állíthatja be:

- Az OPC UA-csomópont adatváltozása közzétételre változik.
- Az OPC UA-események közzététele.
- A telemetria formátuma.

Az OPC-közzétevőt konfigurációs fájlokkal vagy metódus-hívásokkal is konfigurálhatja.

## <a name="use-configuration-files"></a>Konfigurációs fájlok használata

Ez a szakasz az OPC UA csomópont-közzététel konfigurációs fájlokkal történő konfigurálásának lehetőségeit ismerteti.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Konfigurációs fájl használata a közzétételi adatváltozások konfigurálásához

Az OPC UA-csomópontok közzétételre való konfigurálásának legegyszerűbb módja egy konfigurációs fájl. A konfigurációs fájl formátuma a tárház [publishednodes.jsjában](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) van dokumentálva.

A konfigurációs fájl szintaxisa módosult az idő múlásával. Az OPC-közzétevő továbbra is beolvassa a régi formátumokat, de a konfiguráció megőrzése után a legújabb formátumra alakítja őket.

A következő példa a konfigurációs fájl formátumát mutatja be:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Konfigurációs fájl használata a közzétételi események konfigurálásához

OPC UA-események közzétételéhez ugyanazt a konfigurációs fájlt használja, mint az adatváltozások esetében.

Az alábbi példa bemutatja, hogyan konfigurálhatja a [SimpleEvents-kiszolgáló](https://github.com/OPCFoundation/UA-.NETStandard-Samples/tree/master/Workshop/SimpleEvents/Server)által generált események közzétételét. A SimpleEvents-kiszolgáló az [OPC Foundation adattárában](https://github.com/OPCFoundation/UA-.NETStandard-Samples) található:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Metódus-hívások használata

Ez a szakasz az OPC-közzétevő konfigurálásához használható metódusi hívásokat ismerteti.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurálás OPC UA-metódusok használatával

Az OPC-közzétevő tartalmaz egy OPC UA-kiszolgálót, amely a 62222-es porton érhető el. Ha az állomásnév **közzétevő**, akkor a végpont URI azonosítója: `opc.tcp://publisher:62222/UA/Publisher` .

Ez a végpont a következő négy módszert teszi elérhetővé:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurálás IoT Hub közvetlen metódus-hívások használatával

Az OPC-közzétevő a következő IoT Hub közvetlen metódus-hívásokat valósítja meg:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

A metódus-kérelem és a válaszok JSON-tartalmának formátuma a [opcpublisher/HubMethodModel. cs](https://github.com/Azure/iot-edge-opc-publisher/tree/master/opcpublisher)fájlban van definiálva.

Ha ismeretlen metódust hív meg a modulban, az egy olyan karakterlánccal válaszol, amely szerint a metódus nincs implementálva. Ismeretlen metódust hívhat meg a modul pingelésének módjaként.

### <a name="configure-username-and-password-for-authentication"></a>Felhasználónév és jelszó konfigurálása hitelesítéshez

A hitelesítési mód beállítható egy IoT Hub közvetlen metódus hívásával. Az adattartalomnak tartalmaznia kell a **OpcAuthenticationMode** tulajdonságot és a felhasználónevet és a jelszót:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

A jelszót a IoT Hub munkaterhelés-ügyfél titkosítja, és a közzétevő konfigurációjában tárolja. A hitelesítés névtelenre való visszaállításához használja a metódust a következő hasznos tartalommal:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Ha a **OpcAuthenticationMode** tulajdonság nincs beállítva a hasznos adatok között, a hitelesítési beállítások változatlanok maradnak a konfigurációban.

## <a name="configure-telemetry-publishing"></a>Telemetria-közzététel konfigurálása

Ha az OPC-közzétevő értesítést kap egy közzétett csomópontban lévő érték változásáról, akkor a IoT Hubba küldött JSON formátumú üzenetet hoz létre.

A JSON formátumú üzenet tartalmát konfigurációs fájllal is konfigurálhatja. Ha nincs megadva konfigurációs fájl a `--tc` kapcsolóval, a rendszer egy alapértelmezett konfigurációt használ, amely kompatibilis a [csatlakoztatott gyári megoldás-gyorsító](https://github.com/Azure/azure-iot-connected-factory)használatával.

Ha az OPC-közzétevő batch-üzenetekre van konfigurálva, akkor azok érvényes JSON-tömbként lesznek elküldve.

A telemetria a következő forrásokból származik:

- A csomópont OPC közzétevő csomópontjának konfigurációja
- Az OPC UA-verem **MonitoredItem** -objektuma, amelyhez az OPC-közzétevő értesítést kapott.
- Az értesítésnek átadott argumentum, amely részletesen ismerteti az adatérték változását.

A JSON formátumú üzenetbe helyezett telemetria az objektumok fontos tulajdonságai közül választhat. Ha további tulajdonságokra van szüksége, módosítania kell az OPC közzétevő kódjának alapját.

A konfigurációs fájl szintaxisa a következő:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is omitted (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC-közzétevő konfigurálását, a javasolt következő lépés az [OPC-közzétevő futtatásának](howto-opc-publisher-run.md)megismerése.
