---
title: Azure VMware-megoldás CloudSimple – a CloudSimple DNS konfigurálása a privát felhőhöz
description: Ismerteti, hogyan állítható be a DNS-névfeloldás a vCenter-kiszolgálóhoz való hozzáféréshez a helyszíni munkaállomásokról származó CloudSimple-alapú privát felhőben
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63822050512421895b0cfed08fb141f77da20b03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899252"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>DNS konfigurálása névfeloldáshoz a saját Felhőbeli vCenter való hozzáféréshez a helyszíni munkaállomásokról

Ha a vCenter-kiszolgálót a helyszíni munkaállomásokról származó CloudSimple privát felhőben szeretné elérni, konfigurálnia kell a DNS-címek feloldását, hogy a vCenter-kiszolgálót az állomásnév és az IP-cím is megcélozhatja.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>A saját felhőhöz tartozó DNS-kiszolgáló IP-címének beszerzése

1. Jelentkezzen be a [CloudSimple-portálra](access-cloudsimple-portal.md).

2. Navigáljon az **erőforrások**  >  **privát felhők** elemre, és válassza ki azt a privát felhőt, amelyhez csatlakozni szeretne.

3. Az **alapszintű információ** területen található privát felhő **Összegzés** lapján másolja a saját Felhőbeli DNS-kiszolgáló IP-címét.

    ![Privát Felhőbeli DNS-kiszolgálók](media/private-cloud-dns-server.png)


A DNS-konfigurációhoz használja a következő lehetőségek egyikét.

* [Zóna létrehozása a (z) *. cloudsimple.io DNS-kiszolgálón](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Feltételes továbbító létrehozása a helyszíni DNS-kiszolgálón a *. cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Zóna létrehozása a (z) *. cloudsimple.io DNS-kiszolgálón

Létrehozhat egy zónát helyettes zónáként, és a saját felhőben lévő DNS-kiszolgálókra irányíthatja a névfeloldást. Ez a szakasz a kötési DNS-kiszolgáló vagy a Microsoft Windows DNS-kiszolgáló használatával kapcsolatos információkat tartalmaz.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Zóna létrehozása kötési DNS-kiszolgálón

A konfigurálandó fájl és paraméterek az egyéni DNS-beállítástól függően változhatnak.

Az alapértelmezett kötési kiszolgáló konfigurációja esetében például szerkessze a/etc/named.conf-fájlt a DNS-kiszolgálón, és adja hozzá a következő zónával kapcsolatos információkat.

> [!NOTE]
>Ez a cikk a Slave kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Zóna létrehozása Microsoft Windows DNS-kiszolgálón

1. Kattintson a jobb gombbal a DNS-kiszolgálóra, és válassza az **új zóna** lehetőséget. 
  
    ![Képernyőkép, amely kiemeli az új zóna menüpontot.](media/DNS01.png)
2. Válassza ki a **helyettes zónát** , és kattintson a **tovább** gombra.

    ![Képernyőkép, amely kiemeli a helyettes zóna beállítást.](media/DNS02.png)
3. A környezettől függően válassza ki a megfelelő beállítást, majd kattintson a **tovább** gombra.

    ![A zóna adatreplikálási beállításait megjelenítő képernyőkép.](media/DNS03.png)
4. Válassza a **címkeresési zóna** lehetőséget, majd kattintson a **tovább** gombra.

    ![A Címkeresési zóna beállítását kiemelő képernyőkép.](media/DNS01.png)
5. Adja meg a zóna nevét, és kattintson a **tovább** gombra.

    ![A zóna nevének megadását bemutató képernyőkép.](media/DNS05.png)
6. Adja meg azon DNS-kiszolgálók IP-címeit, amelyeket a CloudSimple-portálról beszerzett saját felhőhöz.

    ![Új zóna](media/DNS06.png)
7. A varázsló telepítésének befejezéséhez kattintson a **tovább** gombra.

## <a name="create-a-conditional-forwarder"></a>Feltételes továbbító létrehozása

A feltételes továbbító továbbítja a DNS-névfeloldási kérelmeket a kijelölt kiszolgálóra. Ezzel a beállítással a *. cloudsimple.io-re irányuló kérések a privát felhőben található DNS-kiszolgálókra lesznek továbbítva. Az alábbi példák bemutatják, hogyan állíthat be továbbítókat különböző típusú DNS-kiszolgálókon.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Feltételes továbbító létrehozása egy kötési DNS-kiszolgálón

A konfigurálandó fájl és paraméterek az egyéni DNS-beállítástól függően változhatnak.

Az alapértelmezett kötési kiszolgáló konfigurációja esetében például szerkessze a/etc/named.conf fájlt a DNS-kiszolgálón, és adja hozzá a következő feltételes továbbítási adatokat.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Feltételes továbbító létrehozása Microsoft Windows DNS-kiszolgálón

1. Nyissa meg a DNS-kezelőt a DNS-kiszolgálón.
2. Kattintson a jobb gombbal a **feltételes továbbítók** elemre, és válassza az új feltételes továbbító hozzáadásának lehetőségét.

    ![Feltételes továbbító 1 Windows DNS](media/DNS08.png)
3. Adja meg a DNS-tartományt és a DNS-kiszolgálók IP-címét a privát felhőben, majd kattintson **az OK** gombra.
