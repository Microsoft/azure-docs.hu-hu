---
title: NSX hálózati összetevők konfigurálása az Azure VMware-megoldásban
description: Ismerje meg, hogyan konfigurálhatja az NSX-T hálózati szegmenseket az Azure VMware-megoldás konzoljának használatával.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 0478582a9bc4fb77a1784c27ec4f5c302d6b89fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716997"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>NSX hálózati összetevők konfigurálása az Azure VMware-megoldásban

Az Azure VMware-megoldás privát felhője alapértelmezés szerint a NSX-T szoftveresen definiált hálózatként (SDDC) érhető el. Előre kiépítve egy NSX-T réteg-0 átjáróval aktív/aktív módban, valamint egy alapértelmezett NSX-T 1. rétegbeli átjáró aktív/készenléti módban.  Ezek az átjárók lehetővé teszik a szegmensek (logikai kapcsolók) összekapcsolását, valamint East-West és North-South kapcsolatok biztosítását. 

Miután telepítette az Azure VMware-megoldás saját felhőjét, a számítási **feladatok hálózata** területen konfigurálhatja a szükséges NSX-T objektumokat az Azure VMware-megoldás konzolján.  A konzol a NSX-T műveletek egyszerűsített nézetét jeleníti meg, amelyeket a VMware-rendszergazdának naponta kell megcéloznia, és a NSX-T-T nem ismerő felhasználók számára.  

Négy lehetőség közül választhat a NSX-T összetevők konfigurálásához az Azure VMware-megoldás konzolján:
- **Szegmensek** – a NSX-T kezelőben és a vCenter-ben megjelenő szegmensek létrehozása.
- **DHCP** – DHCP-kiszolgáló vagy DHCP-továbbító létrehozása, ha azt tervezi, hogy DHCP-t használ.
- **Portok tükrözése** – a portok tükrözésének létrehozása a hálózati problémák elhárításához.
- **DNS** – hozzon létre egy DNS-továbbítót a DNS-kérések küldésére egy kijelölt DNS-kiszolgálónak a feloldáshoz.  

>[!NOTE]
>Továbbra is hozzáférhet a NSX-T Manager konzolhoz, ahol a speciális beállítások és más NSX-T funkciók is használhatók. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Képernyőfelvétel: az Azure VMware megoldás konzolján négy lehetőség látható az NSX-T konfigurálásához.":::

## <a name="prerequisites"></a>Előfeltételek
Az Azure VMware-megoldás privát felhőbe létrehozott vagy áttelepített virtuális gépeket (VM-EK) egy szegmenshez kell csatolni. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>NSX-T szegmens létrehozása a Azure Portalban
NSX-T szegmenst hozhat létre és konfigurálhat a Azure Portal Azure VMware-megoldás konzolján.  Ezek a szegmensek az alapértelmezett 1. rétegbeli átjáróhoz csatlakoznak, és az ezeken a szegmensekben lévő munkaterhelések East-West és North-South kapcsolatot kapnak. A szegmens létrehozása után az megjelenik a NSX-T Managerben és a vCenter.

>[!NOTE]
>Ha DHCP-T szeretne használni, [konfigurálnia kell egy DHCP-kiszolgálót vagy DHCP-továbbítót](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) , mielőtt NSX-T-szegmenseket hozna létre és konfigurál.

1. Az Azure VMware-megoldás saját felhőben a **munkaterhelés hálózat** területen válassza a **szegmensek**  >  **Hozzáadás** lehetőséget. Adja meg az új logikai szegmens részleteit, és kattintson **az OK gombra**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Az új szegmens hozzáadását bemutató képernyőkép.":::
   
   - **Szegmens neve** – a vCenter látható logikai kapcsoló neve.
   - **Alhálózati átjáró** – átjáró IP-címe a logikai kapcsoló alhálózatához alhálózati maszkkal. A virtuális gépek logikai kapcsolóhoz vannak csatolva, és az ehhez a kapcsolóhoz csatlakozó összes virtuális gép ugyanahhoz az alhálózathoz tartozik.  Emellett az ehhez a logikai szegmenshez csatolt összes virtuális gépnek ugyanahhoz a szegmenshez tartozó IP-címet kell továbbítania.
   - **DHCP** (nem kötelező) – egy logikai szegmens DHCP-tartományai. DHCP- [kiszolgálót vagy DHCP-továbbítót](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) úgy kell konfigurálni, hogy a szegmenseken DHCP-t használjanak.  
   - **Csatlakoztatott átjáró**  -  *Alapértelmezés szerint ki van választva, és csak olvasható.*  1. rétegbeli átjáró és a szegmens információ típusa. 
      - **T1** – az 1. rétegbeli átjáró neve a NSX-T kezelőjében. Az Azure VMware-megoldás saját felhője egy NSX-T réteg-0 átjáró aktív/aktív módban, valamint egy alapértelmezett NSX-T 1. rétegbeli átjáró aktív/készenléti módban.  Az Azure VMware-megoldás konzolján létrehozott szegmensek csak az alapértelmezett 1. rétegbeli átjáróhoz csatlakoznak, a szegmensek munkaterhelései pedig East-West és North-South kapcsolattal rendelkeznek. A NSX-T Manager használatával csak további 1. rétegbeli átjárókat hozhat létre. Az NSX-T Manager konzolból létrehozott 1. szintű átjárók nem láthatók az Azure VMware Solution konzolon. 
      - Az Azure VMware-megoldás által támogatott **típus** -átfedési szegmens.

   A szegmens már látható az Azure VMware Solution Console, a NSX-T Manger és a vCenter között.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>DHCP-kiszolgáló vagy DHCP-továbbító létrehozása a Azure Portalban
