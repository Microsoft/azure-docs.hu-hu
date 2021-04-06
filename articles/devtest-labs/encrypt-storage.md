---
title: Tesztkörnyezet által használt Azure Storage-fiók titkosítása Azure DevTest Labs
description: Megtudhatja, hogyan konfigurálhatja a labor által használt Azure-tárolók titkosítását Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149311"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>A labor által használt Azure Storage titkosítása Azure DevTest Labs
A Azure DevTest Labsban létrehozott összes labor egy társított Azure Storage-fiókkal jön létre. A Storage-fiók a következő célokra szolgál: 

- A virtuális gépek létrehozásához használható [képletek](devtest-lab-manage-formulas.md) tárolására szolgáló dokumentumok tárolása.
- Az összetevők alkalmazásával létrehozott, üzembe helyezési és kiterjesztési naplókat tartalmazó leletek eredményeinek tárolása. 
- [Virtuális merevlemezek (VHD-k) feltöltése Egyéni rendszerképek létrehozásához a laborban.](devtest-lab-create-template.md)
- A gyorsítótárazás gyakran [](add-artifact-vm.md) használt összetevőket és [Azure Resource Manager sablonokat](devtest-lab-create-environment-from-arm.md) a virtuális gép/környezet létrehozása során felmerülő gyorsabb lekéréshez.

> [!NOTE]
> A fenti információk kritikus fontosságúak a labor működéséhez. A tesztkörnyezet élettartama (és a Labor erőforrásai) tárolása, kivéve, ha explicit módon törölve van. Ezeknek az erőforrásoknak a manuális törlése hibákat eredményezhet a laboratóriumi virtuális gépek és/vagy a későbbi használat során megsérült képletek létrehozásához. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Keresse meg a Storage-fiókot, és tekintse meg a tartalmát

1. A labor kezdőlapján válassza ki az **erőforráscsoportot** az **Áttekintés** oldalon. Ekkor meg kell jelennie a labort tartalmazó erőforráscsoport **erőforráscsoport** -oldalának. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Válassza ki az erőforráscsoportot az Áttekintés oldalon":::
1. Válassza ki a labor Azure Storage-fiókját. A labor Storage-fiók elnevezési konvenciója a következő: `a<labNameWithoutInvalidCharacters><4-digit number>` . Ha például a labor neve `contosolab` , a Storage-fiók neve lehet `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Válassza ki a Storage-fiókot a tesztkörnyezet erőforráscsoporthoz.":::
3. A **Storage-fiók** lapon válassza a bal oldali menüben a **Storage Explorer (előzetes verzió)** lehetőséget, majd válassza a blob- **tárolók** lehetőséget a kapcsolódó laborokkal kapcsolatos tartalom megtalálásához. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Storage Explorer (előzetes verzió)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>A labor Storage-fiók titkosítása
Az Azure Storage automatikusan titkosítja az adatait, ha a felhőben is megmarad. Az Azure Storage-titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. További információ: az [Azure Storage titkosítása inaktív adatokhoz](../storage/common/storage-service-encryption.md).

A Lab Storage-fiókban tárolt adatforgalom **Microsoft által felügyelt kulccsal** van titkosítva. Hivatkozhat a Microsoft által felügyelt kulcsokra az adatok titkosításához, vagy kezelheti a titkosítást a saját kulcsaival. Ha úgy dönt, hogy a labor Storage-fiókjához tartozó saját kulcsokkal kezeli a titkosítást, megadhat egy **ügyfél által felügyelt kulcsot** , amely Azure Key Vault használható a blob Storage-ban és a Azure Filesban tárolt adattitkosításhoz/visszafejtéshez. Az ügyfél által felügyelt kulcsokkal kapcsolatos további információkért lásd: [ügyfelek által felügyelt kulcsok használata Azure Key Vault az Azure Storage-titkosítás kezeléséhez](../storage/common/customer-managed-keys-overview.md).

A következő cikkekből megtudhatja, hogyan konfigurálhat ügyfél által felügyelt kulcsokat az Azure Storage encryption szolgáltatáshoz: 

- [Azure Portalra](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Az Azure Blob Storage életciklusának kezelése
Ahogy említettük, a labor Storage-fiókjában tárolt információk kritikus fontosságúak ahhoz, hogy a labor hibák nélkül működjön. Ha explicit módon nem törli őket, az adattípustól függően ezek az adatmennyiségek továbbra is a laborban maradnak a laborban, illetve az adott labor virtuális gépek élettartama alapján.

### <a name="uploaded-vhds"></a>Feltöltött virtuális merevlemezek
Ezek a VHD-k Egyéni rendszerképek létrehozására használhatók. Ezek eltávolításával többé nem lehet egyéni lemezképeket létrehozni ezekből a VHD-lemezekről.

### <a name="artifacts-cache"></a>Összetevők gyorsítótára
Ezek a gyorsítótárak újra létrejönnek az összetevők alkalmazása során. A rendszer a megfelelő hivatkozott adattárakból származó legújabb tartalommal frissíti őket. Ha tehát törli ezeket az információkat a tárolással kapcsolatos költségek mentéséhez, a mentesség ideiglenes lesz.

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager sablon gyorsítótára
Ezek a gyorsítótárak újra létrejönnek, amikor a Azure Resource Manager-alapú sablon-Tárházak csatlakoztatva vannak és megpördült a laborban. A rendszer a megfelelő hivatkozott adattárakból származó legújabb tartalommal frissíti őket. Ha tehát törli ezeket az információkat a tárolással kapcsolatos költségek mentéséhez, a mentesség ideiglenes lesz.

### <a name="formulas"></a>Képletek
Ezek a dokumentumok támogatják a meglévő virtuális gépekről származó képletek létrehozását és a virtuális gépek képletből való létrehozását. A képletek törlése a következő műveletek végrehajtásakor hibákat eredményezhet:

- Képlet létrehozása meglévő laboratóriumi virtuális gépről
- Képletek létrehozása vagy frissítése 
- Virtuális gép létrehozása képletből

### <a name="artifact-results"></a>Leletek eredményei
Az összetevők alkalmazása esetén az adott leletek eredményeinek mérete a laboratóriumi virtuális gépeken futtatott összetevők számától és típusától függően is növekedhet. Tehát a labor tulajdonosaként érdemes lehet szabályozni az ilyen dokumentumok életciklusát. További információ: [Az Azure Blob Storage életciklusának kezelése](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Javasoljuk, hogy ezt a lépést az Azure Storage-fiókkal kapcsolatos költségek csökkentése érdekében végezze el. 

A következő szabály például egy 90 napos lejárati szabály beállítására szolgál, amely kifejezetten az összetevők eredményeire vonatkozik. Gondoskodik róla, hogy a régebbi összetevők eredményei újra legyenek hasznosítva a Storage-fiókból egy normál ütemben.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan konfigurálhat ügyfél által felügyelt kulcsokat az Azure Storage encryption szolgáltatáshoz: 

- [Azure Portalra](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)