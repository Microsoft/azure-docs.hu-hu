---
title: Titkosított Azure-beli virtuális gépek replikálásának engedélyezése Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan konfigurálható a virtuális gépek replikálása az ügyfél által felügyelt kulccsal (CMK) rendelkező, az egyik Azure-régióból a másikba a Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 9f9052f51c5bab0ea738e9fd15d8f62f45ff0c9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146535"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Gépek replikálása ügyfél által kezelt kulcsokkal (CMK-) kompatibilis lemezekkel

Ez a cikk azt ismerteti, hogyan replikálhat Azure-beli virtuális gépeket Customer-Managed kulcsokkal (CMK) rendelkező felügyelt lemezekkel az egyik Azure-régióból a másikba.

## <a name="prerequisite"></a>Előfeltétel
A CMK-kompatibilis felügyelt lemezekkel rendelkező virtuális gépek replikálásának engedélyezése előtt létre kell hoznia a lemezes titkosítási készlet (eke) t a célként megadott régióban a célként megadott előfizetéshez.

## <a name="enable-replication"></a>A replikáció engedélyezése

Ebben a példában az elsődleges Azure-régió Kelet-Ázsia, a másodlagos régió pedig Dél-Kelet-Ázsia.

1. A tárolóban válassza a **+ replikálás** lehetőséget.
2. Jegyezze fel a következő mezőket.
    - **Forrás**: a virtuális gépek származási helye, amely ebben az esetben az **Azure**.
    - **Forrás helye**: az az Azure-régió, ahol a virtuális gépeket el szeretné látni. Ebben a példában a forrás helye: "Kelet-Ázsia."
    - **Üzembe helyezési modell**: a forrásoldali gépek Azure-beli üzembe helyezési modellje.
    - **Forrás-előfizetés**: az előfizetés, amelyhez a forrás virtuális gépek tartoznak. Minden olyan előfizetés lehet, amely ugyanabban a Azure Active Directory bérlőben található, mint a Recovery Services-tároló.
    - **Erőforráscsoport**: az erőforráscsoport, amelyhez a forrás virtuális gépek tartoznak. A következő lépésben a kiválasztott erőforráscsoport összes virtuális gépe szerepel a védelemben.

3. **Virtual Machines**  >  **válassza a virtuális gépek lehetőséget**, és válassza ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ez után válassza az **OK** gombot.

