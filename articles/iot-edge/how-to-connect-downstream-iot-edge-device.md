---
title: Lefelé irányuló IoT Edge csatlakoztatása – Azure IoT Edge | Microsoft Docs
description: Egy átjáróeszköz konfigurálása IoT Edge átjáróeszközökhöz való Azure IoT Edge való csatlakozáshoz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 500833d1bb4fc492942c08239bd488c2d2c16d30
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484319"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>Lefelé irányuló eszköz IoT Edge csatlakoztatása Azure IoT Edge átjáróhoz

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Ez a cikk útmutatást nyújt a megbízható kapcsolat létesítéhez egy IoT Edge átjáró és egy lefelé irányuló IoT Edge között.

Átjáróforgatókönyvben a IoT Edge egy átjáró és egy lefelé irányuló eszköz is lehet. Több IoT Edge is rétegezhet az eszközök hierarchiájának létrehozásához. Az alhálózati (vagy gyermek) eszközök hitelesíthetők és üzeneteket küldhetnek vagy fogadhatnak az átjáróeszközükön (vagy szülőeszközükön) keresztül.

Az átjáróhierarchiában a IoT Edge két különböző konfigurációja van, és ez a cikk mindkettőre vonatkozik. Az első az **eszköz** felső IoT Edge rétege. Ha több IoT Edge eszköz csatlakozik egymással, minden olyan eszköz, amely nem rendelkezik szülőeszközvel, de közvetlenül csatlakozik IoT Hub a felső réteghez. Ez az eszköz felelős az alatta lévő összes eszköztől származó kérések kezeléséért. A másik konfiguráció minden olyan IoT Edge eszközre vonatkozik, amely a **hierarchia** alsó rétegében található. Ezek az eszközök lehetnek más lefelé irányuló IoT- és IoT Edge-eszközök átjárói, de a kommunikációt a saját szülőeszközökön keresztül is át kell irányítanuk.

Egyes hálózati architektúrák megkövetelik, hogy csak a IoT Edge legfelső szintű eszköz kapcsolódhat a felhőhöz. Ebben a konfigurációban a IoT Edge alsóbb rétegeiben lévő összes eszköz csak az átjáróeszközével (vagy szülőeszközével) és az alsóbb rétegbeli (vagy gyermek) eszközökkel tud kommunikálni.

A cikkben található összes lépés a Configure [an IoT Edge device to](how-to-create-transparent-gateway.md)act as a transparent gateway (A IoT Edge-eszköz konfigurálása transzparens átjáróként) cikkben található lépésekre épül, amely egy IoT Edge-eszközt állít be átjáróként az lefelé irányuló IoT-eszközök számára. Ugyanezek az alapvető lépések vonatkoznak az összes átjáró-forgatókönyvre:

* **Hitelesítés:** Hozzon IoT Hub identitásokat az átjáróhierarchiában lévő összes eszköz számára.
* **Engedélyezés:** Állítsa be a szülő-gyermek kapcsolatot a IoT Hub, hogy úgy engedélyezze a gyermekeszközöknek a szülőeszközükhöz való csatlakozást, mintha a szülőeszközhöz IoT Hub.
* **Átjárófelderítés:** Győződjön meg arról, hogy a gyermekeszköz megtalálja a szülőeszközét a helyi hálózaton.
* **Biztonságos kapcsolat:** Biztonságos kapcsolat létrehozása megbízható tanúsítványokkal, amelyek ugyanannak a láncnak a részei.

## <a name="prerequisites"></a>Előfeltételek

* Ingyenes vagy standard IoT Hub.
* Legalább két **IoT Edge eszköz,** az egyik a legfelső rétegben lévő eszköz, egy vagy több alsó rétegben lévő eszköz. Ha nem állnak rendelkezésre IoT Edge eszközök, futtathatja a Azure IoT Edge Ubuntu virtuális [gépeken.](how-to-install-iot-edge-ubuntuvm.md)
* Ha az Azure CLI-t használja az eszközök létrehozásához és kezeléséhez, az Azure CLI 2.3.1-es vagy újabb verzióval rendelkezik az Azure IoT-bővítmény 0.10.6-os vagy újabb verzióval.

