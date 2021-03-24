---
title: IoT Edge verziójának frissítése az eszközökön – Azure IoT Edge | Microsoft Docs
description: IoT Edge eszközök frissítése a biztonsági démon és a IoT Edge futtatókörnyezet legújabb verzióinak futtatásához
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b24276974eba76aa841cdd7f02145210713474eb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872285"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Az IoT Edge biztonsági démon és futtatókörnyezet frissítése

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Mivel a IoT Edge szolgáltatás új verziókat szabadít fel, frissítenie kell a IoT Edge eszközöket a legújabb funkciókkal és biztonsági frissítésekkel. Ez a cikk tájékoztatást nyújt arról, hogyan frissítheti IoT Edge eszközeit, amikor új verzió érhető el.

Egy IoT Edge eszköz két összetevőjét frissíteni kell, ha újabb verzióra szeretne áttérni. Az első a biztonsági démon, amely az eszközön fut, és elindítja a futásidejű modulokat az eszköz indításakor. A biztonsági démont jelenleg csak az eszközről lehet frissíteni. A második összetevő a futtatókörnyezet, amely az IoT Edge hub és a IoT Edge Agent moduljaiból tevődik fel. Attól függően, hogy miként strukturálja az üzemelő példányt, a futtatókörnyezet az eszközről vagy távolról is frissíthető.

