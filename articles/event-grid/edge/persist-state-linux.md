---
title: Állapot megőrzése Linux rendszeren – Azure Event Grid IoT Edge | Microsoft Docs
description: Metaadatok megőrzése Linuxon
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171482"
---
# <a name="persist-state-in-linux"></a>Állapot megőrzése Linuxon

A Event Grid modulban létrehozott témaköröket és előfizetéseket a rendszer alapértelmezés szerint a tároló fájlrendszerében tárolja. Az adatmegőrzés nélkül, ha a modul újratelepítése megtörténik, az összes létrehozott metaadat elvész. A központi telepítések és újraindítások közötti adatmegőrzéshez a tároló fájlrendszerén kívül kell megőriznie az adattárolást.

Alapértelmezés szerint a rendszer csak a metaadatokat őrzi meg, az eseményeket pedig a memóriában tárolja a jobb teljesítmény érdekében. Az esemény-megőrzés engedélyezéséhez kövesse az események megőrzése szakaszt is.

Ez a cikk azokat a lépéseket ismerteti, amelyekkel üzembe helyezheti a Event Grid modult a Linux rendszerű üzemelő példányokban.

> [!NOTE]
>Az Event Grid modul alacsony jogosultsági szintű felhasználóként fut UID `2000` és Name néven `eventgriduser` .

## <a name="persistence-via-volume-mount"></a>Adatmegőrzés a Volume Mount használatával

 A [Docker-kötetek](https://docs.docker.com/storage/volumes/) a központi telepítések közötti adatmegőrzésre szolgálnak. Lehetővé teheti, hogy a Docker automatikusan hozzon létre egy nevesített kötetet a Event Grid modul üzembe helyezésének részeként. Ez a legegyszerűbb lehetőség. A **kötések** szakaszban megadhatja a kötet nevét, amelyet a következőképpen hozhat létre:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Ne módosítsa a kötési érték második részét. Egy adott helyre mutat a modulon belül. A Linux Event Grid moduljának **/app/metadataDb** kell lennie.

A következő konfiguráció például azt eredményezi, hogy a kötet **egmetadataDbVol** hozza létre, ahol a metaadatok megmaradnak.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Kötet csatlakoztatása helyett létrehozhat egy könyvtárat a gazdagépen, és csatlakoztathatja a könyvtárat.

## <a name="persistence-via-host-directory-mount"></a>Adatmegőrzés a gazdagép könyvtárának csatlakoztatása révén

A Docker-kötetek helyett lehetősége van a gazdagép-mappák csatlakoztatására is.

1. Először hozzon létre egy **eventgriduser** és ID **2000** nevű felhasználót a gazdagépen a következő parancs futtatásával:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. A következő parancs futtatásával hozzon létre egy könyvtárat a gazdagép fájlrendszerén.

   ```sh
   md <your-directory-name-here>
   ```

    A következő parancs futtatása például létrehoz egy **myhostdir** nevű könyvtárat.

    ```sh
    md /myhostdir
    ```
1. Ezután a következő parancs futtatásával tegye **eventgriduser** a mappa tulajdonosát.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Példa:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. A **kötések** használatával csatlakoztassa a könyvtárat, és telepítse újra a Event Grid modult Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Példa:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Ne módosítsa a kötési érték második részét. Egy adott helyre mutat a modulon belül. A Linux Event Grid moduljának **/app/metadataDb** és **/app/eventsDb** kell lennie


## <a name="persist-events"></a>Események megőrzése

Az események megőrzésének engedélyezéséhez előbb engedélyeznie kell a metaadatok megőrzését a Volume Mount vagy a Host Directory Mount használatával a fenti fejezetek segítségével.

Fontos tudnivalók a megőrzött eseményekről:

* Az események megtartása esemény-előfizetések alapján engedélyezett, és a kötet vagy a könyvtár csatlakoztatása után is engedélyezve van.
* Az esemény-megőrzés a létrehozáskor egy esemény-előfizetésen van konfigurálva, és az esemény-előfizetés létrehozása után nem módosítható. Az események megőrzésének váltásához törölnie kell, majd újra létre kell hoznia az esemény-előfizetést.
* A megőrzött események szinte mindig lassabbak, mint a memóriabeli műveletekben, azonban a sebességbeli különbség nagymértékben függ a meghajtó jellemzőitől. A gyorsaság és a megbízhatóság közötti kompromisszum minden üzenetkezelő rendszerhez alkalmazkodik, de általában nagy léptékben észlelhető.

Ha engedélyezni szeretné az események megőrzését egy esemény-előfizetésben, állítsa a következőre `persistencePolicy` `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
