---
title: A Windows rendszerű virtuális asztali RD-átjáró szerepkör üzembe helyezése – Azure
description: A RD-átjáró szerepkör üzembe helyezése a Windows rendszerű virtuális asztalon.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 37d859bccd7fea082e059c7ab19c789276974cd1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445686"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>A RD-átjáró szerepkör üzembe helyezése a Windows Virtual Desktopban (előzetes verzió)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebből a cikkből megtudhatja, hogyan használhatja a RD-átjáró szerepkört (előzetes verzió) Távoli asztali átjáró-kiszolgálók üzembe helyezéséhez a környezetben. A kiszolgálói szerepköröket fizikai gépekre vagy virtuális gépekre is telepítheti attól függően, hogy helyszíni, felhőalapú vagy hibrid környezetet hoz-e létre.

## <a name="install-the-rd-gateway-role"></a>Az RD-átjáró szerepkör telepítése

1. Jelentkezzen be a célkiszolgálóra rendszergazdai hitelesítő adatokkal.

2. A **Kiszolgálókezelőben** válassza a **kezelés**, majd a **szerepkörök és szolgáltatások hozzáadása** lehetőséget. Ekkor megnyílik a **szerepkörök és szolgáltatások hozzáadása** telepítő.

3. A **Kezdés előtt** válassza a **tovább** lehetőséget.

4. A **telepítés típusának kiválasztása** lapon válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés** lehetőséget, majd kattintson a **tovább** gombra.

5. A **célkiszolgáló kijelölése** lapon válassza a **kiszolgáló kiválasztása a kiszolgáló készletből** lehetőséget. A **kiszolgáló készletnél** válassza ki a helyi számítógép nevét. Ha elkészült, kattintson a **Tovább gombra**.

6. A **kiszolgálói szerepkörök kiválasztása**  >  területen válassza a **Távoli asztali szolgáltatások** lehetőséget. Ha elkészült, kattintson a **Tovább gombra**.

7. A **Távoli asztali szolgáltatások** területen válassza a **tovább lehetőséget.**

8. A **szerepkör-szolgáltatások kiválasztása** lapon válassza a csak **Távoli asztali átjáró** lehetőséget, ha a rendszer a szükséges szolgáltatások hozzáadását kéri, válassza a **szolgáltatások hozzáadása** lehetőséget. Ha elkészült, kattintson a **Tovább gombra**.

9. A **hálózati házirend-és elérési szolgáltatások** területen válassza a **tovább** lehetőséget.

10. A **webkiszolgálói szerepkör (IIS)** esetében válassza a **tovább** lehetőséget.

11. A **szerepkör-szolgáltatások kiválasztása** lapon kattintson a **Tovább gombra**.

12. A **telepítési beállítások megerősítése** lapon válassza a **telepítés** lehetőséget. A telepítési folyamat során ne zárjuk le a telepítőt.

## <a name="configure-rd-gateway-role"></a>RD-átjáró szerepkör konfigurálása

Miután telepítette a RD-átjáró szerepkört, konfigurálnia kell.

A RD-átjáró szerepkör konfigurálása:

1. Nyissa meg a **Kiszolgálókezelő alkalmazást**, majd válassza a **Távoli asztali szolgáltatások** lehetőséget.

2. Nyissa meg a **kiszolgálók** csomópontot, kattintson a jobb gombbal a kiszolgáló nevére, majd válassza a **Rd-átjárókezelő** lehetőséget.

3. A RD-átjárókezelő kattintson a jobb gombbal az átjáró nevére, majd válassza a **Tulajdonságok parancsot**.

4. Nyissa meg az **SSL-tanúsítvány** lapot, jelölje be a **tanúsítvány importálása a RD-átjáró** buborékba, majd válassza a **Tallózás és a tanúsítvány importálása..**. lehetőséget.

5. Válassza ki a PFX-fájl nevét, majd kattintson a **Megnyitás** gombra.

6. Ha a rendszer kéri, adja meg a PFX-fájl jelszavát.

7. A tanúsítvány és a titkos kulcs importálása után a megjelenítésnek a tanúsítvány legfontosabb attribútumait kell megjelenítenie.

>[!NOTE]
>Mivel a RD-átjáró szerepkör nyilvánosnak kellene lennie, javasoljuk, hogy nyilvánosan kiállított tanúsítványt használjon. Ha saját maga által kiállított tanúsítványt használ, az összes ügyfelet előre be kell állítania a tanúsítvány megbízhatósági láncával.

## <a name="next-steps"></a>Következő lépések

Ha magas rendelkezésre állást szeretne adni a RD-átjáró-szerepkörhöz, tekintse meg [a magas rendelkezésre állás hozzáadása a távoli asztali webes és átjáró webes](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)felületéhez című témakört.