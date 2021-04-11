---
title: API-proxy modul konfigurálása – Azure IoT Edge | Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre az API-proxy modult IoT Edge átjáró-hierarchiák esetében.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1070a4c8daecfedae513f2fd8738c27abfb33078
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200580"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Az API-proxy modul konfigurálása az átjáró-hierarchia forgatókönyvéhez (előzetes verzió)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Az API-proxy modul lehetővé teszi, hogy IoT Edge eszközök HTTP-kéréseket küldjenek az átjárón keresztül a Cloud Services közvetlen kapcsolatainak használata helyett. Ez a cikk végigvezeti a konfigurációs beállításokon, így testreszabhatja a modult az átjáró-hierarchia követelményeinek támogatásához.

>[!NOTE]
>Ehhez a szolgáltatáshoz a IoT Edge 1,2-es verziója szükséges, amely nyilvános előzetes verzióban, Linux-tárolókat futtat.

Bizonyos hálózati architektúrákban IoT Edge az átjárók mögötti eszközök nem rendelkeznek közvetlen hozzáféréssel a felhőhöz. A felhőalapú szolgáltatásokhoz való kapcsolódást megkísérlő modulok sikertelenek lesznek. Az API-proxy modul támogatja az alsóbb rétegbeli IoT Edge eszközöket ebben a konfigurációban azáltal, hogy átirányítja a modul-kapcsolatokat az átjáró-hierarchia rétegeinek átirányításához. Biztonságos megoldást biztosít az ügyfelek számára, hogy több szolgáltatással kommunikáljanak a HTTPS protokollon keresztül, bújtatás nélkül, de a kapcsolatok leállításával az egyes rétegeken. Az API-proxy modul egy átjáró-hierarchiában lévő IoT Edge-eszközök közötti proxy-modulként funkcionál, amíg el nem éri a felső rétegben lévő IoT Edge-eszközt. Ezen a ponton a legfelső rétegű IoT Edge eszközön futó szolgáltatások kezelik ezeket a kéréseket, és az API-proxy modul a helyi szolgáltatásokból a felhőbe irányuló összes HTTP-forgalmat egyetlen porton keresztül végzi el.

Az API-proxy modul számos forgatókönyvet engedélyezhet az átjáró-hierarchiák számára, például lehetővé teszi, hogy az alsóbb rétegbeli eszközök lekérjenek tároló-lemezképeket vagy leküldéses blobokat a tárolóba A proxybeállítások beállítása határozza meg az adattovábbítás módját. Ez a cikk számos gyakori konfigurációs beállítást ismertet.

## <a name="deploy-the-proxy-module"></a>A proxy modul üzembe helyezése

Az API-proxy modul a Microsoft Container Registry (MCR) használatával érhető el: `mcr.microsoft.com/azureiotedge-api-proxy:latest` .

