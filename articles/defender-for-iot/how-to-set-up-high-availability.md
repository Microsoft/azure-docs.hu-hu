---
title: Magas rendelkezésre állás beállítása
description: Növelje a Defender rugalmasságát a IoT üzembe helyezéséhez a helyszíni felügyeleti konzol magas rendelkezésre állású berendezésének telepítésével. A magas rendelkezésre állású telepítések biztosítják, hogy a felügyelt érzékelők folyamatosan jelentsenek egy aktív helyszíni felügyeleti konzolt.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: eb5f2c6293042f44de5e3c061c6d379bee6e5b06
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820585"
---
# <a name="about-high-availability"></a>A magas rendelkezésre állás ismertetése

Növelje a Defender rugalmasságát a IoT üzembe helyezéséhez a helyszíni felügyeleti konzol magas rendelkezésre állású berendezésének telepítésével. A magas rendelkezésre állású telepítések biztosítják, hogy a felügyelt érzékelők folyamatosan jelentsenek egy aktív helyszíni felügyeleti konzolt.

Ez az üzembe helyezés egy, az elsődleges és másodlagos berendezést tartalmazó helyszíni felügyeleti konzol-párral van megvalósítva.

## <a name="about-primary-and-secondary-communication"></a>Tudnivalók az elsődleges és a másodlagos kommunikációról

Az elsődleges és másodlagos helyszíni felügyeleti konzol párosításakor:

- A helyszíni felügyeleti konzol SLL tanúsítványa az elsődleges és a másodlagos készülékek közötti biztonságos kapcsolat létrehozásához van alkalmazva. A SLL lehet az önaláírt tanúsítvány, amely alapértelmezés szerint telepítve van, vagy az ügyfél által telepített tanúsítvány.

- A helyszíni felügyeleti konzol elsődleges adatbiztonsági mentését a rendszer 10 percenként automatikusan a másodlagos helyszíni felügyeleti konzolra készíti. A helyszíni felügyeleti konzol konfigurációit és az eszközökre vonatkozó biztonsági mentést. A biztonsági mentés nem tartalmazza a PCAP-fájlokat és-naplókat. A PCAPs és a naplók biztonsági mentését és visszaállítását manuálisan végezheti el.

- A felügyeleti konzol elsődleges beállítása duplikálva van a másodlagoson; például a rendszerbeállítások elemre. Ha ezek a beállítások frissülnek az elsődlegesen, a rendszer a másodlagosnál is frissíti őket.

- A másodlagos licenc lejárta előtt meg kell határoznia, hogy az elsődleges legyen a licenc frissítése érdekében.

## <a name="about-failover-and-failback"></a>Tudnivalók a feladatátvétellel és a feladat-visszavétellel kapcsolatban

Ha egy érzékelő nem tud csatlakozni az elsődleges helyszíni felügyeleti konzolhoz, az automatikusan csatlakozik a másodlagoshoz. Az elsődleges és a másodlagos rendszer egyszerre fogja támogatni a rendszerét, ha az érzékelők kevesebb mint fele kommunikál a másodlagosval. A másodlagos átveszi, ha az érzékelők több mint fele kommunikál vele. Az elsődlegesről a másodlagosra történő feladatátvétel körülbelül három percet vesz igénybe. A feladatátvétel bekövetkeztekor az elsődleges helyszíni felügyeleti konzol lefagy. Ha ez történik, bejelentkezhet a másodlagosra ugyanazzal a bejelentkezési hitelesítő adatokkal.

A feladatátvétel során az érzékelők továbbra is megpróbálnak kommunikálni az elsődleges berendezéssel. Ha a felügyelt érzékelők több mint fele sikeresen kommunikál az elsődlegesvel, a rendszer visszaállítja az elsődlegest. A következő üzenet jelenik meg a másodlagos konzolon, amikor az elsődleges visszaállításra kerül.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Képernyőkép a másodlagos konzolon megjelenő üzenetről az elsődleges visszaállításkor.":::

Az átirányítás után jelentkezzen be ismét az elsődleges készülékre.

## <a name="high-availability-setup-overview"></a>A magas rendelkezésre állás beállítása – áttekintés

A telepítési és konfigurációs eljárásokat négy fő szakaszban hajtják végre:

1. Telepítsen egy helyszíni felügyeleti konzolt elsődleges berendezéssel. 

2. Konfigurálja a helyszíni felügyeleti konzol elsődleges berendezését. Például ütemezett biztonsági mentési beállítások, VLAN-beállítások. A részletekért tekintse meg a helyszíni felügyeleti konzol felhasználói útmutatóját. A rendszer automatikusan alkalmazza a beállításokat a másodlagos készülékre párosítás után.

3. Telepítsen egy helyszíni felügyeleti konzol másodlagos berendezését. További információ: [a Defender for IoT telepítése](how-to-install-software.md).

4. Az [itt](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console)leírtak szerint párosítsa az elsődleges és a másodlagos helyszíni felügyeleti konzol készülékeit. A telepítés végrehajtásához az elsődleges helyszíni felügyeleti konzolnak legalább két érzékelőt kell kezelnie.

