---
title: Az Azure IoT Hub APT manifest eszköz frissítésének ismertetése | Microsoft Docs
description: Megtudhatja, hogyan használja a IoT Hub az eszköz frissítését az apt manifest használatával egy Package-alapú frissítéshez.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679587"
---
# <a name="device-update-apt-manifest"></a>Eszköz frissítése APT-jegyzékfájl

Az APT manifest egy olyan JSON-fájl, amely az APT Update Handler által megkövetelt frissítési részleteket ismerteti. Ez a fájl importálható a IoT Hub eszköz frissítéséhez, ugyanúgy, mint bármely más frissítés.

[További](import-update.md) információ a frissítések eszköz frissítése szolgáltatásba való importálásáról.

## <a name="overview"></a>Áttekintés

Ha egy APT-jegyzékfájlt frissítésként küldenek egy eszköz frissítési ügynökének, az ügynök feldolgozza a jegyzékfájlt, és elvégzi a szükséges műveleteket. Ezek a műveletek magukban foglalják az APT manifest-fájlban megadott csomagok letöltését és telepítését, valamint azok függőségeit.

Az eszköz frissítése támogatja az APT frissítés típusa és az APT Update kezelőt. Ez a támogatás lehetővé teszi, hogy az eszköz frissítési ügynöke kiértékelje a telepített Debian-csomagokat, és frissítse a szükséges csomagokat. 

## <a name="schema"></a>Séma

Az APT-jegyzékfájl egy verziószámmal rendelkező JSON-fájl.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Példa:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Az APT-jegyzékfájl neve. A forgatókönyvek esetében ez lehet bármilyen név vagy azonosító. Például: `contoso-iot-edge`.

### <a name="version"></a>version

Az APT-jegyzékfájl verziószáma. Például: `1.0.0.0`.


### <a name="packages"></a>csomagok

A Package-specifikus tulajdonságokat tartalmazó objektumok listája.

#### <a name="name"></a>name

A csomag neve vagy azonosítója. Például: `iotedge`.

#### <a name="version"></a>version

A csomag kívánt verziójának feltételei. Például: `1.0.8-2`.

Jelenleg csak a pontos verziószám támogatott. A verziószám a kívánt Debian-csomag verziószáma [EPOCH:] upstream_version [-debian_revision].

a **EPOCH** egy előjel nélküli int.

a **upstream_version** alfanumerikus karaktereket és karaktereket (például ".", "+", "-" és "~") is tartalmazhat. Egy számjegytel kell kezdődnie.
> [!NOTE]
> a "1.0.8" értéke "1.0.8-0".

Például **`"name":"iotedge" and "version":"1.0.8-2"`** egyenértékű a csomagok parancs használatával történő telepítésével. `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> A Version érték nem tartalmaz egyenlőségjelet

Ha a verzió nincs megadva, a rendszer a megadott csomag legújabb elérhető verzióját fogja telepíteni.

[További](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) információ a Debian-csomagok verziószámozásáról.

> [!NOTE]
> Az APT Package Manager figyelmen kívül hagyja a csomagok verziószámozási követelményeit, ha a telepítendő függő csomagok automatikusan fel lesznek oldva. Ha a függő csomagok explicit verzióit nem adják meg, a rendszer a legújabbat fogja használni, annak ellenére, hogy maga a csomag is megadhat egy szigorú követelményt (=) egy adott verzióra vonatkozóan. Ez az automatikus megoldás hibákat okozhat a nem teljesített függőségekkel kapcsolatban. [További információ](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Ha frissíti a Azure IoT Edge biztonsági démon egy adott verzióját, akkor a csomag és a függő csomag kívánt verzióját is fel kell vennie az `iotedge` `libiothsm-std` apt-jegyzékbe.
[További információ](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Az eszköz frissítési ügynökének és függőségeinek frissítéséhez egy apt-jegyzékfájlt is használhat. Sorolja fel az eszköz frissítési ügynökének nevét és a kívánt verziót az apt-jegyzékfájlban, mint bármely más csomag esetében. Ezt az apt-jegyzékfájlt Ezután importálhatja és telepítheti az eszköz frissítésével IoT Hub folyamathoz. 

## <a name="removing-packages"></a>Csomagok eltávolítása

Az eszközről is használhat egy apt-jegyzékfájlt a telepített csomagok eltávolításához. Egyetlen apt-jegyzékfájlt is használhat több csomag eltávolítására, hozzáadására és frissítésére. Egy csomag eltávolításához vegyen fel egy mínusz "-" jelet a csomag neve után. Az eltávolítani kívánt csomagok verziószámát nem kell megadnia. A csomagok egy apt-jegyzékből való eltávolítása nem távolítja el a függőségeit és konfigurációit.

Példa:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Ez az apt-jegyzékfájl eltávolítja a "foo" csomagot azokról az eszközökről, amelyekre központilag telepítették. 

## <a name="recommended-value-for-installed-criteria"></a>A telepített feltételek javasolt értéke

Az APT-jegyzékfájlok telepítési feltételei az az APT manifest neve, amely az `<name>-<version>` `<name>` apt-jegyzékfájl `<version>` verziója. Például: `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Útmutató az APT-jegyzék létrehozásához

Az APT-jegyzékfájl létrehozásakor figyelembe kell venni néhány útmutatót:

- Mindig győződjön meg arról, hogy az APT manifest egy jól formázott JSON-fájl
- Minden APT-jegyzékfájlnak egyedi verziójúnak kell lennie. Próbáljon ki egy szabványosított módszertant az APT-jegyzékfájl verziójának növeléséhez, hogy ésszerű legyen a forgatókönyvek szempontjából, és könnyen követhető legyen
- Amikor az egyes csomagok kívánt állapotára kerül, adja meg annak a csomagnak a pontos nevét és verzióját, amelyet telepíteni szeretne az eszközére. Mindig ellenőrizze az értékeket a csomag forrásaként használni kívánt csomag adattárában
- Győződjön meg arról, hogy az APT-jegyzékfájlban lévő csomagok a telepítendő vagy eltávolítandó sorrendben vannak felsorolva
- Mindig ellenőrizze a csomagok telepítését egy tesztelési eszközön, hogy az eredmény kívánatos legyen
- Egy csomag adott verziójának (például:) telepítésekor az `iotedge 1.0.9-1` ajánlott eljárás az, hogy az apt-jegyzékben a függő csomagok explicit verzióit is telepíteni kell (például: `libiothsm 1.0.9-1` ).
- Habár nincs felhatalmazva, mindig gondoskodjon arról, hogy az APT-jegyzékfájl kumulatív legyen, így elkerülhető, hogy az eszköz ismeretlen állapotba kerüljön. Az összesítő frissítés biztosítja, hogy az eszközök az összes csomag kívánt verziójával rendelkezzenek, akkor is, ha az eszköz a telepítés meghiúsulása miatt kihagyott egy APT Update-telepítést, vagy offline állapotba kerül.

Például:

**Alapszintű APT-jegyzék**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**HELYTELEN FRISSÍTÉS**

Ez a frissítés tartalmazza a menüsávot, de nem a foo csomagot.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**JÓ FRISSÍTÉS**

Ez a frissítés tartalmazza a foo-csomagot, és tartalmazza a sáv csomagját is.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Új frissítés importálása](import-update.md)

