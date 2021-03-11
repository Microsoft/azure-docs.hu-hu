---
title: Az Azure-beli Linux rendszerű virtuális gépek Cloud-init támogatásának áttekintése
description: A Cloud-init képességeinek áttekintése a virtuális gép konfigurálásához az Azure-ban kiépített időpontban.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/14/2021
ms.author: danis
ms.openlocfilehash: ac907c2ea2ae53bd192c01232c66e0467025daae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563233"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init támogatás az Azure-beli virtuális gépekhez
Ez a cikk ismerteti a [Cloud-init](https://cloudinit.readthedocs.io) számára elérhető támogatást a virtuális gép (VM) vagy virtuálisgép-méretezési csoportok konfigurálásához az Azure üzembe helyezési idején. Ezek a Cloud-init konfigurációk az első rendszerindítás során futnak az Azure-erőforrások kiépítése után.  

A virtuális gépek üzembe helyezése az a folyamat, amelyben az Azure leállítja a virtuális gépet, így például az állomásnév, a Felhasználónév, a jelszó stb., és elérhetővé teheti őket a virtuális gép számára, ahogy elindul. A "kiépítési ügynök" felhasználja ezeket az értékeket, konfigurálja a virtuális gépet, és visszaküldi a jelentést, amikor a művelet befejeződött. 

Az Azure két üzembe helyezési ügynököt támogat a [Cloud-init](https://cloudinit.readthedocs.io)és az [Azure Linux Agent (Wala)](../extensions/agent-linux.md)számára.

## <a name="cloud-init-overview"></a>Cloud-init – áttekintés
a [Cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux RENDSZERű virtuális gépek első indításakor való testre szabására. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.  A `#cloud-config` fájlok vagy más bemenetek megfelelő formázásáról további információt a [Cloud-init dokumentációs webhelyén](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)talál.  `#cloud-config` a fájlok a Base64 kódolású szövegfájlok.

a Cloud-init a disztribúciók között is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. a Cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

Aktívan dolgozunk a támogatott linuxos disztribúciós partnereinkkel, hogy elérhetők legyenek a Cloud-init-lemezképek az Azure piactéren. Ezek a lemezképek a felhő-init üzembe helyezések és konfigurációk zökkenőmentesen működnek a virtuális gépekkel és a virtuálisgép-méretezési csoportokkal. Kezdetben a támogatott Linux-disztribúciós partnerekkel és felsőbb réteggel együttműködve biztosítjuk, hogy a Cloud-init functions az Azure-beli operációs rendszerrel, a csomagok frissítése és nyilvánosan elérhetővé tétele a disztribúciós csomag adattárakban történik. 

A Cloud-init két fázisban érhető el az Azure-beli támogatott Linux-disztribúciós operációs rendszerhez, a csomagok támogatásához, majd a rendszerkép támogatásához:
* a Cloud-init csomag támogatása az Azure-ban olyan dokumentumokon, amelyeket a Cloud-init csomagok már támogatnak vagy előzetes verzióként, így ezeket a csomagokat az operációs rendszer egyéni rendszerképében is használhatja.
* "rendszerkép-felhő – init Ready" dokumentumok, ha a rendszerkép már konfigurálva van a Cloud-init használatára.


### <a name="canonical"></a>Canonical
| Közzétevő/verzió| Ajánlat | Termékváltozat | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20,04 |UbuntuServer |18,04 – LTS |legújabb |igen | igen |
|Canonical 18,04 |UbuntuServer |18,04 – LTS |legújabb |igen | igen |
|Canonical 16,04|UbuntuServer |16.04-LTS |legújabb |igen | igen |
|Canonical 14,04|UbuntuServer |14.04.5-LTS |legújabb |igen | igen |

### <a name="rhel"></a>RHEL
| Közzétevő/verzió | Ajánlat | Termékváltozat | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |igen | igen – a csomag verziószámának támogatása: *18.2 – 1.el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | Igen (Megjegyzés: ez egy előnézeti kép, **és nem** használható többé, ezért ez a 2020. szeptember 1-től lesz eltávolítva) | N/A |
|RedHat 7,7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | igen | igen – a csomag verziójának támogatása: *18,5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 77 – Gen2 | 7.7.2020051913 | igen | igen – a csomag verziójának támogatása: *18,5 -6. el7*|
|RedHat 7,7 (Gen1)|RHEL |7 – LVM | 7.7.2020051921 | igen | igen – a csomag verziójának támogatása: *18,5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 7lvm – Gen2 | 7.7.2020051922  | igen | igen – a csomag verziójának támogatása: *18,5 -6. el7*|
|RedHat 7,7 (Gen1) |RHEL – BYOS | RHEL – lvm77 | 7.7.20200416 | igen  | igen – a csomag verziójának támogatása: *18,5 -6. el7*|
|RedHat 8,1 (Gen1) |RHEL |8,1 – CI |8.1.2020042511 | Igen (Megjegyzés: ez egy előnézeti kép, és ha az összes RHEL 8,1-lemezkép támogatja a Cloud-init-et, akkor a rendszer eltávolítja az 2020. augusztus 1-től) | Nem, ETA a teljes támogatásért 2020. június|
|RedHat 8,1 (Gen2) |RHEL |81 – CI-Gen2 |8.1.2020042524 | Igen (Megjegyzés: ez egy előnézeti kép, és ha az összes RHEL 8,1-lemezkép támogatja a Cloud-init-et, akkor a rendszer eltávolítja az 2020. augusztus 1-től) | Nem, ETA a teljes támogatásért 2020. június |

* Az összes RedHat: RHEL 7,8 és 8,2 (Gen1 és Gen2) lemezkép a Cloud-init használatával lett kiépítve.

### <a name="centos"></a>CentOS

| Közzétevő/verzió | Ajánlat | Termékváltozat | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Igen (Megjegyzés: ez egy előnézeti kép, **és nem** használható többé, ezért ez a 2020. szeptember 1-től lesz eltávolítva) | N/A |
|OpenLogic 7,7 |CentOS | 7.7 |7.7.2020062400 |igen | igen – a csomag verziójának támogatása: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS | 7_7 – Gen2 |7.7.2020062401 |igen | igen – a csomag verziójának támogatása: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 |CentOS – HPC | 7.7 |7.6.2020062600 |igen | igen – a csomag verziójának támogatása: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS – HPC | 7_7 – Gen2 |7.6.2020062601 |igen | igen – a csomag verziójának támogatása: `18.5-6.el7.centos.5`|
|OpenLogic 8,1 |CentOS | 8_1 |8.1.2020062400 |igen | igen – a csomag verziójának támogatása: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS | 8_1 – Gen2 |8.1.2020062401 |igen | igen – a csomag verziójának támogatása: `18.5-7.el8_1.1`|
|OpenLogic 8,1 |CentOS – HPC | 8_1 |8.1.2020062400 |igen | igen – a csomag verziójának támogatása: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS-HPC: 8_1-Gen2 | 8_1 – Gen2 |8.1.2020062401 |igen | igen – a csomag verziójának támogatása: `18.5-7.el8_1.1`|

* Minden OpenLogic: CentOS 7,8 és 8,2 (Gen1 és Gen2) lemezképek kiépítve a Cloud-init használatával.

### <a name="oracle"></a>Oracle

| Közzétevő/verzió | Ajánlat | Termékváltozat | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77 – CI |7.7.01| előzetes rendszerkép (Megjegyzés: ez egy előzetes rendszerkép, és az összes Oracle 7,7-lemezkép támogatja a Cloud-2020 init használatát | nem, az előzetes verzióban a csomag a következőket eredményezi: *18,5-3.0.1. el7*

### <a name="suse-sles"></a>SUSE SLES
Ezek a SLES-lemezképek a Cloud-init használatával lettek kiépítve, a Gen2 lemezkép változatait is frissítették.
* SUSE: SLES-15-SP1-{Basic/BYOS/HPC/HPC-BYOS/chost-BYOS}: gen1:2020.06.10
* SUSE: SLES-SAP-15-SP1: gen1:2020.06.10
* SUSE: SLES-SAP-15-SP1-BYOS: gen1:2020.06.10
* SUSE: Manager-proxy-4-BYOS: gen1:2020.06.10
* SUSE: Manager-Server-4-BYOS: gen1:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 15:2020.06.10
* SUSE: SLES-12-SP5: gen1:2020.06.10
* SUSE: SLES-12-SP5 {-BYOS/Basic/HPC-BYOS/HPC}: gen1:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP4:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP3:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP2:2020.06.10


### <a name="debian"></a>Debian
| Közzétevő/verzió | Ajánlat | Termékváltozat | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
| Debian (Gen1) |Debian – 10 | 10 – cloudinit |Cloud-init – előzetes verzió| Igen (Megjegyzés: ez egy előnézeti kép, **és nem használható többé, ezért ez** a 2021. január 1-től lesz eltávolítva) | Nem, előzetes verzióban. |
| Debian (Gen2) |Debian – 10 | 10 – cloudinit – Gen2 |Cloud-init – előzetes verzió| Igen (Megjegyzés: ez egy előnézeti kép, **és nem használható többé, ezért ez** a 2021. január 1-től lesz eltávolítva) | Nem, előzetes verzióban. |
| Debian (Gen1) |Debian – 10 | 10 – cloudinit |10:0.20201013.422| igen | igen – a csomag verziójának támogatása: `20.2-2~deb10u1` |
| Debian (Gen2) |Debian – 10 | 10 – cloudinit – Gen2 |0.20201013.422| igen | igen – a csomag verziójának támogatása: `20.2-2~deb10u1` |


Jelenleg Azure Stack támogatni fogja a Cloud-init-kompatibilis lemezképek kiépítési folyamatát.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Mi a különbség a Cloud-init és a Linux-ügynök (WALA) között?
A WALA egy Azure platform-specifikus ügynök, amely virtuális gépek kiépítésére és konfigurálására, valamint az [Azure-bővítmények](../extensions/features-linux.md)kezelésére szolgál. 

A virtuális gépeknek a Linux-ügynök helyett a Cloud-init használatára való konfigurálásának feladatát fejlesztjük annak érdekében, hogy a meglévő felhő-init-ügyfelek használhassák a jelenlegi felhőalapú init-parancsfájlokat, vagy új ügyfeleket használjanak a Cloud-init konfigurációs funkcióinak kihasználásához. Ha meglévő beruházásai vannak a Cloud-init parancsfájlokban a linuxos rendszerek konfigurálásához, **nincs szükség további beállításokra** a Cloud-init folyamat engedélyezéséhez. 

a Cloud-init nem tudja feldolgozni az Azure-bővítményeket, ezért a lemezképben továbbra is szükség van a bővítmények feldolgozására, de a kiépítési kód le lesz tiltva, a támogatott Linux-disztribúciók által a Cloud-init által kiépített lemezképek esetében, amelyekre a WALA telepítve lesz.

Virtuális gép létrehozásakor, ha nem tartalmazza az Azure CLI `--custom-data` kapcsolót a kiépítési időpontban, a Cloud-init vagy a Wala a virtuális gép kiépítéséhez szükséges minimális virtuálisgép-kiépítési paramétereket veszi igénybe, és a központi telepítést az alapértelmezett beállításokkal hajtja végre.  Ha a kapcsolóval hivatkozik a Cloud-init konfigurációra `--custom-data` , a virtuális gép indításakor az egyéni adataiban található bármilyen érték a Cloud-init számára elérhető lesz.

a virtuális gépekre alkalmazott felhő-init konfigurációk nem rendelkeznek időbeli korlátozásokkal, és nem eredményezi, hogy a központi telepítés időtúllépés miatt sikertelen lesz. Ez nem igaz a WALA esetében, ha megváltoztatja a WALA alapértelmezett értékeit az egyéni adat feldolgozásához, nem lépheti túl a virtuális gépek kiépítési idejének teljes 40mins, ha igen, a virtuális gép létrehozása sikertelen lesz.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Cloud-init-alapú virtuális gép üzembe helyezése
A Cloud-init-alapú virtuális gép üzembe helyezése olyan egyszerű, mint a felhő-init engedélyezett terjesztésre való hivatkozás az üzembe helyezés során.  A Linux-alapú terjesztők számára a Cloud-init engedélyezését és integrálását kell választania az alapszintű Azure közzétett lemezképekben. Miután megerősítette, hogy a telepíteni kívánt lemezkép a Cloud-init engedélyezve van, az Azure CLI használatával telepítheti a lemezképet. 

A rendszerkép üzembe helyezésének első lépéseként hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

A következő lépés egy fájl létrehozása az aktuális rendszerhéjban, *cloud-init.txt* elnevezve, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. A **Nano** Editor használatához válassza a #1 lehetőséget. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
> [!NOTE]
> a Cloud-init több [bemeneti típussal](https://cloudinit.readthedocs.io/en/latest/topics/format.html)rendelkezik, a Cloud-init a CustomData/userData első sorát használja annak jelzésére, hogy miként kell feldolgoznia a bemenetet, például `#cloud-config` azt jelzi, hogy a tartalmat a Cloud-init konfigurációként kell feldolgozni.


Nyomja le `ctrl-X` a fájlt a fájlból való kilépéshez, majd a fájl `y` mentéséhez nyomja meg a fájl nevét, és erősítse meg a `enter` kilépéskor.

Utolsó lépésként hozzon létre egy virtuális gépet az az [VM Create](/cli/azure/vm) paranccsal. 

A következő példa létrehoz egy *centos74* nevű virtuális gépet, és SSH-kulcsokat hoz létre, ha azok még nem léteznek az alapértelmezett kulcs helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

A virtuális gép létrehozása után az Azure CLI az üzemelő példányra vonatkozó információkat jeleníti meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.  Időbe telik a virtuális gép létrehozása, a telepítendő csomagok és az alkalmazás elindítása. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. A felhő-init naplók megtekintéséhez SSH-t használhat a virtuális gépre, és a hibaelhárítás szakaszban leírt lépéseket követve tekintheti meg. 

Az [ARM-sablon paramétereinek](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters)átadásával is üzembe helyezhet egy Felhőbeli init-alapú virtuális gépet.

## <a name="troubleshooting-cloud-init"></a>A Cloud-init hibaelhárítása
A virtuális gép üzembe helyezése után a Cloud-init a `--custom-data` virtuális gép konfigurálásához megadott összes modulon és parancsfájlon keresztül fut.  Ha a konfiguráció hibáit vagy kihagyásait szeretné elhárítani, keresse meg a modul nevét ( `disk_setup` vagy `runcmd` például) a **/var/log/Cloud-init.log**-ben található Cloud-init naplóban.

> [!NOTE]
> Nem minden modul meghibásodása végzetes felhő-init általános konfigurációs hibát eredményez. Ha például a `runcmd` modult használja, ha a parancsfájl meghibásodik, a Cloud-init továbbra is jelentést készít a kiépítés sikerességéről, mert a runcmd modul végre lett hajtva.

A Cloud-init naplózással kapcsolatos további részletekért tekintse meg a [Cloud-init dokumentációját](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Következő lépések

[A Cloud-init hibáinak elhárítása](cloud-init-troubleshooting.md).


A Cloud-init példákat a konfigurációs változásokról a következő dokumentumokban talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