Az API-proxy modult közvetlenül az Azure Marketplace-ről is üzembe helyezheti: [IOT Edge API proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>A proxy modul ismertetése

Az API-proxy modul egy Nginx fordított proxyt használ az adattovábbításhoz a hálózati rétegeken keresztül. Egy proxy be van ágyazva a modulba, ami azt jelenti, hogy a modul rendszerképének támogatnia kell a proxy konfigurációját. Ha például a proxy egy bizonyos portot figyel, akkor a modulnak meg kell nyitnia a portot.

A proxy a modulba ágyazott alapértelmezett konfigurációs fájllal kezdődik. Az új konfigurációt átadhatja a felhőben lévő modulnak a [modul Twin](../iot-hub/iot-hub-devguide-module-twins.md)használatával. Emellett környezeti változók használatával be-és kikapcsolhatja a konfigurációs beállításokat a központi telepítés idején.

Ez a cikk először az alapértelmezett konfigurációs fájlra koncentrál, és a környezeti változók használatával engedélyezi a beállításait. Ezután megbeszéljük a konfigurációs fájl végének testreszabását.

### <a name="default-configuration"></a>Alapértelmezett konfiguráció

Az API-proxy modul egy alapértelmezett konfigurációt tartalmaz, amely támogatja a gyakori forgatókönyveket, és lehetővé teszi a testreszabást. Az alapértelmezett konfigurációt a modul környezeti változói segítségével szabályozhatja.

Az alapértelmezett környezeti változók jelenleg a következők:

| Környezeti változó | Leírás |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Sorolja fel az összes frissítendő változót egy vesszővel elválasztott listában. Ez a lépés megakadályozza a helytelen konfigurációs beállítások véletlen módosítását.
| `NGINX_DEFAULT_PORT` | Megváltoztatja az Nginx-proxy által figyelt portot. Ha frissíti ezt a környezeti változót, győződjön meg arról, hogy a kiválasztott port a modul Docker is elérhető, és port Kötésként van deklarálva az üzembe helyezési jegyzékben.<br><br>Az alapértelmezett érték a 443.<br><br>Az Azure Marketplace-en való üzembe helyezéskor az alapértelmezett portot 8000-re frissíti a rendszer az edgeHub modullal való ütközések elkerülése érdekében. További információ: a [nyitott portok csökkentése](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | A Docker-kérelmek továbbítására szolgáló címek. Módosítsa ezt a változót a felső rétegbeli eszközön úgy, hogy a beállításjegyzék-modulra mutasson.<br><br>Az alapértelmezett érték a szülő állomásnév. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | A blob-beállításjegyzék kéréseinek továbbítására szolgáló címe. Módosítsa ezt a változót a felső rétegbeli eszközön úgy, hogy a blob Storage modulra mutasson.<br><br>Az alapértelmezett érték a szülő állomásnév. |

## <a name="minimize-open-ports"></a>A nyitott portok csökkentése

A nyitott portok számának minimalizálásához az API-proxy modulnak továbbítania kell az összes HTTPS-forgalmat (443-es port), beleértve a edgeHub modult célzó forgalmat is. Az API-proxy modul alapértelmezés szerint úgy van konfigurálva, hogy átirányítsa az összes edgeHub-forgalmat az 443-es porton.

A következő lépésekkel konfigurálhatja a központi telepítést a nyitott portok minimalizálásához:

1. Frissítse a edgeHub modul beállításait, hogy ne kelljen kötést kötni a 443-es porton, ellenkező esetben a port kötési ütközések lesznek. Alapértelmezés szerint a edgeHub modul a 443, 5671 és 8883 portokhoz kötődik. Törölje a 443-es port kötését, és hagyja a másik két helyet:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Konfigurálja az API-proxy modult a 443-es porton való kötéshez.

   1. A **NGINX_DEFAULT_PORT** környezeti változó értékének beállítása a következőre: `443` .
   1. Frissítse a tároló létrehozási beállításait a 443-es porton való kötéshez.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Ha nincs szükség a nyitott portok minimalizálására, a edgeHub modul a 443-as portot használja, és az API-proxy modult úgy konfigurálja, hogy egy másik portot figyelje. Az API-proxy modul például az 8000-as porton figyelheti az **NGINX_DEFAULT_PORT** környezeti változó értékét, `8000` és létrehoz egy port kötést az 8000-es porthoz.

## <a name="enable-container-image-download"></a>Tároló rendszerképének letöltésének engedélyezése

Az API-proxy modul általános használati esete, ha lehetővé szeretné tenni, hogy a tárolók lemezképeit az alsóbb rétegekben lévő IoT Edge eszközök lehívhatják. Ez a forgatókönyv a [Docker beállításjegyzék-modullal](https://hub.docker.com/_/registry) kéri le a tároló lemezképeit a felhőből, és gyorsítótárazza azokat a felső rétegben. Az API-proxy továbbítja az összes HTTPS-kérést, hogy az alsó rétegből töltsön le egy tároló-rendszerképet, amelyet a felső réteg beállításjegyzék-modulja fog kiszolgálni.

Ehhez a forgatókönyvhöz az alsóbb rétegbeli IoT Edge-eszközöknek a tartomány nevére kell mutatniuk, `$upstream` majd az API-proxy modul portszámát a rendszerkép tároló-beállításjegyzéke helyett. Példa: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Ez a használati eset az oktatóanyagban az [átjárók használatával IoT Edge-eszközök hierarchiájának létrehozása](tutorial-nested-iot-edge.md)című cikkben látható.

Konfigurálja a következő modulokat a **felső rétegben**:

* Docker beállításjegyzék-modul
  * Konfigurálja a modult egy emlékezetes névvel, például a *beállításjegyzéktel* , és tegyen elérhetővé egy portot a modulban a kérések fogadásához.
  * Konfigurálja a modult a tároló-beállításjegyzékhez való leképezéshez.
* API-proxy modul
  * Konfigurálja az alábbi környezeti változókat:

    | Name | Érték |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | A beállításjegyzék-modul neve és a port megnyitása. Például: `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | Az a port, amelyet az Nginx-proxy figyel az alsóbb rétegbeli eszközök kéréseire. Például: `8000`. |

  * Konfigurálja a következő createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Konfigurálja a következő modult bármely **alsóbb rétegen** ehhez a forgatókönyvhöz:

* API-proxy modul
  * Konfigurálja az alábbi környezeti változókat:

    | Name | Érték |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Az a port, amelyet az Nginx-proxy figyel az alsóbb rétegbeli eszközök kéréseire. Például: `8000`. |

  * Konfigurálja a következő createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>BLOB feltöltésének engedélyezése

Az API-proxy modul egy másik felhasználási esete az, hogy az alacsonyabb rétegekben lévő IoT Edge eszközök feltöltsék a blobokat. Ez a használati eset lehetővé teszi az alsóbb rétegbeli eszközök hibaelhárítási funkcióit, például a modul-naplók feltöltését vagy a támogatási csomag feltöltését.

Ez a forgatókönyv az [Azure Blob Storage IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) modul felső rétegében a Blobok létrehozásának és feltöltésének kezelésére használja.

Konfigurálja a következő modulokat a **felső rétegben**:

* Egy Azure-Blob Storage IoT Edge modulban.
* API-proxy modul
  * Konfigurálja az alábbi környezeti változókat:

    | Name | Érték |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | A blob Storage-modul neve és a port megnyitása. Például: `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | Az a port, amelyet az Nginx-proxy figyel az alsóbb rétegbeli eszközök kéréseire. Például: `8000`. |

  * Konfigurálja a következő createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Konfigurálja a következő modult bármely **alsóbb rétegen** ehhez a forgatókönyvhöz:

* API-proxy modul
  * Konfigurálja az alábbi környezeti változókat:

    | Name | Érték |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Az a port, amelyet az Nginx-proxy figyel az alsóbb rétegbeli eszközök kéréseire. Például: `8000`. |

  * Konfigurálja a következő createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

A következő lépésekkel feltöltheti a támogatási csomagot vagy a naplófájlt a felső rétegben található blob Storage-modulba:

1. Hozzon létre egy BLOB-tárolót Azure Storage Explorer vagy a REST API-k használatával. További információkért lásd: [adatok tárolása az Azure Blob Storage on IoT Edgeon](how-to-store-data-blob.md).
1. A [naplók Beolvasása IoT Edge központi telepítésekben](how-to-retrieve-iot-edge-logs.md)szereplő lépések alapján jelentkezzen be a naplóba vagy a támogatási kötegbe, de használja a tartománynevet `$upstream` és a nyílt proxy portot a blob Storage-modul címe helyett. Például:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>A proxy konfigurációjának szerkesztése

Az alapértelmezett konfigurációs fájl be van ágyazva az API-proxy modulba, de új konfigurációt adhat át a modulnak a felhőn keresztül a Twin modul használatával.

A saját konfigurációjának írásakor továbbra is használhatja a környezetet a beállítások telepítésre történő beállításához. Használja a következő szintaxist:

* `${MY_ENVIRONMENT_VARIABLE}`Egy környezeti változó értékének beolvasására használható.
* A beállítások be-és kikapcsolása feltételes utasításokkal a környezeti változók értéke alapján:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Amikor az API-proxy modul elemzi a proxy konfigurációját, az először a-ben felsorolt összes környezeti változót lecseréli a `PROXY_CONFIG_ENV_VAR_LIST` helyettesítéssel megadott értékekre. Ezt követően a és a `#if_tag` pár közötti mindent `#endif_tag` lecseréli a rendszer. A modul ezután az értelmezett konfigurációt az Nginx fordított proxyhoz adja.

A proxy konfigurációjának dinamikus frissítéséhez kövesse az alábbi lépéseket:

1. Írja be a konfigurációs fájlt. Ezt az alapértelmezett sablont használhatja hivatkozásként: [nginx_default_config. conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Másolja a konfigurációs fájl szövegét, és alakítsa át Base64-re.
1. Illessze be a kódolt konfigurációs fájlt a `proxy_config` különálló modul kívánt tulajdonságának értékeként.

   ![Kódolt konfigurációs fájl beillesztése proxy_config tulajdonság értékeként](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Következő lépések

[Egy alsóbb rétegbeli IoT Edge eszköz Azure IoT Edge átjáróhoz való csatlakoztatásához](how-to-connect-downstream-iot-edge-device.md)használja az API-proxy modult.