A Azure IoT Edge legújabb verziójának megkereséséhez tekintse meg [Azure IoT Edge kiadásait](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>A biztonsági démon frissítése

A IoT Edge Security Daemon egy natív összetevő, amelyet a IoT Edge eszköz csomagkezelő funkciójával kell frissíteni.

A parancs használatával keresse meg az eszközön futó Security Daemon verzióját `iotedge version` .

>[!IMPORTANT]
>Ha az 1,0-es vagy a 1,1-es verziójú eszközt a 1,2-as verzióra frissíti, a telepítési és konfigurálási folyamatokban további lépések szükségesek. További információkért tekintse meg a cikk későbbi részében leírt lépéseket: [speciális eset: frissítés 1,0 vagy 1,1 – 1,2](#special-case-update-from-10-or-11-to-12).

# <a name="linux"></a>[Linux](#tab/linux)

Linux x64 rendszerű eszközökön használja az apt-get vagy a megfelelő csomagkezelő eszközt a biztonsági démon legújabb verzióra való frissítéséhez.

A legújabb adattár-konfiguráció beszerzése a Microsofttól:

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Málna PI operációs rendszer stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Másolja a generált listát.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Telepítse a Microsoft GPG nyilvános kulcsot.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Az apt frissítése.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Ellenőrizze, hogy a IoT Edge mely verziói érhetők el.

   ```bash
   apt list -a iotedge
   ```

Ha a biztonsági démon legújabb verziójára szeretne frissíteni, használja a következő parancsot, amely a **libiothsm-STD** verziót is frissíti a legújabb verzióra:

   ```bash
   sudo apt-get install iotedge
   ```

Ha a biztonsági démon egy adott verziójára szeretne frissíteni, adja meg a verziót az apt List kimenetből. Amikor frissül a **iotedge** , az automatikusan megkísérli frissíteni az **libiothsm-STD** csomagot a legújabb verzióra, ami függőségi ütközést eredményezhet. Ha nem a legújabb verziót fogja használni, ügyeljen arra, hogy mindkét csomagot ugyanarra a verzióra célozza meg. A következő parancs például a 1.0.9 kiadás egy adott verzióját telepíti:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Ha a telepíteni kívánt verzió az apt-get használatával nem érhető el, a curl használatával bármely verziót megcélozhat a [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) adattárból. A telepíteni kívánt verzióhoz keresse meg a megfelelő **libiothsm-STD-** és **iotedge** -fájlokat az eszközhöz. Minden fájlhoz kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. A hivatkozás címe segítségével telepítse az összetevők adott verzióit:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Ellenőrizze, hogy a IoT Edge mely verziói érhetők el.

   ```bash
   apt list -a aziot-edge
   ```

Ha a IoT Edge legújabb verziójára szeretne frissíteni, használja a következő parancsot, amely az Identity Service-t is frissíti a legújabb verzióra:

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

A Windows rendszeren futó Linux-IoT Edge esetén a IoT Edge egy Windows-eszközön üzemeltetett linuxos virtuális gépen fut. Ez a virtuális gép előre telepítve van a IoT Edgeval, és a Microsoft Update kezeli az összetevők naprakészen tartásához. Ha engedélyezve vannak az automatikus frissítések, a rendszer minden alkalommal letölti és telepíti az új frissítéseket, amikor azok elérhetővé válnak.

A Windows IoT Edge a IoT Edge közvetlenül a Windows-eszközön fut. A PowerShell-parancsfájlok használatával kapcsolatos frissítési utasításokért lásd: [Azure IoT Edge telepítése és kezelése a Windows](how-to-install-iot-edge-windows-on-windows.md)rendszerhez.
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Jelenleg nem támogatott a Windows rendszerű eszközökön futó IoT Edge 1,2-es verzió.

:::moniker-end

---

## <a name="update-the-runtime-containers"></a>A futásidejű tárolók frissítése

A IoT Edge-ügynök és a IoT Edge hub-tárolók frissítésének módja attól függ, hogy a működés közbeni címkéket (például 1,0) vagy az adott címkéket (például 1.0.7) használja-e a telepítésben.

A parancsok vagy a segítségével keresse meg az eszközön a IoT Edge-ügynök és IoT Edge hub-modulok verzióját `iotedge logs edgeAgent` `iotedge logs edgeHub` .

  ![Tároló verziójának keresése a naplókban](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge címkék ismertetése

A IoT Edge-ügynök és a IoT Edge hub-lemezképek a IoT Edge azon verziójával vannak címkézve, amelyhez társítva vannak. A következő két különböző módon használhatja a címkéket a futásidejű lemezképekkel:

* **Jelölő címkék** – csak a verziószám első két értékének beolvasásával kérheti le az adott számjegyeknek megfelelő legújabb képet. Például a 1,1 frissül, amikor új kiadás jelenik meg, amely a legújabb 1.1. x verzióra mutat. Ha a IoT Edge eszközön lévő tároló futtatókörnyezete újra lekéri a lemezképet, a futásidejű modulok a legújabb verzióra frissülnek. Központi telepítések a Azure Portal alapértelmezettről a címkékre. *Ez a megközelítés fejlesztési célokra javasolt.*

* **Megadott címkék** – a verziószám mindhárom értékének használatával explicit módon állíthatja be a rendszerkép verzióját. Például a 1.1.0 nem változik a kezdeti kiadás után. Ha készen áll a frissítésre, deklarálhat egy új verziószámot az üzembe helyezési jegyzékben. *Ezt a megközelítést éles célokra javasoljuk.*

### <a name="update-a-rolling-tag-image"></a>Egy gördülő címke rendszerképének frissítése

Ha gördülő címkéket használ a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1,0**), akkor a lemezkép legújabb verziójának lekéréséhez kényszeríteni kell a tároló futtatókörnyezetét az eszközön.

Törölje a rendszerkép helyi verzióját a IoT Edge eszközről. Windows rendszerű gépeken a biztonsági démon eltávolítása eltávolítja a futtatókörnyezet lemezképeit is, így nem kell újból végrehajtania ezt a lépést.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Előfordulhat, hogy a `-f` rendszerképek eltávolításához a Force jelzőt kell használnia.

A IoT Edge szolgáltatás lekéri a futásidejű lemezképek legújabb verzióit, és automatikusan elindítja őket az eszközön.

### <a name="update-a-specific-tag-image"></a>Egy adott címke rendszerképének frissítése

Ha adott címkéket használ a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1.0.8**), akkor mindössze annyit kell tennie, hogy frissíti a címkét a telepítési jegyzékben, és alkalmazza a módosításokat az eszközön.

1. A Azure Portal IoT Hub válassza ki a IoT Edge eszközt, és válassza a **modulok beállítása** lehetőséget.

1. A **IoT Edge-modulok** szakaszban válassza a **futtatókörnyezet beállításai** lehetőséget.

   ![Futtatókörnyezet beállításainak konfigurálása](./media/how-to-update-iot-edge/configure-runtime.png)

1. A **Futásidejű beállítások** területen frissítse a **rendszerkép** értékét az **Edge hub** számára a kívánt verzióra. Még ne válassza a **Mentés** lehetőséget.

   ![Edge hub-rendszerkép verziójának frissítése](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Csukja össze az **Edge hub** beállításait, vagy görgessen lefelé, és frissítse az **Edge-ügynök** **rendszerképének** értékét ugyanazzal a kívánt verzióval.

   ![Edge hub-ügynök verziójának frissítése](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Kattintson a **Mentés** gombra.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a telepítést, és válassza a **Létrehozás** lehetőséget.

## <a name="special-case-update-from-10-or-11-to-12"></a>Különleges eset: frissítés 1,0 vagy 1,1 és 1,2 között

Az 1,2-es verziótól kezdődően a IoT Edge szolgáltatás új csomagot használ, és a telepítési és konfigurálási folyamatokban némi különbségek vannak. Ha az 1,0-es vagy a 1,1-es verziót futtató IoT Edge eszközzel rendelkezik, a következő útmutatást követve megismerheti az 1,2-ra való frissítést.

>[!NOTE]
>Jelenleg nem támogatott a Windows rendszerű eszközökön futó IoT Edge 1,2-es verzió.

A 1,2-es és korábbi verziók közötti főbb különbségek a következők:

* A csomag neve a **iotedge** értékről a **aziotra** módosult.
* A **libiothsm-STD** csomag már nem használatos. Ha a IoT Edge kiadás részeként biztosított standard csomagot használta, akkor a konfigurációk átvihetők az új verzióra. Ha a libiothsm-STD eltérő implementációját használta, akkor a felhasználó által megadott összes tanúsítványt, például az eszköz identitási tanúsítványát, az eszköz HITELESÍTÉSSZOLGÁLTATÓját és a megbízhatósági csomagot újra kell konfigurálni.
* Egy új Identity Service, a **aziot** a 1,2 kiadás részeként lett bevezetve. Ez a szolgáltatás kezeli a IoT Edge az identitás kiépítését és felügyeletét, valamint a IoT Hubokkal kommunikáló egyéb eszközök összetevőit, például az Azure IoT Hub az eszköz frissítését. <!--TODO: add link to ADU when available -->
* Az alapértelmezett konfigurációs fájl új névvel és hellyel rendelkezik. Korábban `/etc/iotedge/config.yaml` az eszköz konfigurációs adatai mostantól várhatóan bekerülnek a szolgáltatásba `/etc/aziot/config.toml` . A `iotedge config import` parancs segítségével a konfigurációs adatok áttelepíthetők a régi helyükre és szintaxisra az újat.
* Minden olyan modul, amely a IoT Edge munkaterhelés API-t használja az állandó adatok titkosításához vagy visszafejtéséhez, a frissítés után nem lehet visszafejteni. IoT Edge dinamikusan generál egy fő azonosító kulcsot és egy titkosítási kulcsot belső használatra. Ez a kulcs nem kerül át az új szolgáltatásba. A IoT Edge v 1.2 egy újat fog előállítani.

A frissítési folyamatok automatizálása előtt ellenőrizze, hogy működik-e tesztelési gépen.

Ha elkészült, kövesse az alábbi lépéseket az eszközök IoT Edge frissítéséhez:

1. A legújabb adattár-konfiguráció beszerzése a Microsofttól:

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Málna PI operációs rendszer stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Másolja a generált listát.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Telepítse a Microsoft GPG nyilvános kulcsot.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. Az apt frissítése.

   ```bash
   sudo apt-get update
   ```

5. Távolítsa el a IoT Edge korábbi verzióját, és hagyja a konfigurációs fájlokat a helyükre.

   ```bash
   sudo apt-get remove iotedge
   ```

6. Telepítse a IoT Edge legújabb verzióját, valamint a IoT Identity Service-t.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. Importálja a régi config. YAML fájlt az új formátumba, és alkalmazza a konfigurációs adatokat.

   ```bash
   sudo iotedge config import
   ```

Most, hogy az eszközökön futó IoT Edge szolgáltatás frissítve lett, a cikkben ismertetett lépéseket követve [frissítse a futásidejű tárolókat](#update-the-runtime-containers)is.

## <a name="special-case-update-to-a-release-candidate-version"></a>Speciális eset: frissítés a Release Candidate verzióra

Azure IoT Edge rendszeresen felszabadítja a IoT Edge szolgáltatás új verzióit. Az egyes stabil kiadások előtt egy vagy több Release Candidate (RC) verzió található. Az RC-verziók tartalmazzák a kiadás tervezett funkcióit, de továbbra is tesztelésen és ellenőrzésen mennek keresztül. Ha korábban egy új szolgáltatást szeretne tesztelni, telepítheti az RC-verziót, és visszajelzést küldhet a GitHubon keresztül.

A kiadásra jelölt verziók ugyanazt a számozási konvenciót követik, mint a Releases, de a **-RC** és egy növekményes szám is a végéhez fűzve. A kiadási jelölteket megtekintheti a stabil verzióként megjelenő [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases) listájában. Például keresse meg a **1.0.9-RC5** és a **1.0.9-RC6**, amelyek közül kettő a **1.0.9** előtt érkezett kiadási jelöltek közül. Azt is láthatja, hogy az RC-verziók **előzetes** címkékkel vannak megjelölve.

A IoT Edge-ügynök és a hub-modulok olyan RC-verziókkal rendelkeznek, amelyek ugyanazon egyezménnyel vannak megjelölve. Például: **MCR.microsoft.com/azureiotedge-hub:1.0.9-RC6**.

Előzetes verzióként a Release Candidate verziók nem szerepelnek a normál telepítők által megcélzott legújabb verzióban. Ehelyett manuálisan kell megcéloznia a tesztelni kívánt RC-verzióhoz tartozó eszközöket. A legtöbb esetben az RC verzióra történő telepítés vagy frissítés ugyanaz, mint a IoT Edge egyéb speciális verzióinak megcélzása.

A cikk fejezeteiből megtudhatja, hogyan frissíthet egy IoT Edge eszközt a biztonsági démon vagy a futásidejű modulok egy adott verziójára.

Ha IoT Edge telepíti a rendszert, és nem frissíti a meglévő telepítést, kövesse az [Offline vagy az adott verzió telepítésének](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)lépéseit.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a legújabb [Azure IoT Edge kiadásokat](https://github.com/Azure/azure-iotedge/releases).

Naprakészen tarthatja a legújabb frissítéseket és közleményeket a [eszközök internetes hálózata blogon](https://azure.microsoft.com/blog/topics/internet-of-things/)