DHCP-kiszolgálót vagy továbbítót közvetlenül a Azure Portal Azure VMware-megoldás konzolján hozhat létre. A DHCP-kiszolgáló vagy-továbbító csatlakozik az 1. rétegbeli átjáróhoz, amely az Azure VMware-megoldás telepítésekor jön létre. Az összes szegmens, ahol a DHCP-tartományokat adta, a DHCP része lesz.  A DHCP-kiszolgáló vagy DHCP-továbbító létrehozása után meg kell határoznia egy alhálózatot vagy tartományt a szegmens szintjén a használathoz.

1. Az Azure VMware-megoldás saját felhőben a **munkaterhelés hálózat** területen válassza a **DHCP-**  >  **Hozzáadás** lehetőséget.

2. Válassza a **DHCP-kiszolgáló** vagy a **DHCP-továbbító** lehetőséget, majd adja meg a kiszolgáló vagy a továbbító nevét, valamint három IP-címet. 

   >[!NOTE]
   >A DHCP-továbbító esetében a sikeres konfiguráláshoz csak egy IP-cím szükséges.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="A DHCP-kiszolgáló vagy DHCP-továbbító Azure VMware-megoldásokban való hozzáadását bemutató képernyőkép.":::

4. A DHCP-beállításokat a [logikai szegmensek DHCP-címtartományok](#create-an-nsx-t-segment-in-the-azure-portal) megadásával, majd az **OK gombra** kattintva végezheti el.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>A portok tükrözésének konfigurálása a Azure Portalban
A portok tükrözését beállíthatja a hálózati forgalom figyelésére, amely magában foglalja az egyes csomagok másolatának továbbítását az egyik hálózati kapcsoló portjáról a másikra. Ez a beállítás a tükrözött adatforrást fogadó porton helyezi át a protokoll-elemzőt. Elemzi a forgalmat a forrástól, a virtuális gépről vagy a virtuális gépek csoportjából, majd egy meghatározott célhelyre továbbítja. 

A portok tükrözésének beállításához az Azure VMware-megoldás konzolján a következőkre lesz szüksége:

* [1. lépés. Forrás-és cél virtuális gépek vagy virtuálisgép-csoportok létrehozása](#step-1-create-source-and-destination-vms-or-vm-groups) – a forrásoldali csoport egyetlen vagy több virtuális géppel rendelkezik, ahol a forgalom tükrözött.

* [2. lépés. Port tükrözési profil létrehozása](#step-2-create-a-port-mirroring-profile) – megadhatja a forrás és a cél virtuálisgép-csoportok forgalmi irányát.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>1. lépés Forrás-és cél virtuális gépek vagy virtuálisgép-csoportok létrehozása

Ebben a lépésben létrehoz egy forrásoldali virtuálisgép-csoportot és egy cél virtuálisgép-csoportot.

1. Az Azure VMware-megoldás saját felhőben a **munkaterhelés hálózat** területen válassza a **portok tükrözése** virtuálisgép-  >  **csoportok**  >  **Hozzáadás** lehetőséget.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Képernyőfelvétel: virtuálisgép-csoport létrehozása a portok tükrözéséhez.":::

1. Adja meg az új virtuálisgép-csoport nevét, válassza ki a kívánt virtuális gépeket a listából, majd kattintson az **OK gombra**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="A virtuálisgép-csoportba felvenni kívánt virtuális gépek listáját bemutató képernyőkép.":::

1. A cél virtuálisgép-csoport létrehozásához ismételje meg ezeket a lépéseket.

### <a name="step-2-create-a-port-mirroring-profile"></a>2. lépés Port tükrözési profil létrehozása

Ebben a lépésben meg kell adnia egy profilt a forrás és a cél virtuálisgép-csoportok forgalmi irányához.

>[!NOTE]
>Győződjön meg arról, hogy a forrás és a cél virtuálisgép-csoportokat is létrehozta.

1. Válassza a **portok tükrözése**  >  **Hozzáadás** lehetőséget, majd adja meg a következőket:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Képernyőfelvétel: a port tükrözési profiljához szükséges információk.":::

   - **Port tükrözési neve** – leíró név a profilhoz.
   - **Irány** – válassza a bejövő, kimenő vagy kétirányú lehetőséget.
   - **Forrás** – válassza ki a forrásként szolgáló virtuálisgép-csoportot.
   - **Cél** – válassza ki a cél virtuálisgép-csoportot.
   - **Leírás** – adja meg a port tükrözésének leírását.

1. A profil befejezéséhez kattintson **az OK gombra** . 

   A profil és a virtuálisgép-csoportok az Azure VMware Solution konzolon láthatók.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>DNS-továbbító konfigurálása a Azure Portal
Konfigurálnia kell egy DNS-továbbítót, amelyben adott DNS-kérelmeket továbbítanak a rendszer a kijelölt DNS-kiszolgálónak a feloldáshoz.  A DNS-továbbító társítva van egy **alapértelmezett DNS-zónával** és legfeljebb három **teljes tartománynevet tartalmazó zónával**.

>[!TIP]
>A [DNS-továbbítót a NSX-T Manager konzol használatával is konfigurálhatja](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Ha DNS-továbbítót szeretne beállítani az Azure VMware-megoldás konzolján, a következőkre lesz szüksége:

* [1. lépés. Alapértelmezett DNS-zóna és FQDN-zóna konfigurálása](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – DNS-lekérdezés fogadásakor a DNS-továbbító összehasonlítja a tartománynevet a teljes tartománynév DNS-zónájában lévő tartománynévvel. 

* [2. lépés. DNS-szolgáltatás konfigurálása](#step-2-configure-dns-service) – konfigurálja a DNS-továbbító szolgáltatást.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>1. lépés Alapértelmezett DNS-zóna és FQDN-zóna konfigurálása
Az alapértelmezett DNS-zónát és FQDN-zónát úgy konfigurálja, hogy DNS-lekérdezéseket küldjön a felsőbb rétegbeli kiszolgálónak.  DNS-lekérdezés fogadásakor a DNS-továbbító összehasonlítja a lekérdezésben szereplő tartománynevet a FQDN DNS-zónák tartománynevével. Ha a rendszer egyezést talál, a rendszer továbbítja a lekérdezést a teljes tartománynév DNS-zónájában megadott DNS-kiszolgálókra. Ha nem talál egyezést, a rendszer továbbítja a lekérdezést az alapértelmezett DNS-zónában megadott DNS-kiszolgálókra.

>[!NOTE]
>A teljes tartománynév megadása előtt meg kell adni egy alapértelmezett DNS-zónát. 

1. Az Azure VMware-megoldás saját felhőben a **munkaterhelés hálózat** területen válassza a **DNS**  >  **DNS-zónák**  >  **Hozzáadás** lehetőséget.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="A DNS-zónák és a DNS-szolgáltatások hozzáadását bemutató képernyőkép.":::

1. Válassza az **alapértelmezett DNS-zóna** lehetőséget, és adja meg a következőket

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Az alapértelmezett DNS-zóna hozzáadását bemutató képernyőkép.":::

   1. A DNS-zóna neve.

   1. Legfeljebb három DNS-kiszolgáló IP-címe a **8.8.8.8** formátumban.

1. Válassza ki az **FQDN zónát** , és adja meg a következőket:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="A teljes tartománynevet tartalmazó zóna hozzáadását bemutató képernyőkép. ":::

   1. A DNS-zóna neve.

   1. A teljes tartománynév tartománya.

   1. Legfeljebb három DNS-kiszolgáló IP-címe a **8.8.8.8** formátumban.

1. Az alapértelmezett DNS-zóna és DNS-szolgáltatás hozzáadásának befejezéséhez kattintson **az OK gombra** .

### <a name="step-2-configure-dns-service"></a>2. lépés DNS-szolgáltatás konfigurálása

1. Válassza a **DNS-szolgáltatás** lapot, majd válassza a **Hozzáadás** lehetőséget. Adja meg a részleteket, és kattintson **az OK gombra**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="A DNS-szolgáltatáshoz szükséges adatokat bemutató képernyőkép.":::

   >[!TIP]
   >Az **1. rétegbeli átjáró** alapértelmezés szerint ki van választva, és az Azure VMware-megoldás telepítésekor létrehozott átjárót tükrözi.

   A DNS-szolgáltatás hozzáadása sikeresen megtörtént.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="A DNS-szolgáltatás sikeres hozzáadását bemutató képernyőkép.":::

