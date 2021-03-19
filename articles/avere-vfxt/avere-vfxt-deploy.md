---
title: Avere-vFXT üzembe helyezése az Azure-ban
description: Megtudhatja, hogyan használhatja az Azure Marketplace-en elérhető központi telepítési varázslót a fürt avere-vFXT az Azure-hoz való üzembe helyezéséhez.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 110fc658ffc3b72de91bbf1c9562f3efeecaf55b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342416"
---
# <a name="deploy-the-vfxt-cluster"></a>A vFXT-fürt üzembe helyezése

Ez az eljárás végigvezeti az Azure piactéren elérhető központi telepítési varázsló használatával. A varázsló Azure Resource Manager sablon használatával automatikusan telepíti a fürtöt. Miután megtörtént a paraméterek megadása az űrlapon, majd a **Létrehozás** gombra kattint, az Azure automatikusan végrehajtja ezeket a feladatokat:

* Létrehozza a fürtöt, amely egy alapszintű virtuális gép, amely tartalmazza a fürt üzembe helyezéséhez és kezeléséhez szükséges szoftvert.
* Beállítja az erőforráscsoportot és a virtuális hálózati infrastruktúrát, beleértve az új elemek létrehozását is.
* Létrehozza a fürtcsomópont virtuális gépeket, és konfigurálja őket avere-fürtként.
* Ha a rendszer kéri, egy új Azure BLOB-tárolót hoz létre, és a fürt Core Filer-ként konfigurálja azt.

A jelen dokumentumban szereplő utasítások követése után egy virtuális hálózattal, egy alhálózattal, egy fürttel és egy vFXT-fürttel fog rendelkezni, ahogy az a következő ábrán is látható. Ez az ábra a választható Azure Blob Core Filer-t mutatja be, amely tartalmaz egy új blob Storage-tárolót (új Storage-fiókban, nem jelenik meg) és egy szolgáltatási végpontot a Microsoft Storage-ban az alhálózaton belül.

![a avere-fürt összetevőivel három koncentrikus téglalapot ábrázoló diagram. A külső négyszög "erőforráscsoport" címkével rendelkezik, és a "blob Storage (nem kötelező)" címkével ellátott hatszög szerepel. A következő téglalap a "Virtual Network: 10.0.0.0/16" címkével van ellátva, és nem tartalmaz egyedi összetevőket. A legbelső téglalap az "alhálózat: 10.0.0.0/24" címkével rendelkezik, és tartalmaz egy "fürtcsomópont" címkével ellátott virtuális gépet, egy "vFXT-csomópontok (vFXT-fürt)" címkével ellátott három virtuális gép, valamint egy "szolgáltatási végpont" nevű hatszöget. Van egy nyíl, amely összeköti a szolgáltatási végpontot (amely az alhálózaton belül van) és a blob Storage-t (amely kívül esik az alhálózaton és a vnet, az erőforráscsoporthoz). A nyíl az alhálózat és a virtuális hálózat határain halad át.](media/avere-vfxt-deployment.png)

A létrehozási sablon használata előtt győződjön meg arról, hogy az alábbi előfeltételeket tárgyalta:  

