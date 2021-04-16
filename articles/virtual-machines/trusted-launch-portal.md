---
title: 'Előzetes verzió: Megbízható indítási virtuális gép üzembe helyezése'
description: Megbízható indítást használó virtuális gép üzembe helyezése.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 295579d17f3b24adcf43f6907cc4b1aca01dcae2
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565916"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Virtuális gép üzembe helyezése engedélyezett megbízható indítással (előzetes verzió)

[A megbízható indítás](trusted-launch.md) egy módszer a [2.](generation-2.md) generációs virtuális gépek biztonságának javítására. A megbízható indítás a fejlett és állandó támadási technikákkal szemben a vTPM és a biztonságos rendszerindításhoz hasonló infrastruktúra-technológiák kombinálásával nyújt védelmet.

> [!IMPORTANT]
> A megbízható indítás jelenleg nyilvános előzetes verzióban érhető el.
> 
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Üzembe helyezés a portál használatával

Virtuális gép létrehozása engedélyezett megbízható indítással.

1. Jelentkezzen be az Azure [Portalra.](https://aka.ms/TL_preview)
   > [!NOTE] 
   > A portál hivatkozása egyedi a megbízható indítási előzetes verzióban.
   >  
2. Keressen rá a **Virtual Machines.**
3. A **Szolgáltatások alatt** válassza a Virtuális gépek **lehetőséget.**
4. A Virtuális **gépek lapon** válassza a **Hozzáadás,** majd a **Virtuális gép lehetőséget.**
5. A **Projekt részletei alatt** ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
6. Az **Erőforráscsoport alatt** válassza az **Új létrehozása** lehetőséget, és adjon nevet az erőforráscsoportnak, vagy válasszon ki egy meglévő erőforráscsoportot a legördülő menüből.
7. A **Példány részletei területen** adja meg a virtuális gép nevét, és válasszon ki egy olyan régiót, amely támogatja a megbízható [indítást.](trusted-launch.md#public-preview-limitations)
8. A **Rendszerkép alatt** válasszon ki egy 2. generációs [rendszerképet,](trusted-launch.md#public-preview-limitations)amely támogatja a megbízható indítást. Győződjön meg arról, hogy a következő üzenet jelenik meg: **Ez a rendszerkép támogatja a megbízható indítási előnézetet. Konfigurálja a következőt a Speciális lapon:**.
   > [!TIP]
   > Ha nem látja a kívánt rendszerkép Gen 2-es verzióját a  legördülő menüben,  válassza az Összes rendszerkép megjelenítése lehetőséget, majd módosítsa a virtuálisgép-generáció szűrőt úgy, hogy csak a Gen 2 rendszerképeket mutassa. Keresse meg a rendszerképet  a listában, majd a 2. generációs verzió kiválasztásához használja a Select (Kijelölés) legördülő listát.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Képernyőkép a megbízható indítást támogató gen2-rendszerkép megerősítéséről.":::

13. Válassza ki a megbízható indítást támogató virtuálisgép-méretet. Tekintse meg a támogatott [méretek listáját.](trusted-launch.md#public-preview-limitations)
14. Adja meg a **rendszergazdai fiók adatait,** majd a **Bejövő portszabályokat.** 
1. Váltson át **a Speciális lapra** az oldal tetején található kiválasztásával.
1. Görgessen le a **Virtuális gép létrehozása szakaszhoz.** Győződjön meg arról, **hogy a Gen 2 van** kiválasztva.
1. A Speciális **lapon** görgessen le a Megbízható indítás beállításhoz, majd jelölje be a Megbízható **indítás jelölőnégyzetet.** Így két további lehetőség jelenik meg: Biztonságos rendszerindítás és vTPM. Válassza ki az üzembe helyezéshez megfelelő beállításokat.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="A megbízható indítás beállításait bemutató képernyőkép.":::

15. A lap alján válassza az Áttekintés **+ létrehozás lehetőséget.**
16. A **Virtuális gép létrehozása oldalon** láthatja az üzembe helyezni fog virtuális gép részleteit. Ha készen áll, kattintson a **Létrehozás** gombra.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Az érvényesítési lapról a megbízható indítási lehetőségeket megjelenítő Sceenshot() látható.":::


A virtuális gép üzembe helyezése eltarthat néhány percig. 

## <a name="deploy-using-a-template"></a>Üzembe helyezés sablon használatával

A megbízható indítású virtuális gépeket gyorsindítási sablonnal helyezheti üzembe:

**Linux**:    
[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Megtekintés és frissítés

Egy meglévő virtuális gép megbízható indítási konfigurációját a virtuális gép Áttekintés lapján, a portálon megtekintheti. 

A megbízható indítás konfigurációjának módosításához a  bal oldali menü beállítások szakaszában válassza a Konfiguráció **lehetőséget.** A biztonságos rendszerindítást és a vTPM-et a Megbízható indítás **szakaszban engedélyezheti vagy tilthatja** le. Ha **végzett,** kattintson a lap tetején található Mentés gombra. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Képernyőkép a megbízható indítási konfigurációkról.":::

Ha a virtuális gép fut, egy üzenet jelenik meg, amely szerint a virtuális gép újraindul a módosított megbízható indítási konfiguráció alkalmazásához. Válassza **az Igen** lehetőséget, majd várja meg, amíg a virtuális gép újraindul, amíg a módosítások érvénybe lépnek.


## <a name="verify-secure-boot-and-vtpm"></a>A biztonságos rendszerindítás és a vTPM ellenőrzése

Ellenőrizheti, hogy a biztonságos rendszerindítás és a vTPM engedélyezve van-e a virtuális gépen.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: ellenőrizze, hogy fut-e a biztonságos rendszerindítás

SSH-ból a virtuális géphez, majd futtassa a következő parancsot: 

```bash
mokutil --sb-state
```

Ha a biztonságos rendszerindítást engedélyezi, a parancs a következőt adja vissza:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: annak ellenőrzése, hogy a vTPM engedélyezve van-e

Jelentkezzen be a virtuális gépre SSH-val. Ellenőrizze, hogy a tpm0 eszköz jelen van-e: 

```bash
ls /dev/tpm0
```

Ha a vTPM engedélyezve van, a parancs a következőt adja vissza:

```output
/dev/tpm0
```

Ha a vTPM le van tiltva, a parancs a következőt adja vissza:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: ellenőrizze, hogy fut-e a biztonságos rendszerindítás

Csatlakozzon a virtuális géphez távoli asztal használatával, majd futtassa a következőt: `msinfo32.exe` .

A jobb oldali panelen ellenőrizze, hogy a Biztonságos rendszerindítási állapot **BE van-e va.**

## <a name="enable-the-azure-security-center-experience"></a>A Azure Security Center engedélyezése

Ahhoz, Azure Security Center rendszer megjelenítse a megbízható indítású virtuális gépek adatait, több szabályzatot is engedélyeznie kell. A szabályzatok engedélyezésének legegyszerűbb módja, ha üzembe Resource Manager [ezt](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) a sablont az előfizetésben. 

Kattintson az alábbi gombra a szabályzatok előfizetésben való üzembe helyezéséhez:

[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

A sablont előfizetésenként csak egyszer kell üzembe helyezni. Automatikusan telepíti és `GuestAttestation` `AzureSecurity` bővítményeket telepít az összes támogatott virtuális gépre. Ha hibaüzenetet kap, próbálkozzon újra a sablon ismételt üzembe ásával.

A vTPM-et és a megbízható indítású virtuális gépek biztonságos rendszerindítási javaslatait lásd: Egyéni kezdeményezés hozzáadása [az előfizetéshez.](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Aláírás a biztonságos rendszerindításhoz Linuxon

Bizonyos esetekben szükség lehet az UEFI biztonságos rendszerindításhoz szükséges aláírásra.  Előfordulhat például, hogy végig kell mennie a How to sign things for Secure Boot for Ubuntu (Az Ubuntuhoz való [biztonságos rendszerindítás aláíratásának mikéntjéhez)](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) részben. Ezekben az esetekben meg kell adnia a virtuális gép MOK segédprogramregisztrációs kulcsait. Ehhez az Azure Serial Console-t kell használnia az MOK segédprogram eléréséhez.

1. Engedélyezze a Linuxhoz való Azure soros konzolt. További információ: [Soros konzol Linuxhoz.](/troubleshoot/azure/virtual-machines/serial-console-linux)
1. Jelentkezzen be az [Azure Portal.](https://portal.azure.com)
1. Keressen rá a **Virtuális gépek kifejezésre,** és válassza ki a virtuális gépet a listából.
1. A bal oldali menü Támogatás **+ hibaelhárítás menüpont alatt** válassza a Serial console.  A jobb oldalon megnyílik egy oldal a soros konzollal.
1. Jelentkezzen be a virtuális gépre az Azure soros konzol használatával. A **bejelentkezéshez** adja meg a virtuális gép létrehozásakor használt felhasználónevet. Például: *azureuser*. Amikor a rendszer kéri, adja meg a felhasználónévhez tartozó jelszót.
1. Miután bejelentkezett, a használatával `mokutil` importálja a nyilvános kulcsfájlt. `.der`

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Indítsa újra a gépet az Azure soros konzolról a következő beírásával: `sudo reboot` . 10 másodperces visszaszámlálás kezdődik.
1. Nyomja le vagy fel a billentyűt a visszaszámlálás megszakítása és az UEFI-konzol módban való várakozáshoz. Ha az időzítő nem szakad meg, a rendszerindítási folyamat folytatódik, és az összes MOK-módosítás elveszik.
1. Válassza ki a megfelelő műveletet az MOK segédprogram menüjében.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Képernyőkép a soros konzol MOK felügyeleti menüjében elérhető lehetőségekről.":::


## <a name="next-steps"></a>Következő lépések

További információ a megbízható [indításról és](trusted-launch.md) [a 2. generációs](generation-2.md) virtuális gépekről.
