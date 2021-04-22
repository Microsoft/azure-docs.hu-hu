---
title: BareMetal Infrastructure-példányok csatlakoztatása az Azure-ban
description: Megtudhatja, hogyan azonosíthat és használhat BareMetal-példányokat a Azure Portal Azure CLI-ban.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871650"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>BareMetal Infrastructure-példányok csatlakoztatása az Azure-ban

Ez a cikk bemutatja, [hogyan jeleníti Azure Portal](https://portal.azure.com/) [BareMetal-példányokat.](concepts-baremetal-infrastructure-overview.md) Ez a cikk azt is bemutatja, hogy mit tud Azure Portal az üzembe helyezett BareMetal Infrastructure-példányokkal. 
 
## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása
Az BareMetal-példányok Azure-erőforrás-szolgáltatója biztosítja a példányok láthatóságát a Azure Portal. Alapértelmezés szerint az BareMetal-példányok üzembe helyezéséhez használt Azure-előfizetés regisztrálja az *BareMetalInfrastructure* erőforrás-szolgáltatót. Ha nem látja az üzembe helyezett BareMetal-példányokat, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében. 

Az BareMetal-példány erőforrás-szolgáltatóját az Azure CLI vagy a Azure Portal regisztrálhatja.

### <a name="portal"></a>[Portál](#tab/azure-portal)
 
Fel kell sorolni az előfizetését a Azure Portal majd kattintson duplán az BareMetal-példányok üzembe helyezéséhez használt előfizetésre.
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

1. A Minden **szolgáltatás mezőbe írja** be a **előfizetést,** majd válassza az **Előfizetések lehetőséget.**

1. Válassza ki az előfizetést az előfizetések listájából.

1. Válassza **az Erőforrás-szolgáltatók lehetőséget,** és írja be az **BareMetalInfrastructure** struktúrát a keresésbe. Az erőforrás-szolgáltatónak **Regisztráltnak kell lennie,** ahogy a képen is látható.
 
>[!NOTE]
>Ha az erőforrás-szolgáltató nincs regisztrálva, válassza a **Regisztráció lehetőséget.**
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Képernyőkép a regisztrált BareMetal-példányról.":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdése:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Jelentkezzen be az BareMetal-példány üzembe helyezéséhez használt Azure-előfizetésbe az Azure CLI használatával. Regisztrálja `BareMetalInfrastructure` az erőforrás-szolgáltatót [az az provider register paranccsal:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Az az [provider list paranccsal](/cli/azure/provider#az_provider_list) az összes elérhető szolgáltatót láthatja.

---

További információ az erőforrás-szolgáltatókról: [Azure erőforrás-szolgáltatók és -típusok.](../azure-resource-manager/management/resource-providers-and-types.md)  

## <a name="baremetal-instances-in-the-azure-portal"></a>BareMetal-példányok a Azure Portal
 
Az BareMetal-példány üzembe helyezési kérésének elküldésekor meg kell adnia azt az Azure-előfizetést, amelyről az BareMetal-példányokhoz csatlakozik. Használja ugyanazt az előfizetést, mint az BareMetal-példányokkal használható alkalmazásréteg üzembe helyezéséhez.
 
Az BareMetal-példányok üzembe helyezése [](../azure-resource-manager/management/manage-resources-portal.md) során egy új Azure-erőforráscsoport jön létre az üzembe helyezési kérelemben használt Azure-előfizetésben. Ez az új erőforráscsoport felsorolja az előfizetésben üzembe helyezett összes BareMetal-példányt.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Az BareMetal előfizetésben a Azure Portal válassza az **Erőforráscsoportok lehetőséget.**
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Képernyőkép az erőforráscsoportok listájáról.":::

1. A listában keresse meg az új erőforráscsoportot.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Képernyőkép az BareMetal példányról egy szűrt erőforráscsoportok listájában." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Szűrhet az BareMetal-példány üzembe helyezéséhez használt előfizetésre. Miután a megfelelő előfizetésre szűrt, előfordulhat, hogy az erőforráscsoportok hosszú listájával rendelkezik. Keresse meg a **-Txxx** utójavítását, ahol a xxx három számjegyből áll, például **-T250.**

1. A részletek megtekintéséhez válassza ki az új erőforráscsoportot. A rendszerképen egy üzembe helyezett BareMetal-példány látható.
   
   >[!NOTE]
   >Ha több BareMetal-példánybérlőt helyezett üzembe ugyanabban az Azure-előfizetésben, több Azure-erőforráscsoportot fog látni.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az összes BareMetal-példányhoz futtassa [az az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) parancsot az erőforráscsoporthoz:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> A `--output` paraméter egy globális paraméter, amely minden parancshoz elérhető. A **tábla** értéke felhasználóbarát formában mutatja be a kimenetet. További információ: [Az Azure CLI-parancsok kimeneti formátumai.](/cli/azure/format-output-azure-cli)

---

## <a name="view-the-attributes-of-a-single-instance"></a>Egyetlen példány attribútumainak megtekintése

Megtekintheti egy példány részleteit.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Az BareMetal-példányok listájában válassza ki a megtekinteni kívánt példányt.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Egyetlen példány BareMetal-példányattribútumainak képernyőképe." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
A rendszerkép attribútumai nem különböziknek az Azure-beli virtuális gép (VM) attribútumaitól. A bal oldalon az Erőforráscsoport, az Azure-régió, valamint az előfizetés neve és azonosítója látható. Ha címkéket rendelt hozzá, itt is látni fogja őket. Alapértelmezés szerint az BareMetal-példányokhoz nincs hozzárendelve címke.
 
A jobb oldalon megjelenik az BareMetal-példány, az operációs rendszer (OS), az IP-cím és a termékváltozat neve, amely a processzorszálak és a memória számát mutatja. Az energiaállapotot és a hardververziót (az BareMetal-példánybélyeg változata) is látni fogja. Az energiaállapot azt jelzi, hogy a hardveregység be van-e kapcsolva vagy ki van-e kapcsolva. Az operációs rendszer részletei azonban nem jelzik, hogy működik-e.
 
A lehetséges hardver-változatok a következőek:

* 3. változat (3. változat)

* 4. változat (4. változat)
 
* 4.2-es változat (4.2-es változat)
 
>[!NOTE]
>A 4.2-es verzió a legújabb, operációs rendszer nélküli operációs rendszer nélküli infrastruktúra, amely a meglévő Rev 4 architektúrát használja. A 4. rev. közelebbi közelséget biztosít az Azure-beli virtuálisgép-gazdagépek számára. Jelentős fejlesztéseket tartalmaz az Azure-beli virtuális gépek és a virtuális gépek közötti hálózati SAP HANA között. Az BareMetal-példányokat a következő Azure Portal. További információ: [BareMetal Infrastructure on Azure ( Operációs rendszer nélküli infrastruktúra az Azure-ban).](concepts-baremetal-infrastructure-overview.md)

 
Emellett a jobb oldalon megtalálja az [Azure](../virtual-machines/co-location.md) közelségi elhelyezési csoport nevét, amely automatikusan létrejön minden üzembe helyezett BareMetal-példányhoz. Az alkalmazásréteget kiszolgáló Azure-beli virtuális gépek üzembe helyezésekor hivatkozhat a közelségi elhelyezési csoportra. Amikor az BareMetal-példányhoz társított közelségi elhelyezési csoportot használja, győződjön meg arról, hogy az Azure-beli virtuális gépek az BareMetal-példányhoz közel vannak üzembe telepítve.
 
>[!TIP]
>Ha az alkalmazásréteget ugyanabban az Azure-adatközpontban találja, mint a 4.x változatot, tekintse meg az Azure közelségi elhelyezési csoportjait az [optimális hálózati késés érdekében.](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egy BareMetal-példány részleteinek megtekintése az [az baremetalinstance show paranccsal:](/cli/azure/baremetalinstance#az_baremetalinstance_show)

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Ha nem biztos a példány nevében, futtassa `az baremetalinstance list` a fent leírt parancsot.

---
 
## <a name="check-activities-of-a-single-instance"></a>Egyetlen példány tevékenységeinek ellenőrzése
 
Egyetlen BareMetal-példány tevékenységeit ellenőrizheti. A rögzített fő tevékenységek egyike a példány újraindítása. A felsorolt adatok közé tartozik a tevékenység állapota, az aktivált tevékenység időbélyegzője, az előfizetés azonosítója és a tevékenységet aktiváló Azure-felhasználó.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Képernyőkép az BareMetal-példány tevékenységeiről." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
A példány Azure-beli metaadatainak módosításait a tevékenységnapló is rögzíti. A kezdeményezett újraindítás mellett az **BareMetallnstances** írása tevékenység is látható. Ez a tevékenység nem módosítja magát az BareMetal-példányt, de az egység azure-beli metaadatainak módosításait dokumentumokba is beemi.
 
Egy másik tevékenység, amely rögzítve lesz, az, amikor címkét [ad](../azure-resource-manager/management/tag-resources.md) hozzá vagy töröl egy példányhoz.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Azure-címke hozzáadása és törlése egy példányhoz

### <a name="portal"></a>[Portál](#tab/azure-portal)
 
Hozzáadhat Azure-címkéket egy BareMetal-példányhoz, vagy törölheti őket. A címkék hozzárendelése a virtuális gépekhez való hozzárendeléskor történik. Ahogy a virtuális gépeknél, a címkék is léteznek az Azure-metaadatokban. A címkékre ugyanazok a korlátozások vonatkoznak az BareMetal-példányok esetén, mint a virtuális gépekre.
 
A címkék törlése ugyanúgy működik, mint a virtuális gépeken. A címkék alkalmazása és törlése az BareMetal-példány tevékenységnaplójában található.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A címkék BareMetal-példányokhoz való hozzárendelése ugyanúgy működik, mint a virtuális gépek címkéinek hozzárendelése. Ahogy a virtuális gépeknél, a címkék is léteznek az Azure-metaadatokban. A címkékre ugyanazok a korlátozások vonatkoznak az BareMetal-példányok esetén, mint a virtuális gépekre.

Ha címkéket szeretne hozzáadni egy BareMetal-példányhoz, futtassa [az az baremetalinstance update](/cli/azure/baremetalinstance#az_baremetalinstance_update) parancsot:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Használja ugyanezt a parancsot egy címke eltávolításához:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Példány tulajdonságainak ellenőrzése
 
A példányok beszerzése után a Tulajdonságok szakaszban megtekintheti a példányokkal kapcsolatban gyűjtött adatokat. Az összegyűjtött adatok közé tartozik az Azure-kapcsolat, a tárolási háttér, az ExpressRoute-kapcsolatcsoport azonosítója, az egyedi erőforrás-azonosító és az előfizetés azonosítója. Ezeket az információkat a támogatási kérések során vagy a storage snapshot konfigurációjának beállításakor fogja használni.
 
Egy másik fontos információ, amely a tároló NFS IP-címe. Elkülöníti a tárolót a **bérlőjéhez** az BareMetal-példány vermében. Ezt az IP-címet fogja használni, amikor a tárolási pillanatképek biztonsági mentéséhez [szerkeszti](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)a konfigurációs fájlt.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Képernyőkép az BareMetal-példány tulajdonságbeállításiról." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>BareMetal-példány újraindítása a Azure Portal

Vannak olyan helyzetek, amikor az operációs rendszer nem fejezi be az újraindítást, amihez az BareMetal-példány energia-újraindítása szükséges.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A példány energia-újraindítását közvetlenül a következő Azure Portal:
 
Válassza **az Újraindítás,** majd az **Igen** lehetőséget az újraindítás megerősítéséhez.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Az BareMetal-példány újraindítását bemutató képernyőkép.":::
 
Amikor újraindít egy BareMetal-példányt, késést tapasztal. Ebben a késésben az energiaállapot **indításról** **Elindítva** állapotra oszl, ami azt jelenti, hogy az operációs rendszer teljesen elindult. Ennek eredményeképpen újraindítás után csak akkor tud bejelentkezni az egységbe, ha az állapot Elindítva **állapotra vált.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal-példány újraindításához használja [az az baremetalinstance restart](/cli/azure/baremetalinstance#az_baremetalinstance_restart) parancsot:

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>Az BareMetal-példányban a memória mennyiségétől függően a hardver és az operációs rendszer újraindítása akár egy órát is eltelhet.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Támogatási kérés megnyitása BareMetal-példányokhoz
 
Támogatási kéréseket küldhet kifejezetten BareMetal-példányokhoz.
1. A Azure Portal Súgó **és támogatás alatt** hozzon létre egy **[Új](https://rc.portal.azure.com/#create/Microsoft.Support)** támogatási kérést, és adja meg a következő információkat a jegyhez:
 
   - **Probléma típusa:** Válasszon ki egy problématípust.
 
   - **Előfizetés:** Válassza ki az előfizetését.
 
   - **Szolgáltatás:** BareMetal-infrastruktúra
 
   - **Erőforrás:** Adja meg a példány nevét.
 
   - **Összefoglalás:** Adja meg a kérés összegzését.
 
   - **Probléma típusa:** Válasszon ki egy problématípust.
 
   - **Probléma altípusa:** Válassza ki a probléma altípusát.

1. Válassza a **Megoldások** lapot, hogy megoldást találjon a problémára. Ha nem talál megoldást, lépjen a következő lépésre.

1. Válassza a **Részletek lapot,** és válassza ki, hogy a probléma virtuális gépekkel vagy BareMetal-példányokkal kapcsolatos-e. Ezek az információk segítenek a támogatási kérést a megfelelő szakembereknek irányítani.

1. Adja meg, hogy mikor kezdődött a probléma, és válassza ki a példány régióját.

1. Adjon meg további részleteket a kérésről, és szükség esetén töltsön fel egy fájlt.

1. Válassza **az Áttekintés + létrehozás lehetőséget** a kérés elküldését.
 
A kérelem megerősítése akár öt munkanapot is igénybe vesz.

## <a name="next-steps"></a>Következő lépések

További információ a számítási feladatokról:

- [Mi az SAP HANA az Azure-ban (nagy méretű példányok)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
