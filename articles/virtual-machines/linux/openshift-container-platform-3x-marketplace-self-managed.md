---
title: A OpenShift Container platform 3,11 Self-Managed Marketplace-ajánlat üzembe helyezése az Azure-ban
description: A OpenShift Container platform 3,11 Self-Managed Marketplace-ajánlatának üzembe helyezése az Azure-ban.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670446"
---
# <a name="configure-prerequisites"></a>Előfeltételek konfigurálása

Mielőtt felhasználja a Piactéri ajánlatot egy önállóan felügyelt OpenShift Container platform 3,11-fürt üzembe helyezéséhez az Azure-ban, néhány előfeltételt be kell állítani.  A [OpenShift előfeltételeit](./openshift-container-platform-3x-prerequisites.md) ismertető cikkben megtudhatja, hogyan hozhat létre SSH-kulcsot (jelszó nélkül), az Azure Key vaultot, a Key Vault secrett és egy egyszerű szolgáltatást.

 
## <a name="deploy-using-the-marketplace-offer"></a>Üzembe helyezés a Piactéri ajánlat használatával

Az önfelügyelt OpenShift-tároló platform 3,11-fürtnek az Azure-ba történő üzembe helyezésének legegyszerűbb módja az [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)használata.

Ez a legegyszerűbb, de korlátozott testreszabási lehetőségekkel is rendelkezik. A Piactéri ajánlat üzembe helyezi a OpenShift-tároló platformjának 3.11.82, és a következő konfigurációs beállításokat tartalmazza:

- **Főcsomópontok**: három (3) fő csomópont konfigurálható példány típussal.
- **Infra-csomópontok**: három (3) infra csomópont konfigurálható példány típussal.
- **Csomópontok**: a csomópontok száma (1 és 9 között), a példány típusa pedig konfigurálható.
- **Lemez típusa**: Managed Disks van használatban.
- **Hálózatkezelés**: új vagy meglévő hálózati és egyéni CIDR-tartomány támogatása.
- **CNS**: a CNS engedélyezve lehet.
- **Metrikák**: az Hawkular-metrikák engedélyezhetők.
- **Naplózás**: a EFK naplózása engedélyezve lehet.
- Az **Azure Cloud Provider**: alapértelmezés szerint engedélyezve van, le lehet tiltani.

