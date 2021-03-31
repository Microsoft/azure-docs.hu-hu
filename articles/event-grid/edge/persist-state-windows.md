---
title: Állapot megőrzése a Windowsban – Azure Event Grid IoT Edge | Microsoft Docs
description: Állapot megőrzése a Windowsban
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002817"
---
# <a name="persist-state-in-windows"></a>Állapot megőrzése a Windowsban

A Event Grid modulban létrehozott témaköröket és előfizetéseket a rendszer alapértelmezés szerint a tároló fájlrendszerében tárolja. Az adatmegőrzés nélkül, ha a modul újratelepítése megtörténik, az összes létrehozott metaadat elvész. A központi telepítések és újraindítások közötti adatmegőrzéshez a tároló fájlrendszerén kívül kell megőriznie az adattárolást. 

Alapértelmezés szerint a rendszer csak a metaadatokat őrzi meg, az eseményeket pedig a memóriában tárolja a jobb teljesítmény érdekében. Az esemény-megőrzés engedélyezéséhez kövesse az események megőrzése szakaszt is.

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a Windows rendszerű központi telepítések Event Grid moduljának telepítéséhez.

> [!NOTE]
>A Event Grid modul alacsony jogosultsági szintű felhasználói **ContainerUser** fut a Windows rendszerben.

## <a name="persistence-via-volume-mount"></a>Adatmegőrzés a Volume Mount használatával

A [Docker-kötetek](https://docs.docker.com/storage/volumes/) használatával megőrizhető az egyes központi telepítések. Kötet csatlakoztatásához a Docker-parancsokkal létre kell hoznia a tárolót, engedélyeket kell adnia, hogy a tároló képes legyen olvasni, írni, majd üzembe helyezni a modult.

1. Hozzon létre egy kötetet a következő parancs futtatásával:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Példa:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Az alábbi parancs futtatásával szerezze be a kötethez hozzárendelni kívánt gazdagépi könyvtárat:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Példa:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Minta kimenete:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Adja hozzá a **felhasználók** csoportot a **csatlakoztatási pont** által mutatott értékhez a következőképpen:
    1. Indítsa el a fájlkezelőt.
    1. Navigáljon a **csatlakoztatási pont** által mutatott mappára.
    1. Kattintson a jobb gombbal, majd válassza a **Tulajdonságok** lehetőséget.
    1. Válassza a **Biztonság** elemet.
    1. A * csoport vagy felhasználó neve területen válassza a **Szerkesztés** lehetőséget.
    1. Válassza a **Hozzáadás** lehetőséget, írja be `Users` a Névellenőrzés lehetőséget, majd kattintson **az OK gombra**. 
    1. A *Felhasználók engedélyei* területen válassza a **módosítás** lehetőséget, majd kattintson **az OK gombra**.
1. **Kötések** használata a kötet csatlakoztatásához és Event Grid modul újbóli üzembe helyezéséhez Azure Portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Ne módosítsa a kötési érték második részét. A modul egy adott helyére mutat. A Windows Event Grid moduljának a következőnek kell lennie **: C: \\ app \\ metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Adatmegőrzés a gazdagép könyvtárának csatlakoztatása révén

Kötet csatlakoztatása helyett létrehozhat egy könyvtárat a gazdagépen, és csatlakoztathatja a könyvtárat.

1. Hozzon létre egy könyvtárat a gazdagép fájlrendszerén a következő parancs futtatásával.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Példa:

   ```sh
   mkdir C:\myhostdir
   ```
1. A **kötések** használatával csatlakoztassa a könyvtárat, és telepítse újra a Event Grid modult Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Ne módosítsa a kötési érték második részét. A modul egy adott helyére mutat. A Windows Event Grid moduljának a következőnek kell lennie **: C: \\ app \\ metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Események megőrzése

Az események megőrzésének engedélyezéséhez előbb engedélyeznie kell az események megőrzését a Volume Mount vagy a Host Directory Mount használatával a fenti részekkel.

Fontos tudnivalók a megőrzött eseményekről:

* Az események megtartása esemény-előfizetések alapján engedélyezett, és a kötet vagy a könyvtár csatlakoztatása után is engedélyezve van.
* Az esemény-megőrzés a létrehozáskor egy esemény-előfizetésen van konfigurálva, és az esemény-előfizetés létrehozása után nem módosítható. Az események megőrzésének váltásához törölnie kell, majd újra létre kell hoznia az esemény-előfizetést.
* A megőrzött események szinte mindig lassabbak, mint a memóriabeli műveletekben, azonban a sebességbeli különbség nagymértékben függ a meghajtó jellemzőitől. A gyorsaság és a megbízhatóság közötti kompromisszum az összes üzenetkezelő rendszerhez alkalmazkodik, de csak nagy léptékben érezhető lesz.

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