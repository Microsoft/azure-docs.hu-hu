---
title: Felügyelt identitás engedélyezése útválasztási eseményekhez (előzetes verzió) – portál
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan engedélyezheti a rendszerhez rendelt identitást az Azure digitális Twins számára, és hogyan továbbíthatja az eseményeket a Azure Portal használatával.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5c216661e45e5ed34a95e8a56002f3ad175b089a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545911"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Felügyelt identitás engedélyezése az Azure Digital Twins-események útválasztásához (előzetes verzió): Azure Portal

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Ez a cikk azt ismerteti, hogyan engedélyezhető a [rendszer által hozzárendelt identitás egy Azure Digital Twins-példányhoz](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (jelenleg előzetes verzióban), és hogyan használható az identitás az események olyan támogatott célhelyekre való továbbításakor, mint az [Event Hub](../event-hubs/event-hubs-about.md), a [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   Destinations és az [Azure Storage tároló](../storage/blobs/storage-blobs-introduction.md).

Ez a cikk végigvezeti a folyamaton a [**Azure Portal**](https://portal.azure.com)használatával.

A cikk a következő lépésekkel foglalkozik: 

1. Hozzon létre egy Azure Digital Twins-példányt egy rendszer által hozzárendelt identitással, vagy engedélyezze a rendszer által hozzárendelt identitást egy meglévő Azure Digital Twins-példányon. 
1. Adja hozzá a megfelelő szerepkört vagy szerepköröket az identitáshoz. Rendelje hozzá például az **Azure Event hub Adatfeladói** szerepkört az identitáshoz, ha a végpont az Event hub, vagy ha a végpont Service Bus, **Azure Service Bus adatfeladói szerepkört** .
1. Hozzon létre egy végpontot az Azure Digital Ikrekben, amely képes a rendszerhez rendelt identitások használatára a hitelesítéshez.

## <a name="enable-system-managed-identities-for-an-instance"></a>Rendszer által felügyelt identitások engedélyezése egy példányhoz 

Ha engedélyezi a rendszer által hozzárendelt identitást az Azure Digital Twins-példányon, az Azure automatikusan létrehoz egy identitást a [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md)szolgáltatásban. Ezt az identitást ezután az Azure Digital Twins-végpontok hitelesítésére használhatja az események továbbításához.

**A példány kezdeti beállításának részeként** engedélyezheti a rendszer által felügyelt identitásokat egy Azure digitális Twins-példányhoz, vagy **később is engedélyezheti azt egy olyan példányon, amely már létezik**.

Ezen létrehozási módszerek egyike ugyanazokat a konfigurációs beállításokat és a példányhoz tartozó végeredményt adja meg. Ez a szakasz a mindkettőt ismerteti.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Rendszer által felügyelt identitás hozzáadása a példányok létrehozása során

Ebből a szakaszból megtudhatja, hogyan engedélyezheti a rendszer által felügyelt identitásokat egy jelenleg létrehozott Azure digitális Twins-példányon. Ez a szakasz a létrehozási folyamat felügyelt identitás lépésére koncentrál; az új Azure Digital Twins-példányok létrehozásának teljes áttekintését lásd: [*útmutató: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md).

A rendszer által felügyelt identitás beállítás a példány-telepítő **speciális** lapján található.

Ezen a lapon válassza a bekapcsolva lehetőséget a **rendszerfelügyelt identitás** **lehetőségre** a funkció bekapcsolásához.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Képernyőkép a Azure Portal az Azure Digital Twins erőforrás-létrehozási párbeszédpanelének Speciális lapjára mutat. A lap neve, az on beállítás a rendszerfelügyelt identitásnál és a navigációs gombok (áttekintés + létrehozás, előző, következő: speciális).":::

Ezután az alsó navigációs gomb használatával folytathatja a példány többi beállítását.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Rendszer által felügyelt identitás hozzáadása meglévő példányhoz

Ebben a szakaszban egy rendszer által felügyelt identitást ad hozzá egy már létező Azure digitális Twins-példányhoz.

1. Először navigáljon a [Azure Portal](https://portal.azure.com) egy böngészőben.

1. Keresse meg a példány nevét a portál keresési sávján, és válassza ki a részleteket a részletek megtekintéséhez.

1. A bal oldali menüben válassza az **identitás (előzetes verzió)** lehetőséget.

1. Ezen a lapon válassza a be **lehetőséget a** funkció bekapcsolásához.

1. Kattintson a **Save (Mentés** ) gombra, és erősítse meg az **Igen** gombot.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Képernyőfelvétel az Azure Digital Twins-példány identitás (előzetes verzió) lapjáról Azure Portalról. Az oldal neve az Azure Digital Twins-példány menüben, az állapot, a Mentés gomb és az igen megerősítő gomb alapján van kiemelve.":::

A módosítás mentése után további mezők jelennek meg ezen a lapon az új identitás **objektum-azonosítójának** és **engedélyeinek** megjelenítéséhez.

Szükség esetén átmásolhatja az **objektumazonosítót** , és az **engedélyek** gomb használatával megtekintheti az identitáshoz rendelt Azure-szerepköröket. Egyes szerepkörök beállításához folytassa a következő szakasszal.

## <a name="assign-azure-roles-to-the-identity"></a>Azure-szerepkörök kiosztása az identitáshoz 

Miután létrehozta a rendszer által hozzárendelt identitást az Azure Digital Twins-példányhoz, hozzá kell rendelnie a megfelelő szerepköröket a különböző típusú [végpontok](concepts-route-events.md) hitelesítéséhez az események támogatott célhelyekre való továbbításához. Ez a szakasz a szerepkör-beállításokat és azok hozzárendelését ismerteti a rendszer által hozzárendelt identitáshoz.

>[!NOTE]
> Ez egy fontos lépés – anélkül, hogy az identitás nem fog tudni hozzáférni a végpontokhoz, és az események nem lesznek leküldve.

### <a name="supported-destinations-and-azure-roles"></a>Támogatott célhelyek és Azure-szerepkörök 

Itt láthatók azok a minimális szerepkörök, amelyeknek az identitásnak hozzá kell férnie egy végponthoz a célhely típusától függően. A magasabb szintű engedélyekkel rendelkező szerepkörök (például az adattulajdonosi szerepkörök) is működni fognak.

| Cél | Azure-szerepkör |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs adatfeladó |
| Azure Service Bus | Adatfeladó Azure Service Bus |
| Azure Storage-tároló | Storage-blobadatok közreműködője |

További információ a végpontokról, az útvonalakról és az Azure digitális Ikrekben az útválasztáshoz támogatott célhelyekről: [*fogalmak: Event Routes*](concepts-route-events.md).

### <a name="assign-the-role"></a>A szerepkör kiosztása

>[!NOTE]
> Ezt a szakaszt egy Azure-felhasználónak kell elvégeznie, aki jogosult az Azure-erőforrásokhoz való felhasználói hozzáférés kezelésére (beleértve az engedélyek megadását és delegálását). A követelménynek megfelelő általános szerepkörök a *tulajdonos*, a *fiók rendszergazdája* vagy a *felhasználói hozzáférés rendszergazdája* és a *közreműködő* kombinációja. További információ az Azure Digital Twins szerepköreinek engedélyezési követelményeiről [*: útmutató: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements).

Ha szerepkört szeretne hozzárendelni az identitáshoz, először nyissa meg a [Azure Portal](https://portal.azure.com).

1. Navigáljon a végponti erőforráshoz (az Event hub, a Service Bus témakör vagy a Storage tárolóhoz), és keresse meg a nevét a portálon található keresősáv használatával. 
1. A bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Új szerepkör-hozzárendelés hozzáadásához kattintson a **+ Hozzáadás** gombra.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Képernyőfelvétel a Azure Portal az Event hub hozzáférés-vezérlés (IAM) lapjáról. A + Hozzáadás gomb ki van emelve." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. A szerepkör- **hozzárendelés hozzáadása** lapon adja meg a következő értékeket:
    * **Szerepkör**: válassza ki a kívánt szerepkört a legördülő menüből.
    * **Hozzáférés hozzárendelése**: a **rendszerhez rendelt felügyelt identitás** területen válassza a **digitális ikrek** elemet.
    * **Előfizetés**: Válassza ki előfizetését. Ez megjeleníti az összes Azure digitális Twins által felügyelt identitást a kiválasztott előfizetésen belül.
    * **Válassza ki**: Itt választhatja ki a szerepkörhöz hozzárendelt Azure digitális Twins-példány felügyelt identitását. A felügyelt identitás neve megegyezik a példány nevével, ezért válassza ki az Azure Digital Twins-példány nevét. Ha kiválasztja, a példány identitása megjelenik a panel alján található **kijelölt tagok** szakaszban.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="A felsorolt mezők kitöltése a &quot;szerepkör-hozzárendelés hozzáadása&quot; párbeszédpanelen":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Ha végzett a részletek beírásával, válassza a **Mentés** lehetőséget.

## <a name="create-an-endpoint-with-identity-based-authorization"></a>Végpont létrehozása identitás-alapú hitelesítéssel

Miután beállította a rendszer által felügyelt identitást az Azure Digital Twins-példányhoz, és hozzárendeli a megfelelő szerepkör (eke) t, létrehozhat olyan Azure digitális Twins- [végpontokat](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) , amelyek képesek az identitás hitelesítésre való használatára. Ez a beállítás csak az Event hub és a Service Bus típusú végpontok esetében érhető el (Event Grid esetén nem támogatott).

>[!NOTE]
> Az identitás-alapú hitelesítésre való váltáshoz olyan végpontot nem lehet szerkeszteni, amely a kulcs alapú identitással már létrejött. A végpont első létrehozásakor ki kell választania a hitelesítési típust.

Az utasításokat követve [hozzon létre egy Azure digitális Twins-végpontot](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Ha a végpont típusához szükséges részletek elvégzése lépésre lép, ügyeljen arra, hogy a hitelesítési típushoz válassza az **identitás-alapú** lehetőséget.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Képernyőkép az Event hub típusú végpont létrehozásáról." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Fejezze be a végpont beállítását, majd válassza a **Mentés** lehetőséget.

## <a name="considerations-for-disabling-system-managed-identities"></a>A rendszer által felügyelt identitások letiltásával kapcsolatos megfontolások

Mivel az identitást az azt használó végpontok külön kezelik, fontos figyelembe venni, hogy az identitás vagy szerepköre változásai milyen hatással lehetnek az Azure Digital Twins-példányban található végpontokra. Ha az identitás le van tiltva, vagy egy végponthoz szükséges szerepkört távolít el belőle, a végpont elérhetetlenné válhat, és a rendszer megszakítja az események folyamatát.

A már letiltott felügyelt identitással beállított végpontok használatának folytatásához törölnie kell a végpontot, és [újra létre kell hoznia](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) egy másik hitelesítési típussal. Akár egy órát is igénybe vehet, hogy az események a változás után folytassanak kézbesítést a végpontnak.

## <a name="next-steps"></a>Következő lépések

További információ a felügyelt identitásokról az Azure AD-ben: 
* [*Azure-erőforrások felügyelt identitásai*](../active-directory/managed-identities-azure-resources/overview.md)