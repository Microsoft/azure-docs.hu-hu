---
title: 'Előzetes verzió: megbízható indítású virtuális gép üzembe helyezése'
description: Megbízható indítást használó virtuális gép üzembe helyezése.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: f5e361d32cf2ab436f92ce2ca86a054a6dd3337e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553748"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Megbízható indítást engedélyező virtuális gép üzembe helyezése (előzetes verzió)

A [megbízható indítás](trusted-launch.md) egy módszer a [2. generációs](generation-2.md) virtuális gépek biztonságának javítására. A megbízható indítás a fejlett és állandó támadásokkal szemben az infrastruktúra-technológiák, például a vTPM és a biztonságos rendszerindítás kombinálásával nyújt védelmet.

> [!IMPORTANT]
> A megbízható indítás jelenleg nyilvános előzetes verzióban érhető el.
> 
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Üzembe helyezés a portál használatával

Hozzon létre egy virtuális gépet, amelyen engedélyezve van a megbízható indítás.

1. Jelentkezzen be az Azure [Portalra](https://aka.ms/TL_preview).
1. **Virtual Machines** keresése.
1. A **szolgáltatások** területen válassza a **virtuális gépek** lehetőséget.
1. A **virtuális gépek** lapon válassza a **Hozzáadás**, majd a **virtuális gép** lehetőséget.
1. A **projekt részletei** területen ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
1. A **példány részletei** területen adja meg a virtuális gép nevét, és válasszon egy olyan régiót, amely támogatja a [megbízható indítást](trusted-launch.md#public-preview-limitations).
1. A **rendszerkép** területen válasszon ki egy olyan [rendszerképet, amely támogatja a megbízható indítást](trusted-launch.md#public-preview-limitations). Előfordulhat, hogy csak a rendszerkép 1. generációs verzióját látja, ami nem minden rendben van, folytassa a következő lépéssel.
1. Váltson át a **speciális** lapra úgy, hogy kiválasztja az oldal tetején.
1. Görgessen le a virtuálisgép- **generáció** szakaszhoz, majd válassza a **2**. generációs elemet.
1. Miközben továbbra is a **speciális** lapon, görgessen le a **megbízható indítás** elemig, majd jelölje be a **megbízható indítás** jelölőnégyzetet. Ekkor két további lehetőség jelenik meg – biztonságos rendszerindítás és vTPM. Válassza ki a megfelelő beállításokat a központi telepítéshez.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="A megbízható indítás beállításait bemutató képernyőkép.":::

1. Lépjen vissza az **alapok** lapra, a **kép** területen, és győződjön meg arról, hogy a következő üzenet jelenik meg: **Ez a kép támogatja a megbízható indítási előnézetet. Konfigurálás a Speciális lapon**. A 2. generációs képet most ki kell jelölni.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Képernyőfelvétel: az üzenet, amely megerősíti, hogy ez egy Gen2-rendszerkép, amely támogatja a megbízható indítást.":::

1.  Válasszon olyan virtuálisgép-méretet, amely támogatja a megbízható indítást. Tekintse meg a [támogatott méretek](trusted-launch.md#public-preview-limitations)listáját.
1.  Adja meg a **rendszergazdai fiók** adatait, majd a **bejövő portok szabályait**.
1.  A lap alján válassza a **felülvizsgálat + létrehozás** elemet.
1.  A **virtuális gép létrehozása** lapon megtekintheti a telepíteni kívánt virtuális gép adatait. Ha készen áll, kattintson a **Létrehozás** gombra.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Az érvényesítés lap Sceenshot, amely a megbízható indítási beállításokat tartalmazza.":::


A virtuális gép üzembe helyezése eltarthat néhány percig. 

## <a name="deploy-using-a-template"></a>Üzembe helyezés sablon használatával

A megbízható indítási virtuális gépek üzembe helyezése egy rövid útmutató sablon használatával végezhető el:

**Linux**:    
[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Megtekintés és frissítés

Egy meglévő virtuális gép megbízható indítási konfigurációját úgy tekintheti meg, ha a portálon meglátogatja a virtuális gép **Áttekintés** lapját.

A megbízható indítási konfiguráció módosításához a bal oldali menüben válassza a **Konfigurálás** lehetőséget a **Beállítások** szakaszban. Engedélyezheti vagy letilthatja a biztonságos rendszerindítást és a vTPM a **megbízható indítás** szakaszban. Ha elkészült, válassza a **Mentés** lehetőséget az oldal tetején. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Képernyőkép a megbízható indítási konfiguráció módosításáról.":::

Ha a virtuális gép fut, üzenet jelenik meg arról, hogy a virtuális gép újraindul, hogy alkalmazza a módosított megbízható indítási konfigurációt. Válassza az **Igen** lehetőséget, majd várjon, amíg a virtuális gép újraindul a módosítások érvénybe léptetéséhez.


## <a name="verify-secure-boot-and-vtpm"></a>Biztonságos rendszerindítási és vTPM ellenőrzése

Ellenőrizheti, hogy a biztonságos rendszerindítási és vTPM engedélyezve vannak-e a virtuális gépen.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: Ellenőrizze, hogy fut-e a biztonságos rendszerindítás

SSH-t a virtuális géphez, majd futtassa a következő parancsot: 

```bash
mokutil --sb-state
```

Ha a biztonságos rendszerindítás engedélyezve van, a parancs a következőket fogja visszaadni:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: annak ellenőrzése, hogy engedélyezve van-e a vTPM

Jelentkezzen be a virtuális gépre SSH-val. Ellenőrizze, hogy van-e tpm0 eszköz: 

```bash
ls /dev/tpm0
```

Ha a vTPM engedélyezve van, a parancs a következőket fogja visszaadni:

```output
/dev/tpm0
```

Ha a vTPM le van tiltva, a parancs a következőket fogja visszaadni:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: ellenőrzi, hogy a biztonságos rendszerindítás fut-e

Kapcsolódjon a virtuális géphez a távoli asztal használatával, majd futtassa a parancsot `msinfo32.exe` .

A jobb oldali ablaktáblán győződjön meg arról, hogy a biztonságos rendszerindítási állapot **be van kapcsolva**.

## <a name="enable-the-azure-security-center-experience"></a>A Azure Security Center felületének engedélyezése

Ha engedélyezni szeretné a Azure Security Center a megbízható indítási virtuális gépek adatainak megjelenítését, több szabályzatot is engedélyeznie kell. A házirendek engedélyezésének legegyszerűbb módja a [Resource Manager-sablon](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) üzembe helyezése az előfizetésben. 

Az alábbi gombra kattintva helyezheti üzembe a szabályzatokat az előfizetésében:

[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

A sablont csak egyszer kell központilag telepíteni. A rendszer automatikusan telepíti és bővíti `GuestAttestation` `AzureSecurity` az összes támogatott virtuális gépre. Ha hibákat kap, próbálkozzon újra a sablon ismételt üzembe helyezésével.

A megbízható indítási virtuális gépek vTPM és biztonságos rendszerindítási javaslatainak beszerzéséhez tekintse [meg az egyéni kezdeményezés hozzáadása az előfizetéshez](https://docs.microsoft.com/azure/security-center/custom-security-policies#to-add-a-custom-initiative-to-your-subscription)című témakört.
 
## <a name="sign-things-for-secure-boot-on-linux"></a>A biztonságos rendszerindításhoz szükséges dolgok aláírása Linux rendszeren

Bizonyos esetekben előfordulhat, hogy alá kell írnia az UEFI biztonságos rendszerindításhoz szükséges dolgokat.  Előfordulhat például, hogy át kell lépnie [az Ubuntu biztonságos rendszerindításához szükséges dolgokkal](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) . Ezekben az esetekben meg kell adnia a virtuális géphez tartozó MOK-regisztráció kulcsait. Ehhez az Azure soros konzolt kell használnia az MOK segédprogram eléréséhez.

1. A Linux rendszerhez készült Azure soros konzol engedélyezése. További információ: [soros konzol Linux rendszerhez](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. Keressen rá a **virtuális gépekre** , és válassza ki a virtuális gépet a listából.
1. A bal oldali menüben a **támogatás + hibaelhárítás** területen válassza a **Serial Console** lehetőséget. Egy oldal jobbra, a soros konzollal fog megnyílni.
1. Jelentkezzen be a virtuális gépre az Azure soros konzol használatával. A **bejelentkezéshez** adja meg a virtuális gép létrehozásakor használt felhasználónevet. Például: *azureuser*. Ha a rendszer kéri, adja meg a felhasználónévhez tartozó jelszót.
1. Miután bejelentkezett, `mokutil` a használatával importálhatja a nyilvános kulcs `.der` fájlját.

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. A beírásával indítsa újra a gépet az Azure soros konzolról `sudo reboot` . A rendszer 10 másodperces visszaszámlálást kezd.
1. Nyomja le a fel vagy le billentyűt a visszaszámlálás megszakításához, és várjon az UEFI konzol üzemmódba. Ha az időzítő nem szakad meg, a rendszerindítási folyamat folytatódik, és az összes MOK-módosítás elvész.
1. Válassza ki a megfelelő műveletet az MOK segédprogram menüjéből.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Képernyőfelvétel: a soros konzolon található MOK felügyeleti menüjében elérhető beállítások.":::


## <a name="next-steps"></a>Következő lépések

További információ a [megbízható indításról](trusted-launch.md) és a [2. generációs](generation-2.md) virtuális gépekről.