Ez a cikk részletes lépéseket és lehetőségeket tartalmaz a forgatókönyvnek megfelelő átjáróhierarchia létrehozásához. Irányított oktatóanyag: Új eszközök [hierarchiájának létrehozása IoT Edge átjárók használatával.](tutorial-nested-iot-edge.md)

## <a name="create-a-gateway-hierarchy"></a>Átjáróhierarchia létrehozása

Az átjáró-IoT Edge létrehozásához szülő-gyermek kapcsolatokat kell definiálni a forgatókönyvben IoT Edge eszközökhöz. Új eszközidentitás létrehozásakor beállíthatja a szülőeszközt, vagy kezelheti egy meglévő eszközidentitás szülő- és gyermekét.

A szülő-gyermek kapcsolatok beállításának lépése úgy engedélyezi a gyermekeszközöknek a szülőeszközükhöz való csatlakozást, mint a IoT Hub.

Csak IoT Edge eszközök szülőeszközök, de a IoT Edge és az IoT-eszközök is lehettek gyermekek. Egy szülőnek sok gyermeke lehet, de egy gyermeknek csak egy szülője lehet. Az átjáróhierarchia szülő/gyermek készletek láncolásával jön létre, hogy az egyik eszköz gyermeke egy másik eszköz szülője.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal kezelheti a szülő-gyermek kapcsolatot új eszköz-identitások létrehozásakor vagy meglévő eszközök szerkesztésével.

Amikor létrehoz egy új IoT Edge, lehetősége van szülő- és gyermekeszközöket választani a központ meglévő IoT Edge eszközeinek listájából.

