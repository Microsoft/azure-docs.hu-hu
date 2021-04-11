---
title: BareMetal-infrastruktúra-példányok összekapcsolása az Azure-ban
description: Ismerje meg, hogyan azonosíthatja és kezelheti a BareMetal-példányokat a Azure Portal vagy az Azure CLI-ben.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: a7fdc17aa4271915f7dc02aaa2d7a688016bf892
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579125"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>BareMetal-infrastruktúra-példányok összekapcsolása az Azure-ban

Ez a cikk bemutatja, hogyan jeleníti meg a [Azure Portal](https://portal.azure.com/) a [BareMetal-példányokat](concepts-baremetal-infrastructure-overview.md). Ez a cikk azt is bemutatja, hogy mit tehet a Azure Portal az üzembe helyezett BareMetal-infrastruktúra-példányokkal. 
 
## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása
A BareMetal-példányok Azure-erőforrás-szolgáltatója biztosítja a Azure Portal példányainak láthatóságát. Alapértelmezés szerint a BareMetal példányok üzembe helyezéséhez használt Azure-előfizetés regisztrálja a *BareMetalInfrastructure* erőforrás-szolgáltatót. Ha nem látja az üzembe helyezett BareMetal-példányokat, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében. 

A BareMetal-példány erőforrás-szolgáltatóját a Azure Portal vagy az Azure CLI használatával regisztrálhatja.

### <a name="portal"></a>[Portál](#tab/azure-portal)
 
Az előfizetést fel kell sorolnia a Azure Portal, majd duplán a BareMetal-példányok telepítéséhez használt előfizetésre kell kattintania.
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

1. A **minden szolgáltatás** mezőben adja meg az **előfizetés** elemet, majd válassza az **előfizetések** lehetőséget.

1. Válassza ki az előfizetést az előfizetések listájából.

1. Válassza az **erőforrás-szolgáltatók** lehetőséget, és írja be a **BareMetalInfrastructure** a keresésbe. Az erőforrás-szolgáltatót **regisztrálni** kell, ahogy a képen látható.
 
>[!NOTE]
>Ha az erőforrás-szolgáltató nincs regisztrálva, válassza a **regisztráció** lehetőséget.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Képernyőfelvétel: a regisztrált BareMetal-példányok.":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdéséhez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Jelentkezzen be a BareMetal példány üzembe helyezéséhez használt Azure-előfizetésbe az Azure CLI-n keresztül. Regisztrálja az `BareMetalInfrastructure` erőforrás-szolgáltatót az az [Provider Register](/cli/azure/provider#az_provider_register) paranccsal:

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Az az [Provider List](/cli/azure/provider#az_provider_list) parancs használatával megtekintheti az összes elérhető szolgáltatót.

---

További információ az erőforrás-szolgáltatókkal kapcsolatban: [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>BareMetal-példányok a Azure Portal
 
BareMetal-példány központi telepítési kérelmének elküldésekor meg kell adnia azt az Azure-előfizetést, amelyhez az BareMetal-példányokhoz csatlakozik. Használja ugyanazt az előfizetést, amelyet az BareMetal-példányokon futó alkalmazás rétegének telepítéséhez használ.
 
A BareMetal-példányok üzembe helyezése során új [Azure-erőforráscsoport](../azure-resource-manager/management/manage-resources-portal.md) jön létre a telepítési kérelemben használt Azure-előfizetésben. Ez az új erőforráscsoport felsorolja az előfizetésben üzembe helyezett összes BareMetal-példányt.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Az BareMetal-előfizetés Azure Portaljában válassza az **erőforráscsoportok** lehetőséget.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Az erőforráscsoportok listáját bemutató képernyőkép.":::

1. Keresse meg az új erőforráscsoportot a listában.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Képernyőfelvétel a BareMetal-példányról a szűrt erőforráscsoportok listájában." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Szűrheti a BareMetal-példány üzembe helyezéséhez használt előfizetést. A megfelelő előfizetésre való szűrés után előfordulhat, hogy az erőforráscsoportok hosszú listája van. Keressen egy javítást a **TXXX** , ahol XXX három számjegy, például a **-T250**.

1. A részletek megtekintéséhez válassza ki az új erőforráscsoportot. A képen egy telepített BareMetal-példány látható.
   
   >[!NOTE]
   >Ha több BareMetal-példány bérlőjét telepítette ugyanazon az Azure-előfizetésen belül, több Azure-erőforráscsoport jelenik meg.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az összes BareMetal-példány megjelenítéséhez futtassa az az [baremetalinstance List](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) parancsot az erőforráscsoporthoz:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> A `--output` paraméter egy globális paraméter, amely az összes parancs számára elérhető. A **tábla** értéke rövid formátumban jeleníti meg a kimenetet. További információ: [Az Azure CLI-parancsok kimeneti formátumai](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Egyetlen példány attribútumainak megtekintése

Egyetlen példány részleteit is megtekintheti.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A BareMetal-példányok listájában válassza ki a megtekinteni kívánt egyetlen példányt.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Képernyőfelvétel: egyetlen példány BareMetal-példányának attribútumai." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
A rendszerképben szereplő attribútumok nem sokban különböznek az Azure virtuális gép (VM) attribútumaitól. A bal oldalon láthatja az erőforráscsoportot, az Azure-régiót és az előfizetés nevét és AZONOSÍTÓját. Ha címkéket rendelt hozzá, akkor itt is láthatja őket. Alapértelmezés szerint a BareMetal-példányokhoz nincsenek hozzárendelve címkék.
 
A jobb oldalon látni fogja a BareMetal-példány, az operációs rendszer (OS), az IP-cím és az SKU nevét, amely a CPU-szálak és a memória számát jeleníti meg. Ekkor megjelenik az energiagazdálkodási állapot és a hardver verziója is (az BareMetal-példány bélyegzője). A tápellátási állapot azt jelzi, hogy a hardvereszköz be van-e kapcsolva vagy ki van-e kapcsolva. Az operációs rendszer részletei azonban nem jelzik, hogy működik-e.
 
A lehetséges hardver-változatok a következők:

* 3. változat (Rev 3)

* 4. változat (Rev 4)
 
* 4,2-es változat (Rev 4,2)
 
>[!NOTE]
>A Rev 4,2 a legújabb, a meglévő Rev 4 architektúrát használó BareMetal-infrastruktúra. A Rev 4 Az Azure-beli virtuális gépek (VM) gazdagépei számára biztosít szorosabb közelséget. Jelentős mértékben javítja az Azure-beli virtuális gépek és a SAP HANA példányok közötti hálózati késést. A BareMetal-példányok a Azure Portalon keresztül érhetők el és kezelhetők. További információ: BareMetal- [infrastruktúra az Azure](concepts-baremetal-infrastructure-overview.md)-ban.

 
Emellett a jobb oldalon megtalálja az [Azure Proximity-elhelyezési csoport](../virtual-machines/co-location.md) nevét, amely automatikusan létrejön az egyes telepített BareMetal-példányok esetében. Az alkalmazás réteget futtató Azure-beli virtuális gépek üzembe helyezése esetén hivatkozzon a közelségi csoportra. Ha a BareMetal-példányhoz társított közelségi elhelyezési csoportot használja, gondoskodjon arról, hogy az Azure-beli virtuális gépek üzembe helyezése a BareMetal-példány közelében történjen.
 
>[!TIP]
>Ha az alkalmazás rétegét ugyanabban az Azure-adatközpontban szeretné megkeresni, mint a 4. x változatot, tekintse meg az [Azure Proximity elhelyezési csoportok az optimális hálózati késés](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)érdekében

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A BareMetal-példány részleteinek megtekintéséhez futtassa az az [baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) parancsot:

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Ha nem biztos benne, hogy a példány neve, futtassa a `az baremetalinstance list` fent ismertetett parancsot.

---
 
## <a name="check-activities-of-a-single-instance"></a>Egyetlen példány tevékenységének ellenőrzését
 
Egyetlen BareMetal-példány tevékenységeit is megtekintheti. Az egyik fő tevékenység a példány újraindítása. A felsoroltak közé tartozik a tevékenység állapota, az elindított tevékenység időbélyege, az előfizetés azonosítója, valamint a tevékenységet indító Azure-felhasználó.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="A BareMetal-példány tevékenységeit ábrázoló képernyőkép." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
A példány metaadatait az Azure-ban a tevékenység naplójában is rögzíti a rendszer. Az újraindítás után megtekintheti az **írási BareMetallnstances** tevékenységeit. Ez a tevékenység nem végez módosításokat a BareMetal-példányon, de az egység metaadatait az Azure-ban dokumentálja.
 
Egy másik tevékenység, amely rögzítésre kerül, amikor hozzáad vagy töröl egy [címkét](../azure-resource-manager/management/tag-resources.md) egy példányhoz.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Azure-címke hozzáadása és törlése egy példányhoz

### <a name="portal"></a>[Portál](#tab/azure-portal)
 
Hozzáadhat Azure-címkéket egy BareMetal-példányhoz, vagy törölheti is azokat. A címkék ugyanúgy lesznek hozzárendelve, mint amikor címkéket rendelnek a virtuális gépekhez. A virtuális gépekhez hasonlóan a címkék az Azure-metaadatokban is szerepelnek. A címkék ugyanazok a korlátozások vonatkoznak a BareMetal-példányokra, mint a virtuális gépek esetében.
 
A címkék törlése ugyanúgy működik, mint a virtuális gépek esetében. A címkék alkalmazásával és törlésével kapcsolatban a BareMetal-példány tevékenységi naplójában szerepel.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A címkék BareMetal-példányokhoz rendelése ugyanúgy működik, mint a virtuális gépekhez tartozó címkék kiosztása. A virtuális gépekhez hasonlóan a címkék az Azure-metaadatokban is szerepelnek. A címkék ugyanazok a korlátozások vonatkoznak a BareMetal-példányokra, mint a virtuális gépek esetében.

Ha címkéket szeretne hozzáadni egy BareMetal-példányhoz, futtassa az az [baremetalinstance Update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) parancsot:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Egy címke eltávolításához használja ugyanazt a parancsot:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Példány tulajdonságainak megtekintése
 
A példányok beolvasásakor a Properties (Tulajdonságok) szakaszra kattintva megtekintheti a példányokkal kapcsolatban gyűjtött adatokat. Az összegyűjtött adatok közé tartozik az Azure-kapcsolat, a tárolási háttér, a ExpressRoute áramkör-azonosító, az egyedi erőforrás-azonosító és az előfizetés-azonosító. Ezeket az információkat a támogatási kérelmekben vagy a tárolási Pillanatképek konfigurációjának beállításakor fogja használni.
 
Egy másik fontos információ, amelyet látni fog a Storage NFS IP-címe. Elkülöníti a tárolót a **bérlőhöz** a BareMetal-példány veremben. Ezt az IP-címet akkor fogja használni, ha szerkeszti a [tárolási Pillanatképek biztonsági másolatait tartalmazó konfigurációs fájlt](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Képernyőfelvétel a BareMetal-példány tulajdonságainak beállításairól." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>BareMetal-példány újraindítása a Azure Portal

Különböző helyzetekben az operációs rendszer nem fejezi be az újraindítást, amelyhez a BareMetal-példány újraindítása szükséges.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A példány Power restartjét közvetlenül a Azure Portalról végezheti el:
 
Válassza az **Újraindítás** lehetőséget, majd az **Igen** gombot az újraindítás megerősítéséhez.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="A BareMetal-példány újraindítását bemutató képernyőkép.":::
 
BareMetal-példány újraindításakor késést tapasztalhat. Ebben a késésben a tápellátás a kezdeti kezdéstől **kezdve kezdődik** , ami azt jelenti **, hogy az** operációs rendszer teljesen elindult. Ennek eredményeképpen az újraindítást követően csak akkor jelentkezhet be az egységbe, ha az állapot **megkezdődött**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal-példány újraindításához használja az az [baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) parancsot:

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>A BareMetal-példány memóriájának mennyiségétől függően a hardver és az operációs rendszer újraindítása akár egy órát is igénybe vehet.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Támogatási kérelem megnyitása BareMetal-példányokhoz
 
A támogatási kérelmeket kifejezetten BareMetal-példányokhoz is elküldheti.
1. Azure Portal a Súgó és **támogatás** területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
 
   - **Probléma típusa:** Válassza ki a probléma típusát.
 
   - **Előfizetés:** Válassza ki az előfizetését.
 
   - **Szolgáltatás:** BareMetal-infrastruktúra
 
   - **Erőforrás:** Adja meg a példány nevét.
 
   - **Összefoglalás:** Adja meg a kérés összegzését.
 
   - **Probléma típusa:** Válassza ki a probléma típusát.
 
   - **Probléma altípusa:** Válassza ki a probléma altípusát.

1. Válassza a **megoldások** fület a probléma megoldásának megkereséséhez. Ha nem talál megoldást, folytassa a következő lépéssel.

1. Válassza a **részletek** lapot, és adja meg, hogy a probléma virtuális gépekkel vagy BareMetal-példányokkal rendelkezik-e. Ez az információ segít a támogatási kérelemnek a megfelelő szakértőknek történő irányításában.

1. Jelezze, hogy mikor kezdődött el a probléma, és válassza ki a példány régióját.

1. Adja meg a kérés további részleteit, és szükség esetén töltse fel a fájlt.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.
 
Egy támogatási képviselő legfeljebb öt munkanapot vesz igénybe, hogy erősítse meg a kérését.

## <a name="next-steps"></a>Következő lépések

További információ a munkaterhelésekről:

- [Mi SAP HANA az Azure-ban (nagyméretű példányok)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