A Azure Portal bal felső részén kattintson az **erőforrás létrehozása** elemre, írja be a "openshift Container platform" kifejezést a keresőmezőbe, és nyomja le az ENTER billentyűt.

   ![Új erőforrás-keresés](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Az eredmények lap a listában a **Red Hat OpenShift Container Platform 3,11-** as verziójában nyílik meg. 

   ![Új erőforrás-keresés eredménye](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kattintson az ajánlatra az ajánlat részleteinek megtekintéséhez. Az ajánlat üzembe helyezéséhez kattintson a **Létrehozás** gombra. Ekkor megjelenik a szükséges paraméterek megadására szolgáló felhasználói felület. Az első képernyő az **alapvető beállítások** panel.

   ![Ajánlat címe lap](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Alapvető beállítások**

Ha segítséget szeretne kérni bármelyik bemeneti paraméterről, vigye a kurzort ***a paraméter neve melletti*** fölé.

Adja meg a bemeneti paraméterek értékeit, majd kattintson **az OK** gombra.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Virtuális gép rendszergazdájának felhasználóneve | Az összes virtuálisgép-példányon létrehozandó rendszergazda felhasználó |
| Nyilvános SSH-kulcs rendszergazdai felhasználóhoz | Virtuális gépre való bejelentkezéshez használt nyilvános SSH-kulcs – nem lehet hozzáférési kód |
| Előfizetés | Azure-előfizetés a fürt üzembe helyezéséhez |
| Erőforráscsoport | Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő üres erőforráscsoportot a fürt erőforrásaihoz |
| Hely | Azure-régió a fürt üzembe helyezéséhez |

   ![Ajánlat alapjai panel](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Infrastruktúra-beállítások**

Adja meg a bemeneti paraméterek értékeit, majd kattintson **az OK** gombra.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| OCP-fürt neve előtag | Az összes csomóponthoz tartozó állomásnevek konfigurálásához használt fürt-előtag. 1 és 20 karakter között |
| Fő csomópont mérete | Fogadja el az alapértelmezett virtuálisgép-méretet, vagy kattintson a **méret módosítása** lehetőségre egy másik virtuálisgép-méret kiválasztásához.  Válassza ki a megfelelő virtuálisgép-méretet a munkahelyi terheléshez |
| Infrastruktúra-csomópont mérete | Fogadja el az alapértelmezett virtuálisgép-méretet, vagy kattintson a **méret módosítása** lehetőségre egy másik virtuálisgép-méret kiválasztásához.  Válassza ki a megfelelő virtuálisgép-méretet a munkahelyi terheléshez |
| Alkalmazás-csomópontok száma | Fogadja el az alapértelmezett virtuálisgép-méretet, vagy kattintson a **méret módosítása** lehetőségre egy másik virtuálisgép-méret kiválasztásához.  Válassza ki a megfelelő virtuálisgép-méretet a munkahelyi terheléshez |
| Alkalmazás-csomópont mérete | Fogadja el az alapértelmezett virtuálisgép-méretet, vagy kattintson a **méret módosítása** lehetőségre egy másik virtuálisgép-méret kiválasztásához.  Válassza ki a megfelelő virtuálisgép-méretet a munkahelyi terheléshez |
| Megerősített gazdagép mérete | Fogadja el az alapértelmezett virtuálisgép-méretet, vagy kattintson a **méret módosítása** lehetőségre egy másik virtuálisgép-méret kiválasztásához.  Válassza ki a megfelelő virtuálisgép-méretet a munkahelyi terheléshez |
| Új vagy meglévő Virtual Network | Új vNet létrehozása (alapértelmezett) vagy meglévő vNet használata |
| Alapértelmezett CIDR-beállítások kiválasztása vagy az IP-címtartomány testreszabása (CIDR) | Fogadja el az alapértelmezett CIDR-tartományokat, vagy válassza az **Egyéni IP-címtartomány** lehetőséget, és adja meg az egyéni CIDR  Az alapértelmezett beállítások a vNet-t, a CIDR-t, a 10.0.0.0/14-et, a 10.1.0.0/16-ot, az infra alhálózatot 10.2.0.0/16, valamint a számítási és a CNS-alhálózatot 10.3.0.0/16 |
| Key Vault erőforráscsoport neve | Az Key Vault tartalmazó erőforráscsoport neve |
| Key Vault neve | Annak a Key Vaultnak a neve, amely a titkos SSH-kulccsal rendelkező titkot tartalmazza.  Csak alfanumerikus karakterek és kötőjelek megengedettek, és 3 – 24 karakter közé kell esnie. |
| Titok neve | Az SSH titkos kulcsot tartalmazó titok neve.  Csak alfanumerikus karakterek és kötőjelek engedélyezettek |

   ![Infrastruktúra-ajánlati panel](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Méret módosítása**

Egy másik virtuálisgép-méret kiválasztásához kattintson a ***méret módosítása** _ elemre.  Ekkor megnyílik a virtuális gép kiválasztási ablaka.  Válassza ki a kívánt virtuálisgép-méretet, és kattintson az _ * Select * * (* *) elemre.

   ![Virtuális gép méretének kiválasztása](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Meglévő Virtual Network**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Meglévő Virtual Network neve | A meglévő vNet neve |
| Fő csomópontok alhálózatának neve | A fő csomópontok meglévő alhálózatának neve.  Legalább 16 IP-címet kell tartalmaznia, és követnie kell az RFC 1918-et |
| Az infra-csomópontok alhálózatának neve | Az infra-csomópontok meglévő alhálózatának neve.  Legalább 32 IP-címet kell tartalmaznia, és követnie kell az RFC 1918-et |
| A számítási és CNS-csomópontok alhálózatának neve | A meglévő alhálózat neve a számítási és a CNS-csomópontokhoz.  Legalább 32 IP-címet kell tartalmaznia, és követnie kell az RFC 1918-et |
| Erőforráscsoport a meglévő Virtual Network | A meglévő vNet tartalmazó erőforráscsoport neve |

   ![Infrastruktúra meglévő vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Egyéni IP-címtartomány**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Címtartomány a Virtual Network | A vNet egyéni CIDR |
| A fő csomópontokat tartalmazó alhálózat címtartomány | Egyéni CIDR a fő alhálózathoz |
| Az infrastruktúra-csomópontokat tartalmazó alhálózat címtartomány | Egyéni CIDR az infrastruktúra-alhálózathoz |
| A számítási és CNS-csomópontokat tartalmazó alhálózat címtartomány | A számítási és a CNS-csomópontok egyéni CIDR |

   ![Ajánlat-infrastruktúra egyéni IP-tartománya](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**Az OpenShift-tárolóplatform 3.11-es verziója**

Adja meg a bemeneti paraméterek értékeit, majd kattintson **az OK** gombra.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| OpenShift rendszergazdai felhasználói jelszava | A kezdeti OpenShift-felhasználó jelszava.  Ez a felhasználó a fürt rendszergazdája is lesz. |
| OpenShift rendszergazdai felhasználói jelszavának megerősítése | Írja be újra a OpenShift-rendszergazda felhasználói jelszavát |
| Red Hat előfizetés-kezelő felhasználóneve | A Red Hat-előfizetés vagy a szervezet AZONOSÍTÓjának eléréséhez használt Felhasználónév.  Ezzel a hitelesítő adatokkal regisztrálja az RHEL-példányt az előfizetésében, és nem a Microsoft vagy a Red Hat tárolja. |
| Red Hat előfizetés-kezelő felhasználói jelszava | A Red Hat-előfizetéshez vagy az aktiválási kulcshoz való hozzáféréshez használt jelszó.  Ezzel a hitelesítő adatokkal regisztrálja az RHEL-példányt az előfizetésében, és nem a Microsoft vagy a Red Hat tárolja. |
| Red Hat előfizetés-kezelő OpenShift-készlet azonosítója | Az OpenShift-tároló platformjának jogosultságát tartalmazó készlet azonosítója. Győződjön meg arról, hogy elegendő jogosultsága van a OpenShift tároló platform számára a fürt telepítéséhez |
| Red Hat előfizetés-kezelő OpenShift-készlet azonosítója a Broker/főcsomópontok számára | A OpenShift tároló platformra vonatkozó jogosultságokat tartalmazó készlet azonosítója a Broker/főcsomópontok számára. Győződjön meg arról, hogy elegendő jogosultsága van a OpenShift tároló platform számára a fürt telepítéséhez. Ha nem a Broker/Master Pool ID-t használja, adja meg az alkalmazás-csomópontok készletének AZONOSÍTÓját. |
| Az Azure Cloud Provider konfigurálása | Konfigurálja a OpenShift az Azure Cloud Provider használatára. Akkor szükséges, ha az Azure Disk Attach-t használ állandó kötetekhez.  Az alapértelmezett érték az igen |
| Azure AD egyszerű szolgáltatásnév – ügyfél-azonosító GUID | Azure AD egyszerű ügyfél-azonosító GUID – más néven AppID. Csak akkor szükséges, ha az Azure Cloud Provider konfigurálása **Igen** értékre van állítva |
| Azure AD szolgáltatás egyszerű ügyfél-AZONOSÍTÓjának titka | Az Azure AD szolgáltatás egyszerű ügyfél-AZONOSÍTÓjának titka. Csak akkor szükséges, ha az Azure Cloud Provider konfigurálása **Igen** értékre van állítva |
 
   ![Ajánlat OpenShift panel](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**További beállítások**

A további beállítások panelen engedélyezheti a glusterfs-tárolás, a naplózás, a metrikák és az útválasztó altartomány konfigurálását.  Az alapértelmezett érték nem telepíti ezeket a beállításokat, és a nip.io-t az útválasztó altartományként fogja használni tesztelési célokra. A CNS engedélyezése három további számítási csomópontot telepít három további csatlakoztatott lemezzel, amely glusterfs-hüvelyeket fog üzemeltetni.  

Adja meg a bemeneti paraméterek értékeit, majd kattintson **az OK** gombra.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Tároló natív tárolójának konfigurálása (CNS) | Telepíti a CNS-t a OpenShift-fürtben, és engedélyezi azt tárolóként. Ha az Azure-szolgáltató le van tiltva, az alapértelmezett érték lesz. |
| A fürt naplózásának konfigurálása | Telepíti a EFK naplózási funkcióit a fürtbe.  A EFK-hüvelyek üzemeltetéséhez megfelelő méretű infra-csomópontok |
| Metrikák konfigurálása a fürthöz | Telepíti a Hawkular-metrikákat a OpenShift-fürtbe.  Az Hawkular-metrikai hüvelyek tárolásához megfelelő méretű infra-csomópontok |
| Alapértelmezett útválasztó altartománya | Válassza a nipio lehetőséget a teszteléshez vagy az egyéni beállításhoz a saját altartományának megadásához éles környezetben |
 
   ![További panel ajánlat](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**További beállítások – további paraméterek**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| CNS Csomópont mérete | Az új virtuálisgép-méret kiválasztásához fogadja el az alapértelmezett csomópont-méretet, vagy válassza a **méret módosítása** lehetőséget. |
| Adja meg az egyéni altartományt | Az alkalmazásoknak az OpenShift-fürtön keresztül az útválasztón keresztül történő kiküldéséhez használandó egyéni útválasztási tartomány.  Győződjön meg arról, hogy a megfelelő helyettesítő DNS-bejegyzést hozza létre] |
 
   ![További telepítési CNS-telepítés](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Összefoglalás**

Ezen a ponton az ellenőrzés az alapvető kvóta ellenőrzéséhez elegendő a fürthöz kiválasztott virtuális gépek teljes számának üzembe helyezéséhez.  Tekintse át az összes megadott paramétert.  Ha a bemenetek elfogadhatók, kattintson **az OK** gombra a folytatáshoz.

   ![Ajánlat összegzése panel](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Vásárlás**

Erősítse meg a kapcsolattartási adatokat a vásárlás lapon, és kattintson a **vásárlás** gombra a használati feltételek elfogadásához és a OpenShift-tároló platform fürt üzembe helyezésének megkezdéséhez.

   ![Ajánlat vásárlása panel](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Kapcsolódás a OpenShift-fürthöz

Az üzembe helyezés befejezésekor a rendszer lekéri a kapcsolódást a központi telepítés kimenet szakaszából. Kapcsolódjon a OpenShift-konzolhoz a böngészőben a **OpenShift-konzol URL-címének** használatával. azt is megteheti, hogy SSH-t használ a megerősített gazdagépen. Az alábbi példa egy olyan példát mutat be, ahol a rendszergazdai Felhasználónév clusteradmin, a megerősített nyilvános IP-cím DNS teljes tartományneve pedig bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az az [Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot, a OpenShift-fürtöt és az összes kapcsolódó erőforrást, ha már nincs rájuk szükség.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [A OpenShift üzembe helyezésének hibája az Azure-ban](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift-tároló platform – első lépések](https://docs.openshift.com)
- 
