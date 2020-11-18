---
title: Nem Azure-beli gépek összekapcsolásának Azure Security Center
description: Ismerje meg, hogyan csatlakoztathatók a nem Azure-beli gépek Security Center
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 572c657dd44db73b1401c72f0750fe8e000985ac
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734354"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Nem Azure-beli gépek összekapcsolásának Security Center

Security Center nyomon követheti a nem Azure-beli számítógépek biztonsági állapotát, de először össze kell kapcsolni őket az Azure-ban.

A nem Azure-beli számítógépeket a következő módokon lehet összekötni:

- Azure arc-kompatibilis kiszolgálók használata (**ajánlott**)
- Security Center a Azure Portal oldalain (**első lépések** és **leltár**)

Ezek mindegyikét ezen a lapon mutatjuk be.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Nem Azure-beli gépek hozzáadása az Azure Arcmal

Az Azure arc-kompatibilis kiszolgálók a nem Azure-beli gépek Azure Security Centerhoz való hozzáadásának előnyben részesített módja.

Az Azure arc-kompatibilis kiszolgálókkal rendelkező gépek Azure-erőforrásként jelennek meg, és Security Center a többi Azure-erőforráshoz hasonló javaslatokkal.

Emellett az Azure arc-kompatibilis kiszolgálók olyan továbbfejlesztett funkciókat biztosítanak, mint például a vendég-konfigurációs házirendek engedélyezése a gépen, a Log Analytics-ügynök kiterjesztéseként, a többi Azure-szolgáltatással való üzembe helyezés egyszerűsítése és egyebek. Az előnyök áttekintését lásd: [támogatott forgatókönyvek](../azure-arc/servers/overview.md#supported-scenarios).

További információ az [Azure arc használatára képes kiszolgálókról](../azure-arc/servers/overview.md).

**Az Azure arc üzembe helyezése:**

- Egy gépen kövesse a gyors útmutató [: hibrid gép összekapcsolása Azure arc-kompatibilis kiszolgálókkal](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)című témakör utasításait.
- Ha több gépet szeretne összekapcsolni az ív használatára képes kiszolgálókon, lásd: [hibrid gépek összekapcsolása az Azure-ba a Scale](../azure-arc/servers/onboard-service-principal.md) -ben

> [!TIP]
> Ha AWS-ben futó gépeket készít, az AWS-hez való Security Center összekötője transzparens módon kezeli az Azure arc-telepítést. További információ az [AWS-fiókok Azure Security Center való összekapcsolásáról](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Nem Azure-beli gépek hozzáadása a Azure Portal

1. A Security Center menüjében nyissa meg az **első lépések** lapot.
1. Válassza a **Bevezetés** lapot.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Első lépések lap a kezdeti lépések oldalon" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. A **nem Azure-beli kiszolgálók hozzáadása** alatt válassza a **Konfigurálás** lehetőséget.

    > [!TIP]
    > Megnyithatja a gépek hozzáadása lehetőséget is a **leltár** lap **nem Azure-beli kiszolgálók hozzáadása** gombjával.
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Nem Azure-beli gépek hozzáadása az eszköz leltározási oldaláról":::

    Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

    Hozzáadhat számítógépeket egy meglévő munkaterülethez, vagy létrehozhat egy új munkaterületet.

1. Ha új munkaterületet szeretne létrehozni, válassza az  **Új munkaterület létrehozása** lehetőséget.

1. A munkaterületek listájából válassza a **kiszolgálók hozzáadása** lehetőséget a megfelelő munkaterülethez.
    Megjelenik az **ügynökök kezelése** lap.

    Innen válassza ki az alábbi megfelelő eljárást a bevezetéshez használt gépek típusától függően:

    - [A Azure Stack virtuális gépek előkészítése](#onboard-your-azure-stack-vms)
    - [A Linux rendszerű gépek előkészítése](#onboard-your-linux-machines)
    - [Windows rendszerű gépek előkészítése](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-vms"></a>A Azure Stack virtuális gépek előkészítése

Azure Stack virtuális gépek hozzáadásához szüksége lesz az **ügynökök kezelése** oldalon található információkra, valamint a **Azure monitor, a frissítés és a konfiguráció kezelése** virtuálisgép-bővítmény konfigurálására a Azure stack futó virtuális gépeken.

1. Az **ügynökök kezelése** lapon másolja a munkaterület- **azonosítót** és az **elsődleges kulcsot** a Jegyzettömbbe.
1. Jelentkezzen be **Azure stack** -portálra, és nyissa meg a **virtuális gépek** lapot.
1. Válassza ki a Security Center védelemmel ellátni kívánt virtuális gépet.
    >[!TIP]
    > A virtuális gépek Azure Stackon való létrehozásával kapcsolatos információkért tekintse meg ezt a rövid útmutatót [a Windows rendszerű virtuális gépekről](/azure-stack/user/azure-stack-quick-windows-portal) , illetve [a Linux rendszerű virtuális gépekről](/azure-stack/user/azure-stack-quick-linux-portal).
1. Válassza a **Bővítmények** lehetőséget. Megjelenik a virtuális gépen telepített virtuálisgép-bővítmények listája.
1. Válassza a **Hozzáadás** lapot. Az **új erőforrás** menü megjeleníti az elérhető virtuálisgép-bővítmények listáját.
1. Válassza ki a **Azure monitor, a frissítés és a konfiguráció kezelése** bővítményt, és válassza a **Létrehozás** lehetőséget. Megnyílik a **telepítési bővítmény** konfigurációja lap.
    >[!NOTE]
    > Ha nem látja a piactéren felsorolt **Azure monitor, frissítés és konfigurálás felügyeleti** bővítményt, forduljon a Azure stack-kezelőhöz, hogy elérhető legyen.
1. A **bővítmény** konfigurálása lapon illessze be az előző lépésben a Jegyzettömbbe másolt **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** .
1. A konfiguráció befejezése után kattintson **az OK gombra**. A bővítmény állapota a **kiépítés sikerességének** megfelelően jelenik meg. Akár egy óráig is eltarthat, amíg a virtuális gép megjelenik Security Center.

### <a name="onboard-your-linux-machines"></a>A Linux rendszerű gépek előkészítése

Linuxos gépek hozzáadásához szüksége lesz a WGET parancsra az **ügynökök kezelése** lapon.

1. Az **ügynökök kezelése** lapon másolja a **WGET** parancsot a Jegyzettömbbe. Olyan helyre mentse a fájlt, amelyet el lehet érni a Linux rendszerű számítógépről.
1. A Linux rendszerű számítógépen nyissa meg a fájlt a WGET paranccsal. Válassza ki a teljes tartalmat, és másolja és illessze be egy terminál-konzolba.
1. A telepítés befejezésekor ellenőrizheti, hogy a *omsagent* telepítve van-e a *pgrep* parancs futtatásával. A parancs visszaküldi a *omsagent* PID-t.
    Az ügynök naplói a következő címen találhatók: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* az új Linux-gép a Security Centerban akár 30 percet is igénybe vehet.

### <a name="onboard-your-windows-machines"></a>Windows rendszerű gépek előkészítése

Windows rendszerű gépek hozzáadásához szüksége lesz az **ügynökök kezelése** oldalon található információkra, és le kell töltenie a megfelelő ügynök fájlját (32/64 bites).
1. Válassza ki a számítógép processzortípusának megfelelő **Windows-ügynök letöltése** hivatkozást a telepítési fájl letöltéséhez.
1. Az **ügynökök kezelése** lapon másolja a munkaterület- **azonosítót** és az **elsődleges kulcsot** a Jegyzettömbbe.
1. Másolja a letöltött telepítőfájlt a célszámítógépen, és futtassa.
1. Kövesse a telepítési varázslót (**következő**, **Elfogadom**, **tovább**, **tovább**).
    1. Az **Azure log Analytics** lapon illessze be a Jegyzettömbbe másolt **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** .
    1. Ha a számítógépnek Azure Government felhőben Log Analytics munkaterületre kell jelentenie, válassza az **Azure US government** lehetőséget az **Azure Cloud** legördülő listából.
    1. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.
    1. Ha megadta az összes konfigurációs beállítást, válassza a **tovább** lehetőséget.
    1. A **telepítésre kész** oldalon tekintse át az alkalmazandó beállításokat, majd válassza a **telepítés** lehetőséget.
    1. A **Konfigurálás sikeresen befejeződött** lapon válassza a **Befejezés** lehetőséget.

Ha elkészült, a **Microsoft monitoring Agent** megjelenik a **Vezérlőpulton**. Áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök megfelelően csatlakozik-e.

Az ügynök telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Windows rendszerű számítógépek összekapcsolása](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Ellenőrzése

Gratulálunk! Az Azure-és a nem Azure-beli gépeket most már egyetlen helyen láthatja. Nyissa meg a [tárgyieszköz-leltár lapot](asset-inventory.md) , és a megfelelő erőforrástípusok alapján szűrje a szűrőt. Ezek az ikonok megkülönböztetik a típusokat:

  ![A nem Azure-beli gép ASC ikonja](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Nem Azure-beli gép

  ![Az Azure Machine ASC ikonja](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![Az Azure arc-kiszolgáló ASC ikonja](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Azure arc-kompatibilis kiszolgáló

## <a name="next-steps"></a>Következő lépések

Ezen az oldalon bemutatjuk, hogyan adhat hozzá a nem Azure-beli gépeket Azure Security Centerhoz. Az állapotuk figyeléséhez használja a leltári eszközöket a következő oldalon leírtak szerint:

- [Erőforrások megismerése és kezelése az eszközök leltározásával és felügyeleti eszközeivel](asset-inventory.md)