* [Új előfizetés](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Előfizetés tulajdonosának engedélyei](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Kvóta a vFXT-fürthöz](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Storage Service-végpont (ha szükséges)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – a meglévő virtuális hálózatot használó központi telepítések és a blob Storage létrehozása szükséges.

További információ a fürtök üzembe helyezésének lépéseiről és tervezéséről: [a avere-vFXT rendszerének](avere-vfxt-deploy-plan.md) és [telepítésének áttekintése](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Az Azure-hoz készült avere-vFXT létrehozása

Nyissa meg a Azure Portal létrehozási sablonját a avere keresésével, és válassza a "avere vFXT for Azure ARM sablon" lehetőséget.

![A Azure Portalt tartalmazó böngészőablakban láthatók a "New > Marketplace > all" című rész. A minden oldalon a keresőmezőbe a "avere" kifejezés szerepel, a második pedig a "avere vFXT for Azure ARM Template" kifejezést piros színnel kiemelve.](media/avere-vfxt-template-choose.png)

Miután elolvasta a részleteket a avere vFXT for Azure ARM-sablon lapon, kattintson a **Létrehozás** gombra a kezdéshez.

![Az Azure Marketplace az üzembe helyezési sablon első oldalával, amely a következőt jeleníti meg](media/avere-vfxt-deploy-first.png)

A sablon négy lépésből áll – két információ gyűjtése lapokra, valamint az érvényesítési és a megerősítési lépésekre.

* Az egyik lapon a rendszer a fürthöz tartozó virtuális gép beállításait gyűjti.
* A két oldal paramétereket gyűjt a fürt létrehozásához és további erőforrásokhoz, például alhálózatokhoz és tárhelyekhez.
* A három oldal összegzi a beállításokat, és érvényesíti a konfigurációt.
* A négy oldal a szoftverek használati feltételeit és kikötéseit ismerteti, és lehetővé teszi a fürt létrehozási folyamatának elindítását.

## <a name="page-one-parameters---cluster-controller-information"></a>Az oldal egy paramétere – a fürt vezérlője információi

A központi telepítési sablon első lapja a fürt vezérlőre koncentrál.

![A központi telepítési sablon első lapja](media/avere-vfxt-deploy-1.png)

Adja meg a következő információkat:

* **Tartományvezérlő neve** – adja meg a fürt TARTOMÁNYVEZÉRLŐJE virtuális gép nevét.

* **Vezérlő felhasználóneve** – állítsa be a fürthöz tartozó virtuális gép legfelső szintű felhasználónevét.

* **Hitelesítés típusa** – válassza a jelszó vagy az SSH nyilvános kulcsú hitelesítés lehetőséget a vezérlőhöz való csatlakozáshoz. A nyilvános SSH-kulcs módszere ajánlott; Ha segítségre van szüksége, olvassa el, [hogyan hozhat létre és HASZNÁLHAT SSH-kulcsokat](../virtual-machines/linux/ssh-from-windows.md) .

* **Jelszó** vagy **nyilvános SSH-kulcs** – a kiválasztott hitelesítési típustól függően meg kell adnia egy RSA nyilvános kulcsot vagy jelszót a következő mezőkben. Ezt a hitelesítő adatot a korábban megadott felhasználónévvel kell használni.

* **Előfizetés** – válassza ki az előfizetést a avere vFXT.

* **Erőforráscsoport** – válasszon ki egy meglévő üres erőforráscsoportot a avere vFXT-fürthöz, vagy kattintson az "új létrehozása" elemre, és adjon meg egy új erőforráscsoport-nevet.

* **Hely** – válassza ki a fürt és az erőforrások Azure-beli helyét.

Ha elkészült, kattintson **az OK gombra** .

> [!NOTE]
> Ha azt szeretné, hogy a tartományvezérlő nyilvános IP-címmel rendelkezzen, hozzon létre egy új virtuális hálózatot a fürthöz egy meglévő hálózat kiválasztása helyett. Ez a beállítás a két oldalon található.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Oldal két paraméter – vFXT-fürt adatai

A telepítési sablon második lapja lehetővé teszi a fürt méretének, a csomópont típusának, a gyorsítótár méretének és a tárolási paraméterek beállítását más beállítások között.

![A központi telepítési sablon második lapja](media/avere-vfxt-deploy-2.png)

* **Avere vFXT-csomópontok** száma – válassza ki a fürt csomópontjainak számát. A minimum három csomópont, a maximum pedig tizenkét.

* **Fürtfelügyelő jelszava** – hozza létre a fürt felügyeletének jelszavát. A rendszer ezt a jelszót használja a felhasználónévvel ```admin``` a fürt Vezérlőpultján való bejelentkezéshez, ahol nyomon követheti a fürtöt, és konfigurálhatja a fürt beállításait.

* **Avere vFXT-fürt neve** – adjon egyedi nevet a fürtnek.

* **Méret** – ez a szakasz a fürtcsomópontok esetében használt virtuálisgép-típust jeleníti meg. Bár csak egy ajánlott lehetőség van, a **méret módosítása** hivatkozás egy olyan táblázatot nyit meg, amely tartalmazza az adott példány típusát és az ár-kalkulátorra mutató hivatkozást.

* **Gyorsítótár mérete/csomópont** – a fürt gyorsítótára a fürtcsomópontok között oszlik el, így a avere vFXT-fürt teljes gyorsítótárának mérete a csomópontok számával megszorozva lesz.

  Ajánlott konfiguráció: használjon 4 TB-ot Standard_E32s_v3 csomópontokhoz.

* **Virtuális hálózat** – adjon meg egy új virtuális hálózatot a fürt kiválasztásához, vagy válasszon egy meglévő hálózatot, amely megfelel a [avere vFXT-rendszer megtervezése](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)című témakörben ismertetett előfeltételeknek.

  > [!NOTE]
  > Ha új virtuális hálózatot hoz létre, a tartományvezérlő nyilvános IP-címmel fog rendelkezni, hogy hozzáférhessen az új magánhálózathoz. Ha meglévő virtuális hálózatot választ, a tartományvezérlő nyilvános IP-cím nélkül van konfigurálva.
  >
  > A fürt egy nyilvánosan látható IP-címe könnyebb hozzáférést biztosít a vFXT-fürthöz, de kis biztonsági kockázatot hoz létre.
  >* A fürt vezérlőjének nyilvános IP-címe lehetővé teszi, hogy Jump hostként használja a avere vFXT-fürthöz a privát alhálózaton kívülről való kapcsolódáshoz.
  >* Ha nem rendelkezik nyilvános IP-címmel a vezérlőn, szüksége lesz egy másik ugrási gazdagépre, egy VPN-kapcsolatra vagy egy ExpressRoute a fürt eléréséhez. Használjon például olyan meglévő virtuális hálózatot, amely már rendelkezik konfigurált VPN-kapcsolattal.
  >* Ha nyilvános IP-címmel rendelkező vezérlőt hoz létre, akkor a vezérlő virtuális gépet egy hálózati biztonsági csoporttal kell védelemmel ellátnia. Alapértelmezés szerint a avere vFXT for Azure üzembe helyezése egy hálózati biztonsági csoportot hoz létre, amely a nyilvános IP-címekkel rendelkező vezérlők esetében csak a 22-es portra korlátozza a bejövő hozzáférést. A rendszer további védettségét úgy biztosíthatja, hogy leállítja az IP-címek tartományához való hozzáférést, azaz csak a fürt eléréséhez használni kívánt gépek kapcsolatait engedélyezi.

  Egy új virtuális hálózat is konfigurálva van egy Storage Service-végponttal az Azure Blob Storage-hoz, és a hálózati hozzáférés-vezérlés zárolva értékkel csak a fürt alhálózatán lévő IP-címek engedélyezése.

* **Alhálózat** – válasszon egy alhálózatot, vagy hozzon létre újat.

* **Blob Storage létrehozása és használata** – válassza az **igaz** lehetőséget egy új Azure Blob-tároló létrehozásához, és konfigurálja háttérbeli tárolóként az új avere vFXT-fürthöz. Ez a beállítás egy új Storage-fiókot is létrehoz a fürt erőforráscsoporthoz, és létrehoz egy Microsoft Storage szolgáltatásbeli végpontot a fürt alhálózatán belül.
  
  Ha egy meglévő virtuális hálózatot ad meg, akkor a fürt létrehozása előtt rendelkeznie kell egy Storage Service-végponttal. (További információért olvassa el [a avere vFXT-rendszer megtervezése](avere-vfxt-deploy-plan.md)című témakört.)

  Ha nem szeretne új tárolót létrehozni, állítsa be a mezőt **hamis** értékre. Ebben az esetben a fürt létrehozása után csatolnia és konfigurálnia kell a tárolót. Útmutatásért olvassa el a [tároló konfigurálása](avere-vfxt-add-storage.md) című témakört.

* **(Új) Storage-fiók** – ha új Azure BLOB-tárolót hoz létre, adja meg az új Storage-fiók nevét.

## <a name="validation-and-purchase"></a>Ellenőrzés és vásárlás

A három oldal összegzi a konfigurációt, és érvényesíti a paramétereket. Az érvényesítés sikeres ellenőrzése után tekintse át az összegzést, és kattintson az **OK** gombra.

> [!TIP]
> A fürt létrehozási beállításainak mentéséhez kattintson az **OK** gomb melletti **sablon és paraméterek letöltése** hivatkozásra. Ez az információ akkor lehet hasznos, ha később egy hasonló fürtöt kell létrehoznia – például egy helyreállítási fürt létrehozásához vész-helyreállítási forgatókönyvben. (További információért olvassa el a vész- [helyreállítási útmutatót](disaster-recovery.md) .)

![A központi telepítési sablon harmadik lapja – ellenőrzés](media/avere-vfxt-deploy-3.png)

A négy. oldal a használati feltételeket és az adatvédelmi és díjszabási információkra mutató hivatkozásokat tartalmazza.

Adja meg a hiányzó kapcsolattartási adatokat, majd kattintson a **Létrehozás** gombra a feltételek elfogadásához és az Azure-fürthöz tartozó avere-vFXT létrehozásához.

![A központi telepítési sablon negyedik lapja – feltételek és kikötések, létrehozás gomb](media/avere-vfxt-deploy-4.png)

A fürt üzembe helyezése 15-20 percet vesz igénybe.

## <a name="gather-template-output"></a>Sablon kimenetének összegyűjtése

Amikor a avere vFXT-sablon befejezi a fürt létrehozását, fontos adatokat ad ki az új fürtről.

> [!TIP]
> Ügyeljen arra, hogy a **felügyeleti IP-címet** a sablon kimenetéről másolja. Ehhez a címnek kell lennie a fürt felügyeletéhez.

Az információk megkeresése:

1. Nyissa meg a fürthöz tartozó erőforráscsoportot.

1. A bal oldalon kattintson a **központi telepítések**, majd a **Microsoft-avere. vfxt-template** elemre.

   ![Erőforráscsoport-portál lap a bal oldalon és a Microsoft-avere. vfxt-templateben kiválasztott központi telepítések a központi telepítési név alatt lévő táblázatban](media/avere-vfxt-outputs-deployments.png)

1. A bal oldalon kattintson a **kimenetek** elemre. Másolja az értékeket az egyes mezőkbe.

   ![kimenetek oldal, amely a SSHSTRING, a RESOURCE_GROUP, a hely, a NETWORK_RESOURCE_GROUP, a hálózat, az ALHÁLÓZAT, a SUBNET_ID, a VSERVER_IPs és a MGMT_IP értékeket mutatja a címkéktől jobbra található mezőkben.](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy a fürt fut, és ismeri a felügyeleti IP-címét, [kapcsolódjon a fürt konfigurációs eszközéhez](avere-vfxt-cluster-gui.md).

A konfigurációs felületen testreszabhatja a fürtöt, beleértve a következő telepítési feladatokat:

* [Támogatás engedélyezése](avere-vfxt-enable-support.md)
* [Tár hozzáadása](avere-vfxt-add-storage.md) (ha szükséges)