## <a name="high-availability-requirements"></a>Magas rendelkezésre állási követelmények

Győződjön meg arról, hogy teljesítette a következő magas rendelkezésre állási követelményeket:

- Tanúsítványkövetelmények

- A szoftverre és a hardverre vonatkozó követelmények

- Hálózati hozzáférési követelmények

### <a name="software-and-hardware-requirements"></a>A szoftverre és a hardverre vonatkozó követelmények

- Mind az elsődleges, mind a másodlagos helyszíni felügyeleti konzol készülékének azonos hardveres modellekkel és szoftver-verziókkal kell futnia.

- A magas rendelkezésre állású rendszer csak a IoT felhasználók számára állítható be a CLI-eszközök használatával.

### <a name="network-access-requirements"></a>Hálózati hozzáférési követelmények

Ellenőriznie kell, hogy a szervezeti biztonsági házirend lehetővé teszi-e a következő szolgáltatások elérését az elsődleges és másodlagos helyszíni felügyeleti konzolon. Ezek a szolgáltatások az érzékelők és a másodlagos helyszíni felügyeleti konzol közötti kapcsolatot is lehetővé teszik:

|Port|Szolgáltatás|Leírás|
|----|-------|-----------|
|**443 vagy TCP**|HTTPS|Hozzáférést biztosít a helyszíni felügyeleti konzol webkonzolhoz.|
|**22 vagy TCP**|SSH|Szinkronizálja az adatközpontot az elsődleges és a másodlagos helyszíni felügyeleti konzol berendezései között.|
|**123 vagy UDP**|NTP| A helyszíni felügyeleti konzol NTP-idő szinkronizálása. Győződjön meg arról, hogy az aktív és a passzív készülékek ugyanazzal az időzónával vannak definiálva.|

## <a name="create-the-primary-and-secondary-pair"></a>Az elsődleges és a másodlagos pár létrehozása

Az eljárás megkezdése előtt ellenőrizze, hogy mind az elsődleges, mind a másodlagos helyszíni felügyeleti konzol készüléke be van-e kapcsolva.  

### <a name="on-the-primary"></a>Az elsődleges

1. Jelentkezzen be a CLI-be a IoT-felhasználó védőjeként.

2. Futtassa az alábbi parancsot az elsődlegesen:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>Ebben a dokumentumban a főkiszolgálói felügyeleti konzolt elsődlegesként nevezik, és az ügynököt másodlagosnak nevezzük.

3. Adja meg a másodlagos berendezés IP-címét a ```<Secondary ip>``` mezőben, majd válassza az ENTER billentyűt. Az IP-cím ezután érvényesítve lesz, és az SSL-tanúsítvány le lesz töltve az elsődlegesre. Az IP-cím megadásával az érzékelők a másodlagos készülékhez is társítva vannak.

4. A következő parancs futtatásával ellenőrizze, hogy a tanúsítvány megfelelően van-e telepítve:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Futtassa az alábbi parancsot az elsődlegesen. **Ne fusson a sudo-val.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Ez lehetővé teszi az elsődleges és a másodlagos készülékek közötti kapcsolat biztonsági mentését és helyreállítását.

6. Adja meg a másodlagos IP-címet, és válassza az ENTER billentyűt.

### <a name="on-the-secondary"></a>A másodlagos

1. Jelentkezzen be a CLI-be a IoT-felhasználó védőjeként.

2. Futtassa a következő parancsot a másodlagoson. Ne **fusson a sudo-val**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Ez lehetővé teszi, hogy az elsődleges és a másodlagos készülék közötti kapcsolat biztonsági mentési és visszaállítási célokat szolgáljon.

3. Adja meg az elsődleges IP-címet, majd nyomja le az ENTER billentyűt.

### <a name="track-high-availability-activity"></a>Magas rendelkezésre állású tevékenység követése

Az alapvető alkalmazási naplók exportálhatók a Defender for IoT támogatási csapatának a magas rendelkezésre állással kapcsolatos problémák kezelésére.  

Az alapvető naplók elérése:

1. Válassza az **Exportálás** lehetőséget a **Rendszerbeállítások** ablakban.

## <a name="update-the-on-premises-management-console-with-high-availability"></a>A helyszíni felügyeleti konzol frissítése magas rendelkezésre állással

A magas rendelkezésre állású frissítést a következő sorrendben hajtsa végre. Az új lépések megkezdése előtt győződjön meg arról, hogy az egyes lépések befejeződik.

A magas rendelkezésre állással történő frissítéshez:

1. Frissítse az elsődleges helyszíni felügyeleti konzolt.

2. Frissítse a másodlagos helyszíni felügyeleti konzolt.

3. Frissítse az érzékelőket.

## <a name="see-also"></a>Lásd még

[A helyszíni felügyeleti konzol aktiválása és beállítása](how-to-activate-and-set-up-your-on-premises-management-console.md)