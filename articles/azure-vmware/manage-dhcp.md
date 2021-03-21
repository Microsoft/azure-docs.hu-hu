---
title: DHCP kezelése Azure VMware-megoldáshoz
description: Ismerje meg, hogyan hozhat létre és kezelhet DHCP-t az Azure VMware-megoldás privát felhője számára.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708551"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>DHCP kezelése Azure VMware-megoldáshoz

A privát felhőalapú környezetekben futó alkalmazások és munkaterhelések DHCP-szolgáltatásokat igényelnek az IP-címek hozzárendeléseihez.  Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet DHCP-t az Azure VMware-megoldásban kétféleképpen:

- Ha a NSX-T használatával futtatja a DHCP-kiszolgálót, [létre kell hoznia egy DHCP-kiszolgálót és egy](#create-a-dhcp-server) [továbbítót az adott kiszolgálóra](#create-dhcp-relay-service). A DHCP-kiszolgáló létrehozásakor hozzá kell adnia egy hálózati szegmenst is, és meg kell adnia a DHCP IP-címtartományt.   

- Ha harmadik féltől származó külső DHCP-kiszolgálót használ a hálózaton, [létre kell hoznia a DHCP-továbbító szolgáltatást](#create-dhcp-relay-service). Ha egy DHCP-kiszolgálóhoz hoz létre továbbítót, függetlenül attól, hogy a NSX-T vagy egy külső gyártót használ a DHCP-kiszolgáló üzemeltetéséhez, meg kell adnia a DHCP IP-címtartományt.

>[!IMPORTANT]
>A DHCP nem működik a VMware HCX L2 stretch Network virtuális gépei (VM-EK) esetében, ha a DHCP-kiszolgáló a helyszíni adatközpontban található.  Alapértelmezés szerint a NSX letiltja az összes DHCP-kérést az L2 stretch bejárásával. A megoldáshoz tekintse [meg a DHCP-kérelmek küldése](#send-dhcp-requests-to-the-on-premises-dhcp-server) a helyszíni DHCP-kiszolgáló eljáráshoz című témakört.


## <a name="create-a-dhcp-server"></a>DHCP-kiszolgáló létrehozása

Ha a NSX-T használatával szeretné üzemeltetni a DHCP-kiszolgálót, hozzon létre egy DHCP-kiszolgálót. Ezután vegyen fel egy hálózati szegmenst, és adja meg a DHCP IP-címtartományt.

1. A NSX-T Managerben válassza a **hálózati**  >  **DHCP** lehetőséget, majd válassza a **kiszolgáló hozzáadása** elemet.

1. Válassza a **DHCP** lehetőséget a **kiszolgáló típusa mezőben**, adja meg a kiszolgáló nevét és IP-címét, majd kattintson a **Mentés** gombra.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. Válassza az **1. rétegbeli átjárók** lehetőséget, válassza ki a függőleges három pontot az 1. rétegbeli átjárón, majd válassza a **Szerkesztés** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Válassza ki a használni kívánt átjárót" border="true":::

1. Alhálózat hozzáadásához válassza a **nincs IP-foglalási csoport** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="alhálózat hozzáadása" border="true":::

1. A **Típus mezőben** válassza a **DHCP helyi kiszolgáló** lehetőséget. 
   
1. A **DHCP-kiszolgáló** esetében válassza az **alapértelmezett DHCP** lehetőséget, majd kattintson a **Mentés** gombra.

1. Válassza a **Mentés** újra, majd a **Szerkesztés Bezárás** lehetőséget.

### <a name="add-a-network-segment"></a>Hálózati szegmens hozzáadása

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>DHCP-továbbító szolgáltatás létrehozása

Ha harmadik féltől származó külső DHCP-kiszolgálót szeretne használni, létre kell hoznia egy DHCP-továbbító szolgáltatást. A DHCP IP-címtartományt is megadja a NSX-T kezelőjében. 

1. A NSX-T Managerben válassza a **hálózati**  >  **DHCP** lehetőséget, majd válassza a **kiszolgáló hozzáadása** elemet.

1. Válassza a **DHCP-továbbító** lehetőséget a **kiszolgáló típusa** mezőben, adja meg a kiszolgáló nevét és IP-címét, majd kattintson a **Mentés** gombra.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP-továbbító szolgáltatás létrehozása" border="true":::

1. Válassza az **1. rétegbeli átjárók** lehetőséget, válassza ki a függőleges három pontot az 1. rétegbeli átjárón, majd válassza a **Szerkesztés** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="1. szintű átjáró szerkesztése" border="true":::

1. Az IP-címek kiosztásának definiálásához válassza a **nincs IP-foglalási készlet** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="IP-címek kiosztásának szerkesztése" border="true":::

1. A **Típus mezőben** válassza a **DHCP-kiszolgáló** lehetőséget. 
   
1. A **DHCP-kiszolgáló** esetében válassza a **DHCP-továbbító** lehetőséget, majd kattintson a **Mentés** gombra.

1. Válassza a **Mentés** újra, majd a **Szerkesztés Bezárás** lehetőséget.


## <a name="specify-the-dhcp-ip-address-range"></a>A DHCP IP-címtartomány meghatározása

1. A NSX-T Managerben válassza a **hálózati**  >  **szegmensek** lehetőséget. 
   
1. Válassza ki a szegmens neve függőleges három pontját, és válassza a **Szerkesztés** lehetőséget.
   
1. Válassza az **alhálózatok beállítása** lehetőséget az alhálózat DHCP IP-címének megadásához. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="hálózati szegmensek" border="true":::
      
1. Szükség esetén módosítsa az átjáró IP-címét, majd adja meg a DHCP-tartomány IP-címét. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="alhálózatok szerkesztése" border="true":::
      
1. Válassza az **alkalmaz**, majd a **Mentés** lehetőséget. A szegmenshez hozzá van rendelve egy DHCP-kiszolgáló készlet.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="A szegmenshez hozzárendelt DHCP-kiszolgáló készlete" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>DHCP-kérések küldése a helyszíni DHCP-kiszolgálónak

Ha az Azure VMware Megoldásbeli virtuális gépekről DHCP-kéréseket szeretne küldeni az L2 kiterjesztett szegmensen a helyszíni DHCP-kiszolgálóra, létre kell hoznia egy biztonsági szegmens profilt. 

1. Jelentkezzen be a helyszíni vCenter, és a Home ( **Kezdőlap**) területen válassza a **HCX** lehetőséget.

1. Válassza a **hálózati bővítmény** lehetőséget a **szolgáltatások** területen.

1. Válassza ki azt a hálózati bővítményt, amelyet támogatni kíván az Azure VMware-megoldásból a helyszíni rendszerre irányuló DHCP-kérések támogatásához. 

1. Jegyezze fel a célként megadott hálózatnév nevét.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Képernyőkép a VMware vSphere-ügyfél hálózati bővítményeiről" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Az Azure VMware megoldás NSX-T kezelőjében válassza a **hálózati**  >  **szegmensek**  >  **szegmens profilok** lehetőséget. 

1. Válassza a **szegmens profil hozzáadása** , majd a **szegmens biztonság** lehetőséget.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="A szegmens profil hozzáadása a NSX-T-ben – képernyőkép" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Adjon meg egy nevet és egy címkét, majd állítsa be a **BPDU szűrő** kapcsolót a be értékre, és az összes DHCP-váltás kikapcsolva értékre.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="A BPDU szűrő bekapcsolását és a DHCP-váltást ábrázoló képernyőkép" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Távolítsa el az összes MAC-címet, ha van ilyen, a **BPDU szűrő engedélyezési listájában**.  Kattintson a **Mentés** gombra.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="A BPDU szűrő engedélyezési listán szereplő MAC-címeket bemutató képernyőkép":::

1. A **hálózati**  >  **szegmensek**  >  **szegmensek** területen, a keresés területen adja meg a definíciós hálózat nevét.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Képernyőfelvétel a hálózatkezelés > szegmensek szűrő mezőről":::

1. Válassza ki a szegmens neve függőleges három pontját, és válassza a **Szerkesztés** lehetőséget.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="A szegmens szerkesztési gombjának képernyőképe" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Módosítsa a **szegmensek biztonságát** a korábban létrehozott szegmens-profilra.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="A szegmens biztonsága mező képernyőképe" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Következő lépések

További információ a [gazdagép-karbantartásról és az életciklus-kezelésről](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).