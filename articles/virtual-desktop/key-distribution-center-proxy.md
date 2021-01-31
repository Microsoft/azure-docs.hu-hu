---
title: Kerberos kulcsszolgáltató proxy Windows rendszerű virtuális asztal beállítása – Azure
description: Windows rendszerű virtuális asztali címkészlet beállítása Kerberos kulcsszolgáltató proxy használatára.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219655"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos kulcsszolgáltató proxy konfigurálása (előzetes verzió)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk bemutatja, hogyan konfigurálhat egy Kerberos kulcsszolgáltató (KDC) proxyt (előzetes verzió) a gazdagép-készlethez. Ez a proxy lehetővé teszi a szervezetek számára, hogy a vállalat határain kívül Kerberos hitelesítést végezzenek. A KDC-proxy használatával például engedélyezheti a külső ügyfelek intelligens kártyás hitelesítését.

## <a name="how-to-configure-the-kdc-proxy"></a>A KDC-proxy konfigurálása

A KDC-proxy konfigurálása:

1. Jelentkezzen be rendszergazdaként a Azure Portalba.

2. Nyissa meg a Windows rendszerű virtuális asztali lapot.

3. Válassza ki azt az alkalmazáskészletet, amely számára engedélyezni szeretné a KDC-proxyt, majd válassza az **RDP-tulajdonságok** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A Azure Portal lap képernyőképe, amely egy felhasználót jelöl ki a gazdagépeken, majd a példaként szolgáló címkészlet nevét, majd az RDP-tulajdonságokat.](media/rdp-properties.png)

4. Válassza a **speciális** fület, majd adjon meg egy értéket a következő formátumban szóközök nélkül:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![A speciális lapot kijelölő képernyőkép, amely a 4. lépésben leírtak szerint megadott értéket tartalmazza.](media/advanced-tab-selected.png)

5. Kattintson a **Mentés** gombra.

6. A kiválasztott gazdagépnek most meg kell kezdenie az RDP-kapcsolati fájlok kiírását a megadott kdcproxyname-mezővel.

## <a name="next-steps"></a>Következő lépések

A Távoli asztali szolgáltatások RDGateway szerepköre egy KDC proxy szolgáltatást tartalmaz. Lásd: [a RD-átjáró szerepkör telepítése a Windows rendszerű virtuális asztalon](rd-gateway-role.md) , hogy a Windows rendszerű virtuális asztali környezetnek hogyan kell beállítania egyet.