1. A [Azure Portal](https://portal.azure.com)lépjen az IoT Hubra.
1. Válassza **IoT Edge** a navigációs menüből a Kívánt elemet.
1. Válassza **a IoT Edge hozzáadása lehetőséget.**
1. Az eszközazonosító és a hitelesítési beállítások megadása mellett a Szülőeszköz beállítása vagy **a** Gyermekeszközök kiválasztása lehetőséget **is használhatja.**
1. Válassza ki a szülőként vagy gyermekként kívánt eszközt vagy eszközöket.

Meglévő eszközökhöz szülő-gyermek kapcsolatokat is létrehozhat vagy kezelhet.

1. A [Azure Portal](https://portal.azure.com)lépjen az IoT Hubra.
1. Válassza **IoT Edge** a navigációs menüből a Kívánt elemet.
1. Válassza ki a felügyelni kívánt eszközt a IoT Edge **listájából.**
1. Válassza **a Szülőeszköz beállítása vagy a** **Gyermekeszközök kezelése lehetőséget.**
1. Hozzáadhat vagy eltávolíthat szülő- vagy gyermekeszközöket.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI [azure-iot](/cli/azure/iot) bővítménye parancsokat biztosít az IoT-erőforrások kezeléséhez. Az IoT- és a IoT Edge-eszközök szülő-gyermek kapcsolatát új eszköz identitások létrehozásakor vagy meglévő eszközök szerkesztésével kezelheti.

Az [az iot hub device-identity](/cli/azure/iot/hub/device-identity) parancskészlet lehetővé teszi az adott eszköz szülő-gyermek kapcsolatainak kezelését.

A `create` parancs paramétereket tartalmaz a gyermekeszközök hozzáadásához és a szülőeszköz beállításához az eszköz létrehozásakor.

A további eszközidentitási parancsok, például a , a és a és a , lehetővé teszik a meglévő eszközök `add-children` `list-children` `remove-children` `get-parent` `set-parent` szülő-gyermek kapcsolatainak kezelését.

---

## <a name="prepare-certificates"></a>Tanúsítványok előkészítése

Egy egységes tanúsítványláncot kell telepíteni az azonos átjáróhierarchiában lévő eszközökre, hogy biztonságos kommunikációt létesítsen közöttük. A hierarchiában lévő összes eszköznek , akár IoT Edge, akár IoT levéleszközről van szó, ugyanazon legfelső szintű hitelesítésszolgáltatói tanúsítvány másolatára van szüksége. Ezután IoT Edge hierarchiában lévő összes eszköz ezt a legfelső szintű hitelesítésszolgáltatói tanúsítványt használja az eszköz hitelesítésszolgáltatói tanúsítványának legfelső szintű tanúsítványaként.

Ezzel a beállítással minden alsóbb rétegbeli IoT Edge-eszköz vagy IoT levéleszköz ellenőrizheti a szülője identitását annak ellenőrzésével, hogy az edgeHub, amelyhez csatlakozik, rendelkezik-e a megosztott legfelső szintű hitelesítésszolgáltatói tanúsítvány által aláírt kiszolgálótanúsítvánnyal.

<!-- TODO: certificate graphic -->

Hozza létre a következő tanúsítványokat:

* Egy **legfelső szintű hitelesítésszolgáltatói** tanúsítvány, amely az adott átjáróhierarchiában lévő összes eszköz legfelső szintű megosztott tanúsítványa. Ez a tanúsítvány minden eszközön telepítve van.
* A **főtanúsítvány-láncba** foglalni kívánt köztes tanúsítványok.
* Egy **eszköz hitelesítésszolgáltatói** tanúsítványa és titkos **kulcsa,** amelyet a fő- és köztes tanúsítványok hoznak létre. Az átjáróhierarchiában minden egyes IoT Edge egyedi eszköz-hitelesítésszolgáltatói tanúsítványra van szükség.

Használhat önaírt hitelesítésszolgáltatót, vagy vásárolhat egyet egy megbízható kereskedelmi hitelesítésszolgáltatótól( például Baltimore, Verisign, Digicert vagy GlobalSign).

Ha nem rendelkezik saját tanúsítványokkal, létrehozhat bemutatótanúsítványokat az [eszköz IoT Edge teszteléséhez.](how-to-create-test-certificates.md) A cikkben található lépéseket követve hozzon létre egy fő- és köztes tanúsítványkészletet, majd hozzon létre IoT Edge összes eszköz hitelesítésszolgáltatói tanúsítványait.

## <a name="configure-iot-edge-on-devices"></a>Eszközök IoT Edge konfigurálása

Az átjáróként való IoT Edge lépései nagyon hasonlóak az IoT Edge eszközként való beállításának lépéseihez.

Az átjárófelderítés engedélyezéséhez minden IoT Edge átjáróeszközt olyan állomásnévvel kell konfigurálni, amely alapján **a** gyermekeszköze megkeresi azt a helyi hálózaton. Minden lefelé irányuló IoT Edge eszközt konfigurálni kell  egy olyan parent_hostname, amelyhez csatlakozni tud. Ha egyetlen IoT Edge eszköz szülő- és gyermekeszköz is, akkor mindkét paraméterre szüksége lesz.

A biztonságos kapcsolatok engedélyezéséhez az átjáró forgatókönyvében minden IoT Edge-eszközt egyedi eszköz-hitelesítésszolgáltatói tanúsítvánnyal és az átjáróhierarchiában lévő összes eszköz által megosztott főtanúsítvánnyal kell konfigurálni.

Az eszközön már IoT Edge kell lennie. Ha nem, kövesse [az IoT Edge-eszköz](how-to-register-device.md) regisztrálása a IoT Hub, majd az Install the Azure IoT Edge runtime (Az Azure IoT Edge [telepítése) lépéseit.](how-to-install-iot-edge.md)

Az ebben a szakaszban található lépések  a cikk korábbi részében tárgyalt legfelső szintű **hitelesítésszolgáltatói** tanúsítványra, eszköz-hitelesítésszolgáltatói tanúsítványra és titkos kulcsra hivatkoznak. Ha ezeket a tanúsítványokat egy másik eszközön hozta létre, akkor elérhetővé kell tenni őket ezen az eszközön. A fájlokat fizikai átvitelre is használhatja, például USB-meghajtóval, egy olyan szolgáltatással, mint a [Azure Key Vault,](../key-vault/general/overview.md)vagy egy olyan funkcióval, mint a [Biztonságos fájlmásolás.](https://www.ssh.com/ssh/scp/)

A következő lépésekkel konfigurálhatja IoT Edge eszközén.

Győződjön meg arról, hogy az **iotedge** felhasználó olvasási engedélyekkel rendelkezik a tanúsítványokat és kulcsokat tartó címtárhoz.

1. Telepítse a **legfelső szintű hitelesítésszolgáltatói tanúsítványt** ezen a IoT Edge eszközön.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. Frissítse a tanúsítványtárolót.

   ```bash
   sudo update-ca-certificates
   ```

   Ennek a parancsnak azt kell megjelenítenie, hogy egy tanúsítvány hozzá lett adva az /etc/ssl/certs parancshoz.

1. Nyissa meg az IoT Edge konfigurációs fájlját.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Ha a konfigurációs fájl még nem létezik az eszközön, a sablon `/etc/aziot/config.toml.edge.template` használatával hozzon létre egyet.

1. Keresse meg **az Állomásnév szakaszt** a konfigurációs fájlban. A paramétert tartalmazó sornál ne legyen felváltva, és frissítse az értéket úgy, hogy az legyen a teljes tartománynév (FQDN) vagy az IoT Edge `hostname` IP-címe.

   Ennek a paraméternek az értéke az, amit a lefelé irányuló eszközök az átjáróhoz való csatlakozáshoz fognak használni. Az állomásnév alapértelmezés szerint a gép nevét veszi fel, de a teljes tartománynév vagy az IP-cím szükséges az lefelé irányuló eszközök csatlakoztatásához.

   Használjon 64 karakternél rövidebb állomásnevet, amely a kiszolgálói tanúsítvány köznevének karakterkorlátja.

   Az átjáróhierarchiában konzisztensnek kell lennie az állomásnév mintával. FQDN-eket vagy IP-címeket használjon, de mindkettőt ne.

1. *Ha az eszköz gyermekeszköz,* keresse meg a **Szülő állomásnév szakaszt.** A nem kívánt érték megadása után frissítse a paramétert a szülőeszköz teljes tartománynevére vagy IP-címére, a szülőeszköz konfigurációs fájljában megadott gazdanévnek `parent_hostname` megfelelő értékkel.

1. Keresse meg **a Megbízhatósági csomag tanúsítványa szakaszt.** A paramétert pedig frissítse az eszköz legfelső szintű hitelesítésszolgáltatói tanúsítványának `trust_bundle_cert` URI-azonosítóját használva.

1. Ellenőrizze, IoT Edge az eszköz a IoT Edge megfelelő verzióját használja-e az indításakor.

   Keresse meg **a Default Edge Agent** szakaszt, és ellenőrizze, hogy a rendszerkép értéke az 1.2-es IoT Edge-e. Ha nem, frissítse:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. Keresse meg **a peremhálózati hitelesítésszolgáltató tanúsítványa** szakaszt a konfigurációs fájlban. A szakasz sorait ne adja meg, és adja meg a tanúsítvány és a kulcsfájlok elérési útját a IoT Edge eszközön.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Mentse ( ) és zárja be ( ) a `Ctrl+O` `Ctrl+X` konfigurációs fájlt.

1. Ha korábban már használt más tanúsítványokat a IoT Edge törölje a következő két könyvtárban található fájlokat, hogy az új tanúsítványok biztosan alkalmazva legyen:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Válassza az Alkalmaz gombot a módosítások alkalmazásához.

   ```bash
   sudo iotedge config apply
   ```

1. Ellenőrizze, hogy vannak-e hibák a konfigurációban.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >A IoT Edge ellenőrzőeszköz egy tárolót használ a diagnosztikai ellenőrzés végrehajtásához. Ha ezt az eszközt lefelé irányuló vagy IoT Edge szeretné használni, győződjön meg arról, hogy hozzáférnek az eszközhöz, vagy hogy a tároló rendszerképe elérhető a `mcr.microsoft.com/azureiotedge-diagnostics:latest` privát tárolójegyzékben.

## <a name="network-isolate-downstream-devices"></a>Lefelé irányuló eszközök hálózati elkülönítése

A cikkben eddig található lépések átjáróként IoT Edge eszközként vagy lefelé irányuló eszközként konfigurálták az eszközöket, és megbízható kapcsolatot hoznak létre közöttük. Az átjáróeszköz kezeli az lefelé irányuló eszköz és a IoT Hub közötti interakciókat, beleértve a hitelesítést és az üzenetek útválasztását. Az lefelé irányuló IoT Edge üzembe helyezett modulok továbbra is létrehozhatnak saját kapcsolatokat a felhőszolgáltatásokkal.

Egyes hálózati architektúrák, például az ISA-95 szabványnak megfelelők, igyekeznek minimalizálni az internetkapcsolatok számát. Ilyen esetekben közvetlen internetkapcsolat nélkül konfigurálhat lefelé irányuló IoT Edge eszközöket. Az IoT Hub kommunikáció átjáróeszközön keresztüli útválasztásán túl az IoT Edge-eszközök az átjáróeszközre támaszkodhatnak az összes felhőbeli kapcsolathoz.

Ez a hálózati konfiguráció megköveteli, hogy csak IoT Edge átjáróhierarchia felső rétegében lévő IoT Edge létesítsen közvetlen kapcsolatot a felhővel. IoT Edge rétegben lévő összes eszköz csak a szülőeszközével vagy gyermekeszközével kommunikálhat. Az átjáróeszközökön elérhető speciális modulok lehetővé teszik ezt a forgatókönyvet, beleértve a következőket:

* Az **API-proxymodulra** minden olyan átjárón szükség van IoT Edge amely alatt egy másik IoT Edge található. Ez azt jelenti, hogy az *átjáróhierarchia* minden rétegén lennie kell, kivéve az alsó réteget. Ez a modul [egy nginx fordított](https://nginx.org) proxyt használ a HTTP-adatok hálózati rétegeken keresztüli egyetlen porton való útválasztásához. Nagy mértékben konfigurálható az ikermodul és a környezeti változók segítségével, így az átjáró forgatókönyvének követelményeihez igazítható.

* A **Docker beállításjegyzék-modul** az átjáróhierarchia  IoT Edge felső rétegében található átjáró átjárón helyezhető üzembe. Ez a modul felelős a tároló-rendszerképek leolvasásáért és gyorsítótárazásáért az összes IoT Edge rétegben található eszköz nevében. A modul felső rétegben való üzembe helyezésének alternatívája egy helyi regisztrációs adatbázis használata, vagy a tároló rendszerképének manuális betöltése az eszközökre, és a modul le pull-szabályzatának **beállítása soha.**

* A **Azure Blob Storage IoT Edge** az átjáróhierarchia felső IoT Edge átjárón helyezhetők üzembe.  Ez a modul felelős a blobok feltöltéséért az összes IoT Edge rétegben lévő eszköz nevében. A blobok feltöltésének lehetősége emellett hasznos hibaelhárítási funkciókat is IoT Edge, például a modulnapló feltöltését és a csomagfeltöltés támogatását.

### <a name="network-configuration"></a>Hálózati konfiguráció

A felső rétegben lévő összes átjáróeszközhöz a hálózati operátorok a következőt kell:

* Adjon meg egy statikus IP-címet vagy teljes tartománynevet (FQDN).
* Engedélyezze az erről az IP-címről kimenő kommunikációt a Azure IoT Hub állomásnévre a 443-as (HTTPS) és az 5671-es (AMQP) porton keresztül.
* Engedélyezze a kimenő kommunikációt erről az IP-címről a Azure Container Registry állomásnévvel a 443-as (HTTPS) porton keresztül.

  Az API-proxymodul egyszerre csak egy tároló-beállításjegyzék kapcsolatait tudja kezelni. Javasoljuk, hogy a privát tárolójegyzékben tárolja az összes tároló rendszerképét, beleértve a Microsoft Container Registry (mcr.microsoft.com) által biztosított nyilvános rendszerképeket is.

Az alsóbb rétegben található átjáróeszközökhöz a hálózati operátorok a következőt kell:

* Adjon meg egy statikus IP-címet.
* Engedélyezze az erről az IP-címről a szülőátjáró IP-címére irányuló kimenő kommunikációt a 443-as (HTTPS) és az 5671-es (AMQP) porton keresztül.

### <a name="deploy-modules-to-top-layer-devices"></a>Modulok üzembe helyezése a felső réteg eszközein

Az IoT Edge-hierarchia felső rétegében található IoT Edge szükséges modulokat az eszközön futtatott számítási feladatokhoz szükséges modulok mellett telepíteni kell rá.

Az API-proxymodul úgy lett kialakítva, hogy a leggyakoribb átjáróforgatókönyvek kezeléséhez legyen testre szabva. Ez a cikk a modulok alapszintű konfigurációban való beállítását és egy példát mutat be. Részletesebb információkért és példákért tekintse meg a Configure [the API proxy module for your gateway hierarchy scenario (AZ API-proxy](how-to-configure-api-proxy-module.md) konfigurálása az átjáróhierarchia forgatókönyvéhez) modult.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)lépjen az IoT Hubra.
1. Válassza **IoT Edge** a navigációs menüből a Kívánt elemet.
1. Válassza ki a konfigurálni kívánt felső rétegben lévő eszközt a IoT Edge **listájából.**
1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.
1. A IoT Edge **modulok szakaszban** válassza a **Hozzáadás, majd** a **Marketplace-modul lehetőséget.**
1. Keresse meg és válassza ki a **IoT Edge API-proxy modult.**
1. Válassza ki az API-proxymodul nevét az üzembe helyezett modulok listájából, és frissítse a következő modulbeállításokat:
   1. A Környezeti **változók lapon** frissítse a változó értékét **NGINX_DEFAULT_PORT** `443` értékre.
   1. A Tároló **létrehozása beállítások lapon** frissítse a portkötéseket a 443-as portra való hivatkozáshoz.

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

   Ezek a módosítások úgy konfigurálják az API-proxymodult, hogy a 443-as portot figyelje. A portkötések ütközésének elkerülése érdekében úgy kell konfigurálnia az edgeHub-modult, hogy ne figyelje a 443-as portot. Ehelyett az API-proxymodul a 443-as porton keresztül irányít minden edgeHub-forgalmat.

1. Válassza **a Futásidejű beállítások lehetőséget,** és keresse meg az edgeHub-modul létrehozási beállításait. Törölje a 443-as port portkötését, és hagyja meg az 5671-es és a 8883-as port kötését.

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

1. A **futásidejű** beállítások módosításainak mentéséhez válassza a Mentés lehetőséget.
1. Válassza **ismét a Hozzáadás** lehetőséget, majd IoT Edge **modult.**
1. Adja meg a következő értékeket, hogy hozzáadja a Docker beállításjegyzék-modult az üzemelő példányhoz:
   1. **IoT Edge modul neve:**`registry`
   1. A **Modulbeállítások lapon,** a **Rendszerkép URI-ját:**`registry:latest`
   1. A Környezeti **változók lapon** adja hozzá a következő környezeti változókat:

      * **Név:** Érték: Annak a tárolójegyzéknek az `REGISTRY_PROXY_REMOTEURL` URL-címe, amelybe a beállításjegyzék-modult leképezni szeretné. Például: `https://myregistry.azurecr`.

        A beállításjegyzék-modul csak egy tároló-beállításjegyzékhez tud leképezni, ezért javasoljuk, hogy az összes tárolólemezképet egyetlen privát tárolójegyzékben tárolja.

      * **Név:** `REGISTRY_PROXY_USERNAME` **Érték:** Felhasználónév a tároló-beállításjegyzékben való hitelesítéshez.

      * **Név:** `REGISTRY_PROXY_PASSWORD` **Érték:** A tároló-beállításjegyzékben való hitelesítéshez használt jelszó.

   1. A Tároló **létrehozása beállítások lapon illessze** be a következőt:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. Válassza **a Hozzáadás** lehetőséget a modul üzembe helyezéshez való hozzáadásához.
1. Válassza **a Tovább: Útvonalak lehetőséget** a következő lépéshez.
1. Ha engedélyezni szeretné, hogy az eszközről a felhőbe irányuló üzenetek a lefelé irányuló eszközökről IoT Hub, olyan útvonalat kell tartalmaznia, amely az összes üzenetet továbbítja a IoT Hub. Például:
    1. **Név:**`Route`
    1. **Érték:**`FROM /messages/* INTO $upstream`
1. Az **utolsó lépéshez válassza** az Áttekintés + létrehozás lehetőséget.
1. Válassza **a Létrehozás lehetőséget** az eszköz üzembe helyezéséhez.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A [Azure Cloud Shell](https://shell.azure.com/)hozzon létre egy üzembe helyezési JSON-fájlt. Például:

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

   Ez az üzembe helyezési fájl úgy konfigurálja az API-proxymodult, hogy a 443-as portot figyelje. A portkötések ütközésének elkerülése érdekében a fájl úgy konfigurálja az edgeHub-modult, hogy ne figyelje a 443-as portot. Ehelyett az API-proxymodul a 443-as porton keresztül irányít minden edgeHub-forgalmat.

1. A következő paranccsal hozzon létre üzembe helyezést egy IoT Edge eszközön:

   ```bash
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>Modulok üzembe helyezése alsóbb rétegű eszközökön

IoT Edge átjáróhierarchia alsó rétegeiben lévő összes eszközhöz egy szükséges modul szükséges, amely az eszközön futtatott számítási feladatokhoz használható modulok mellett telepítve lesz.

#### <a name="route-container-image-pulls"></a>Tároló rendszerkép-lekért útvonalának útválasztása

Mielőtt az átjáróhierarchiákban lévő IoT Edge eszközökhöz szükséges proxymodult tárgyalja, fontos megérteni, hogy az alsóbb rétegben IoT Edge eszközök hogyan kapják meg a modul rendszerképeit.

Ha az alsó rétegbeli eszközök nem tudnak csatlakozni a felhőhöz, de azt szeretné, hogy a szokásos módon kérik le a modul rendszerképeit, az átjáróhierarchia felső rétegbeli eszközét úgy kell konfigurálni, hogy kezelje ezeket a kéréseket. A felső rétegbeli eszköznek  futtatnia kell egy Docker beállításjegyzék-modult, amely le van képezve a tárolójegyzékre. Ezután konfigurálja az API-proxymodult, hogy a tárolókéréseket a modulhoz irányítsa. Ezeket a részleteket a cikk korábbi szakaszai ismertetik. Ebben a konfigurációban az alsó rétegbeli eszközök nem a felhőbeli tárolóregisztrációs adatbázisra, hanem a felső rétegben futó beállításjegyzékre mutatnak.

A hívása helyett például az `mcr.microsoft.com/azureiotedge-api-proxy:1.0` alsó rétegben található eszközöknek a hívását kell használniuk. `$upstream:443/azureiotedge-api-proxy:1.0`

A **$upstream** paraméter egy alsóbb rétegbeli eszköz szülőszámára mutat, így a kérés az összes réteget át fogja irányítva, amíg el nem éri a felső réteget, amely egy proxykörnyezet a tárolókéréseket a beállításjegyzék-modulhoz irányítja. Ebben a példában a portot arra a portra kell cserélni, amelyet a szülőeszköz `:443` API-proxymodulja figyel.

Az API-proxymodul csak egy beállításjegyzék-modulhoz irányítható, és minden beállításjegyzék-modul csak egy tároló-beállításjegyzékhez tud leképezni. Ezért az alsóbb rétegbeli eszközök által lekért rendszerképeket egyetlen tárolójegyzékben kell tárolni.

Ha nem szeretné, hogy az alsóbb rétegbeli eszközök modul lekéréses kérelmeket használjanak egy átjáróhierarchián keresztül, egy másik lehetőség egy helyi beállításjegyzék-megoldás kezelése. Vagy az üzembe helyezések létrehozása előtt az eszközökre is lekuláljuk a modul rendszerképeit, majd az **imagePullPolicy vezérlőt** állítsa **never (soha) beállításra.**

#### <a name="bootstrap-the-iot-edge-agent"></a>A IoT Edge rendszerindítása

A IoT Edge ügynök az első olyan futásidejű összetevő, amely bármely IoT Edge indul. Meg kell győződni arról, hogy az alsóbb rétegbeli IoT Edge-eszközök hozzáférhetnek az edgeAgent modul rendszerképéhez az indításkor, majd hozzáférhetnek az üzemelő példányhoz, és elindítják a modul többi rendszerképét.

Amikor a konfigurációs fájlba kerül egy IoT Edge-eszközön, és meg kell adnia a hitelesítési adatait, tanúsítványait és szülő gazdanevét, frissítse az edgeAgent tároló rendszerképét is.

Ha a legfelső szintű átjáróeszköz a tároló rendszerkép-kérelmek kezeléséhez van konfigurálva, cserélje le a helyére a szülő állomásnevet és az `mcr.microsoft.com` API-proxy figyelőportját. Az üzembe helyezési jegyzékben használhatja a parancsot parancsikonként, de ehhez az edgeHub-modulnak kell kezelnie az útválasztást, és ez a modul még nem indult `$upstream` el. Például:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

Ha helyi tárolójegyzéket használ, vagy manuálisan biztosítja a tároló rendszerképeket az eszközön, ennek megfelelően frissítse a konfigurációs fájlt.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>A futásidő konfigurálása és proxymodul üzembe helyezése

Az **API-proxymodul** szükséges a felhő és az összes lefelé irányuló eszköz közötti összes kommunikáció IoT Edge útválasztásához. A IoT Edge alsó rétegében található, alsóbb rétegbeli eszközöknek nincs szükségük IoT Edge a modulra.

Az API-proxymodul úgy lett kialakítva, hogy a leggyakoribb átjáró-forgatókönyvek kezeléséhez legyen testre szabva. Ez a cikk röviden ismerteti a modulok alapszintű konfigurációban való beállításának lépéseit. Részletesebb információkért és példákért tekintse meg az [API-proxymodul konfigurálása az átjáróhierarchia](how-to-configure-api-proxy-module.md) forgatókönyvéhezcímet.

1. A [Azure Portal](https://portal.azure.com)lépjen az IoT Hubra.
1. Válassza **IoT Edge** a navigációs menüből a Kívánt elemet.
1. Válassza ki a konfigurálni kívánt alsó rétegben található eszközt a IoT Edge **listájából.**
1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.
1. A IoT Edge **modulok szakaszban** válassza a **Hozzáadás, majd** a **Marketplace-modul lehetőséget.**
1. Keresse meg és válassza ki a **IoT Edge API-proxy modult.**
1. Válassza ki az API-proxymodul nevét az üzembe helyezett modulok listájából, és frissítse a következő modulbeállításokat:
   1. A **Modulbeállítások lapon** frissítse a Kép **URI értékét.** Cserélje le a `mcr.microsoft.com` elemet a `$upstream:443` kérdésre.
   1. A Környezeti **változók lapon** frissítse a változó értékét **NGINX_DEFAULT_PORT** `443` értékre.
   1. A Tároló **létrehozása beállítások lapon** frissítse a portkötéseket a 443-as portra való hivatkozáshoz.

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

   Ezek a módosítások úgy konfigurálják az API-proxymodult, hogy a 443-as portot figyelje. A portkötések ütközésének elkerülése érdekében úgy kell konfigurálnia az edgeHub-modult, hogy ne figyelje a 443-as portot. Ehelyett az API-proxymodul a 443-as porton keresztül irányít minden edgeHub-forgalmat.

1. Válassza **a Futásidejű beállítások lehetőséget.**
1. Frissítse az edgeHub-modul beállításait:

   1. A Kép **mezőben** cserélje le a `mcr.microsoft.com` helyére a következőt: `$upstream:443` .
   1. A **Beállítások létrehozása mezőben** törölje a 443-as port portkötését, és hagyja meg az 5671-es és a 8883-as port kötését.

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

1. Frissítse az edgeAgent modul beállításait:
   1. A Kép **mezőben** cserélje le a `mcr.microsoft.com` helyére a következőt: `$upstream:443` .

1. A **futásidejű** beállítások módosításainak mentéséhez válassza a Mentés lehetőséget.
1. Válassza **a Tovább: Útvonalak lehetőséget** a következő lépéshez.
1. Ahhoz, hogy a lefelé irányuló eszközök eszközről a felhőbe irányuló üzenetei elérhűek IoT Hub egy útvonalat, amely az összes üzenetet a felé `$upstream` továbbítja. A felsőbb rétegbeli paraméter alsóbb rétegbeli eszközök esetén a szülőeszközre mutat. Például:
    1. **Név:**`Route`
    1. **Érték:**`FROM /messages/* INTO $upstream`
1. Az **utolsó lépéshez válassza** az Áttekintés + létrehozás lehetőséget.
1. Válassza **a Létrehozás lehetőséget** az eszköz üzembe helyezéséhez.

## <a name="next-steps"></a>Következő lépések

[IoT Edge-eszköz használata átjáróként](iot-edge-as-gateway.md)

[Az API-proxymodul konfigurálása az átjáróhierarchia forgatókönyvéhez](how-to-configure-api-proxy-module.md)