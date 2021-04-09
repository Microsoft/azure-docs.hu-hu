---
title: Privát hozzáférés engedélyezése privát hivatkozással (előzetes verzió) – portál
titleSuffix: Azure Digital Twins
description: A Azure Portal használatával megtudhatja, hogyan engedélyezheti a privát hozzáférést az Azure Digital Twins-megoldásokhoz privát hivatkozással.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100417832"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Privát hozzáférés engedélyezése privát kapcsolattal (előzetes verzió): Azure Portal

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Ez a cikk bemutatja, hogyan [engedélyezheti a privát hivatkozásokat az Azure Digital Twins-példányok privát végpontján](concepts-security.md#private-network-access-with-azure-private-link-preview) (jelenleg előzetes verzióban érhető el). Az Azure Digital Twins-példány privát végpontjának konfigurálása lehetővé teszi az Azure-beli digitális Twins-példányok védelmét, valamint a nyilvános kitettség kiiktatását, valamint az [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)származó adatok kiszűrése elkerülését.

Ez a cikk végigvezeti a folyamaton a [**Azure Portal**](https://portal.azure.com)használatával.

A cikk a következő lépésekkel foglalkozik: 
1. Kapcsolja be a privát hivatkozást, és állítson be egy privát végpontot egy Azure Digital Twins-példányhoz.
1. A nyilvános hálózati hozzáférés jelzőit letilthatja vagy engedélyezheti, ha csak a magánhálózati kapcsolatok API-hozzáférését szeretné korlátozni.

## <a name="prerequisites"></a>Előfeltételek

A privát végpontok beállítása előtt szüksége lesz egy [**Azure Virtual Networkra (VNet)**](../virtual-network/virtual-networks-overview.md) , ahol a végpont telepíthető. Ha még nem rendelkezik VNet, a beállításához kövesse az [Azure Virtual Network rövid](../virtual-network/quick-create-portal.md) útmutatóinak egyikét.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Privát végpont hozzáadása Azure Digital Twins-példányhoz 

A [Azure Portal](https://portal.azure.com)használatakor kiválaszthatja, hogy a példány kezdeti beállításának részeként be kell-e kapcsolni egy privát végpontot egy Azure digitális Twins-példányhoz, vagy később engedélyeznie kell egy olyan példányon, amely már létezik. 

Ezen létrehozási módszerek egyike ugyanazokat a konfigurációs beállításokat és a példányhoz tartozó végeredményt adja meg. Ez a szakasz leírja, hogyan végezheti el az egyes műveleteket.

>[!TIP]
> Privát kapcsolati végpontot is beállíthat a privát kapcsolati szolgáltatáson keresztül, az Azure Digital Twins-példányon keresztül. Ez ugyanazokat a konfigurációs beállításokat és a végeredményt is megadja.
>
> A magánhálózati kapcsolati erőforrások beállításával kapcsolatos további információkért tekintse meg a [Azure Portal](../private-link/create-private-endpoint-portal.md), az [Azure CLI](../private-link/create-private-endpoint-cli.md), az [ARM](../private-link/create-private-endpoint-template.md)vagy a [PowerShell](../private-link/create-private-endpoint-powershell.md)saját hivatkozásokkal kapcsolatos dokumentációját.

### <a name="add-a-private-endpoint-during-instance-creation"></a>Privát végpont hozzáadása a példány létrehozása során

Ebben a szakaszban egy, a jelenleg létrehozott Azure-beli digitális Twins-példány privát végpontján engedélyezheti a privát kapcsolatot. Ez a szakasz a létrehozási folyamat hálózatkezelési lépésére koncentrál; az új Azure Digital Twins-példányok létrehozásának teljes áttekintését lásd: [*útmutató: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md).

A magánhálózati kapcsolat beállításai a példány-telepítő **hálózatkezelés** lapján találhatók.

Ezen a lapon engedélyezheti a privát végpontokat a **kapcsolati módszerhez** tartozó **magánhálózati végpont** beállítás kiválasztásával.

Ez egy **privát végponti kapcsolatok** nevű szakaszt ad hozzá, ahol konfigurálhatja a privát végpont részleteit. A folytatáshoz kattintson a **+ Hozzáadás** gombra.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Képernyőkép az Azure Digital Twins-hoz készült erőforrás létrehozása párbeszédpanel hálózatkezelés lapján látható Azure Portalról. A lap neve, a magánhálózati végpont beállítása a kapcsolati módszerhez és a + Hozzáadás gomb egy új magánhálózati végponti kapcsolat létrehozásához." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Ekkor megnyílik egy oldal, amely egy új privát végpont adatait adja meg.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="A privát végpont létrehozása oldalon látható Azure Portal képernyőképe. Az alábbiakban ismertetett mezőket tartalmazza.":::

1. Adja meg az **előfizetéséhez** és az **erőforráscsoporthoz** tartozó beállításokat. Állítsa a **helyet** a használni kívánt VNet megegyező helyre. Válassza ki a végpont **nevét** , és a **cél alerőforrásokhoz** válassza az *API* lehetőséget.

1. Ezután válassza ki azt a **virtuális hálózatot** és **alhálózatot** , amelyet a végpont üzembe helyezéséhez használni szeretne.

1. Végül válassza ki, hogy szeretné-e **integrálni a magánhálózati DNS-zónával**. Használhatja az alapértelmezett **Igen** vagy, ha segítségre van szüksége ehhez a beállításhoz, a portálon található hivatkozásra kattintva [További információkat kaphat a magánhálózati DNS-integrációról](../private-link/private-endpoint-overview.md#dns-configuration).

A konfigurációs beállítások kitöltése után nyomja meg az **OK gombot** a befejezéshez.

Ez visszaadja az Azure Digital Twins-példány telepítőjének **hálózatkezelés** lapját, ahol az új végpontot a * * privát végponti kapcsolatok területen kell látni.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Képernyőkép az Azure Digital Twins-hoz készült erőforrás létrehozása párbeszédpanel hálózatkezelés lapján látható Azure Portalról. Az új privát végponti kapcsolatok és a navigációs gombok (áttekintés + létrehozás, előző, következő: speciális) láthatók." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Ezután az alsó navigációs gomb használatával folytathatja a példány többi beállítását.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Privát végpont hozzáadása meglévő példányhoz

Ebben a szakaszban engedélyezheti a privát kapcsolatot egy olyan Azure digitális Twins-példányhoz tartozó privát végponttal, amely már létezik.

1. Először navigáljon a [Azure Portal](https://portal.azure.com) egy böngészőben. Hozza létre az Azure digitális Twins-példányát úgy, hogy megkeresi a nevét a portál keresési sávján.

1. A bal oldali menüben válassza a **hálózatkezelés (előzetes verzió)** lehetőséget.

1. Váltson a **privát végpont kapcsolatai** lapra.

1. Válassza a **+ privát végpont** lehetőséget a **privát végpont létrehozása** beállítás megnyitásához.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Képernyőkép a Azure Portalről, amely az Azure Digital Twins-példány hálózatkezelés (előzetes verzió) lapját mutatja. A Private Endpoint Connections lap ki van jelölve, és a + Private Endpoint gomb is ki van emelve." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Az  **alapvető beállítások**   lapon adja meg vagy válassza ki a projekt **előfizetését** és **erőforrását** , valamint a végpont **nevét** és **régióját** . A régiónak meg kell egyeznie az Ön által használt VNet régióval.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Képernyőfelvétel a privát végpont létrehozása párbeszédpanel első (alapismeretek) lapjáról Azure Portal. A fent leírt mezőket tartalmazza.":::

    Ha elkészült, válassza a **Tovább: erőforrás >** gombot a következő lapra való ugráshoz.

1. Az **erőforrás** lapon adja meg vagy válassza ki az alábbi adatokat: 
    * **Kapcsolódási módszer**: válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban** lehetőséget az Azure Digital Twins-példány kereséséhez.
    * **Előfizetés**: adja meg az előfizetését.
    * **Erőforrás típusa**: válassza a **Microsoft. DigitalTwins/digitalTwinsInstances** lehetőséget
    * **Erőforrás**: válassza ki az Azure Digital Twins-példány nevét.
    * **Cél alerőforrása**: válassza az **API** lehetőséget.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="A privát végpont létrehozása párbeszédpanel második (erőforrás) lapját bemutató Azure Portal képernyőképe. A fent leírt mezőket tartalmazza.":::

    Ha elkészült, válassza a Next ( **Konfigurálás) >** gombot a következő lapra való ugráshoz.    

1. A **konfiguráció** lapon adja meg vagy válassza ki az alábbi adatokat:
    * **Virtuális hálózat**: válassza ki a virtuális hálózatot.
    * **Alhálózat**: válasszon egy alhálózatot a virtuális hálózatból.
    * **Integrálás a saját DNS-zónával**: válassza ki, hogy szeretné **-e integrálni a magánhálózati DNS-zónával**. Használhatja az alapértelmezett **Igen** vagy, ha segítségre van szüksége ehhez a beállításhoz, a portálon található hivatkozásra kattintva [További információkat kaphat a magánhálózati DNS-integrációról](../private-link/private-endpoint-overview.md#dns-configuration).
    Ha az **Igen** lehetőséget választja, meghagyhatja az alapértelmezett konfigurációs adatokat.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="A privát végpont létrehozása párbeszédpanel harmadik (konfiguráció) lapját bemutató Azure Portal képernyőképe. A fent leírt mezőket tartalmazza.":::

    Ha elkészült, a telepítés befejezéséhez kattintson a **felülvizsgálat + létrehozás** gombra. 

1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd kattintson a  **Létrehozás** gombra. 

A végpont üzembe helyezése után meg kell jelennie az Azure Digital Twins-példány privát végpont-kapcsolataiban.

>[!TIP]
> A végpont a Azure Portal privát kapcsolati központjából is megtekinthető.

## <a name="disable--enable-public-network-access-flags"></a>Nyilvános hálózati hozzáférési jelzők letiltása/engedélyezése

Beállíthatja az Azure Digital Twins-példányát az összes nyilvános kapcsolat megtagadásához, és a hálózati biztonság növelése érdekében csak privát végpontokon keresztül csatlakozhat a kapcsolatokhoz. Ez a művelet **nyilvános hálózati hozzáférési jelzővel** végezhető el. 

Ez a szabályzat lehetővé teszi, hogy csak a privát kapcsolati kapcsolatok API-hozzáférését korlátozza. Ha a nyilvános hálózati hozzáférés jelzője *Letiltva* értékre van állítva, akkor a nyilvános felhőből érkező összes REST API hívás az Azure digitális Twins-példányának adatsíkjával fog visszatérni `403, Unauthorized` . Azt is megteheti, hogy ha a házirend *le van tiltva* , és egy privát végponton keresztül kezdeményezi a kérést, az API-hívás sikeres lesz.

Ez a cikk bemutatja, hogyan frissítheti a hálózati jelző értékét a [Azure Portal](https://portal.azure.com)használatával. Az Azure CLI vagy a ARMClient parancssori eszközzel kapcsolatos utasításokért tekintse meg a jelen cikk [CLI-verzióját](how-to-enable-private-link-cli.md) .

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A [Azure Portal](https://portal.azure.com)nyilvános hálózati hozzáférésének letiltásához vagy engedélyezéséhez nyissa meg a portált, és navigáljon az Azure Digital Twins-példányához.

1. A bal oldali menüben válassza a **hálózatkezelés (előzetes verzió)** lehetőséget.

1. A **nyilvános hozzáférés** lapon állítsa be a **nyilvános hálózati hozzáférés engedélyezése** **Letiltva** vagy **az összes hálózat** lehetőséget.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Képernyőkép az Azure Digital Twins-példány hálózatkezelési (előzetes) oldalát bemutató Azure Portalról. A nyilvános hozzáférés lap ki van jelölve, és kiválaszthatja, hogy a nyilvános hálózati hozzáférés lehetőség is ki van-e jelölve." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

További információ az Azure-hoz készült privát hivatkozásról: 
* [*Mi az az Azure Private link Service?*](../private-link/private-link-service-overview.md)