4. A **Beállítások** területen a következő cél-hely beállításokat állíthatja be.

    - **Cél helye**: a forrásként szolgáló virtuális gép adatai replikálásának helye. Site Recovery a kiválasztott gép helyétől függően a megfelelő célcsoportok listáját jeleníti meg. Azt javasoljuk, hogy ugyanazt a helyet használja, mint a Recovery Services tároló helye.
    - **Cél-előfizetés**: a vész-helyreállítási célra használt cél-előfizetés. Alapértelmezés szerint a cél-előfizetés megegyezik a forrás-előfizetéssel.
    - **Cél erőforráscsoport**: az az erőforráscsoport, amelyhez a replikált virtuális gépek tartoznak. Alapértelmezés szerint a Site Recovery új erőforráscsoportot hoz létre a célként megadott régióban. A név lekéri az `asr` utótagot. Ha már létezik olyan erőforráscsoport, amelyet Azure Site Recovery hozott létre, a rendszer újra felhasználja. Azt is megteheti, hogy testreszabja, ahogy az a következő szakaszban is látható. A célként megadott erőforráscsoport helye bármely Azure-régió lehet, kivéve a forrásként szolgáló virtuális gépeket tároló régiót.
    - **Cél virtuális hálózat**: alapértelmezés szerint a site Recovery új virtuális hálózatot hoz létre a céltartományban. A név lekéri az `asr` utótagot. Ez a forrás-hálózatra van leképezve, és minden jövőbeli védelemhez használatos. [További](./azure-to-azure-network-mapping.md) információ a hálózati leképezésről.
    - **Cél Storage-fiókok (ha a forrás virtuális gép nem használ felügyelt lemezeket)**: alapértelmezés szerint a site Recovery egy új célként megadott Storage-fiókot hoz létre a forrásként szolgáló virtuális gép tárolási konfigurációjának használatával. Ha már létezik egy Storage-fiók, az újra felhasználható.
    - **Replikált felügyelt lemezek (ha a forrás virtuális gép felügyelt lemezeket használ)**: a site Recovery új replika felügyelt lemezeket hoz létre a céltartományban, hogy a forrás virtuális gép felügyelt lemezeit a forrásként szolgáló virtuális gép felügyelt lemezei alapján tükrözze (standard vagy prémium).
    - **Gyorsítótár-tárolási fiókok**: site Recovery a forrás régióban a *cache Storage* nevű további Storage-fiókra van szükség. A rendszer a forrásként szolgáló virtuális gépek összes módosítását nyomon követi és továbbítja a gyorsítótárbeli Storage-fiókba. Ezután a rendszer replikálja őket a célhelyre.
    - **Rendelkezésre állási csoport**: alapértelmezés szerint a site Recovery új rendelkezésre állási készletet hoz létre a célként megadott régióban. A név `asr` utótagot tartalmaz. Ha Site Recovery által létrehozott rendelkezésre állási csoport már létezik, a rendszer újra felhasználja.
    - **Lemezes titkosítási készletek (des)**: site Recovery a replikához és a célként felügyelt lemezekhez használandó lemez-titkosítási készlet (eke) t igényli. A replikáció engedélyezése előtt előre létre kell hoznia a DES-t a cél előfizetésben és a célként megadott régióban. Alapértelmezés szerint a DES nincs kiválasztva. A "Testreszabás" gombra kattintva válassza ki a DES-t a forrásoldali lemezen.
    - **Replikációs házirend**: a helyreállítási pontok megőrzési előzményeinek és az alkalmazás-konzisztens Pillanatképek gyakoriságának beállításait határozza meg. Alapértelmezés szerint a Site Recovery egy új replikációs házirendet hoz létre, amely a helyreállítási pontok megőrzésének *24 óráját* és *60 percet* vesz igénybe az alkalmazás-konzisztens pillanatkép gyakorisága esetében.

    ![A CMK-kompatibilis lemezekkel rendelkező gép replikálásának engedélyezése](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Cél erőforrások testreszabása

Az alábbi lépéseket követve módosíthatja a Site Recovery alapértelmezett célhelyének beállításait.

1. Az alapértelmezett cél-előfizetés módosításához kattintson a "cél előfizetés" elem melletti **Testreszabás** lehetőségre. Válassza ki az előfizetést az Azure AD-bérlőben elérhető előfizetések listájából.

2. A következő alapértelmezett beállítások módosításához válassza az "erőforráscsoport, hálózat, tárterület és rendelkezésre állási csoportok" melletti **Testreszabás** lehetőséget:
    - A **cél erőforráscsoport** területen válassza ki az erőforráscsoportot az előfizetés célhelyén található erőforráscsoportok listájából.
    - A **célként megadott virtuális hálózat** esetében válassza ki a hálózatot a célhelyen lévő virtuális hálózatok listájából.
    - A **rendelkezésre állási csoport** számára a rendelkezésre állási csoport beállításait hozzáadhatja a virtuális géphez, ha azok egy rendelkezésre állási csoport részét képezik a forrás régióban.
    - A **cél Storage-fiókok** esetében válassza ki a használni kívánt fiókot.

3. A "Storage encryption settings" elem melletti **Testreszabás** lehetőség kiválasztásával kiválaszthatja a cél des-t minden ügyfél által felügyelt kulcs (CMK) számára engedélyezett forrás által felügyelt lemezre. A kiválasztás időpontjában látni fogja, hogy a DES melyik kulcstároló-tárolóhoz van társítva.

4. Válassza a **cél erőforrás létrehozása** a  >  **replikáció engedélyezése** lehetőséget.
5. Miután a virtuális gépek engedélyezettek a replikáláshoz, a virtuális gépek állapotát a **replikált elemek** területen tekintheti meg.

![A virtuális gépek állapotának ellenőrzését bemutató képernyőkép.](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>A kezdeti replikálás során előfordulhat, hogy az állapot frissítése hosszabb ideig is eltarthat. Kattintson a **frissítés**  gombra a legutóbbi állapot lekéréséhez.

## <a name="faqs"></a>Gyakori kérdések

* Engedélyeztem a CMK egy meglévő replikált elemen, Hogyan biztosíthatom, hogy a CMK az adott régióban is alkalmazza?

    A replika által felügyelt lemez neve (a célként megadott régióban Azure Site Recovery létrehozva) és a DES csatlakoztatása ehhez a replika-lemezhez. Ha azonban a csatlakoztatása után nem fogja tudni megtekinteni a DES-adatokat a lemezek panelen. Azt is megteheti, hogy letiltja a virtuális gép replikálását, és újból engedélyezi azt. Gondoskodik róla, hogy a DES és Key Vault adatait a replikált elemek lemezei paneljén lássuk.

* Új CMK-kompatibilis lemez lett hozzáadva a replikált objektumhoz. Hogyan replikálható a lemez Azure Site Recovery használatával?

    A meglévő replikált elemek új CMK-kompatibilis lemezének hozzáadása nem támogatott. Tiltsa le a replikációt, és engedélyezze újra a replikációt a virtuális gép számára.

* Engedélyeztem mind a platformot, mind az ügyfél által felügyelt kulcsokat, Hogyan biztosíthatom a lemezeket?

    A kettős titkosítás engedélyezése a platformmal és az ügyfél által felügyelt kulcsokkal Site Recovery suppprted. A gép biztonsága érdekében kövesse a jelen cikk utasításait. Előre létre kell hoznia egy Double encryption enabled DES-t a célként megadott régióban. A virtuális gép replikálásának engedélyezésekor ezt a DES-t megadhatja Site Recovery.

