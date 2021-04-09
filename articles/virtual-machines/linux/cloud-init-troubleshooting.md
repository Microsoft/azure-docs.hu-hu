---
title: Hibakeresés a Cloud-init használatával
description: Azure-beli virtuális gép üzembe helyezése a Cloud-init használatával – problémamegoldás.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 842107245fe26155d53866bf95e11b08d7593ad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582153"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>A virtuális gépek üzembe helyezésének hibaelhárítása a Cloud-init használatával

Ha általánosított egyéni rendszerképeket hozott létre, a Cloud-init használatával végezze el az üzembe helyezést, de úgy találta, hogy a virtuális gép nem megfelelően lett létrehozva, az egyéni lemezképek hibakeresése szükséges.

Néhány példa a kiépítési problémákra:
- A virtuális gép 40 percig elakad a létrehozáskor, és a virtuális gép létrehozása sikertelenként van megjelölve
- `CustomData` nem kerül feldolgozásra
- Az ideiglenes lemez csatlakoztatása sikertelen
- A felhasználók nem jönnek létre, vagy felhasználói hozzáférési problémák léptek fel
- A hálózatkezelés nincs megfelelően beállítva
- Fájl-vagy particionálási hibák cseréje

Ez a cikk végigvezeti a Cloud-init hibaelhárításának lépésein. Részletesebb részleteket a [Cloud-init Deep Dive](./cloud-init-deep-dive.md)című témakörben talál.

## <a name="step-1-test-the-deployment-without-customdata"></a>1. lépés: a központi telepítés tesztelése a nélkül `customData`

A Cloud-init `customData` a virtuális gép létrehozásakor fogadja el az átadott értéket. Először is győződjön meg arról, hogy ez nem okoz problémát az üzemelő példányokkal kapcsolatban. Próbálja meg kiépíteni a virtuális gépet bármilyen konfiguráció nélkül. Ha úgy találja, hogy a virtuális gép nem tud kiépíteni, folytassa az alábbi lépésekkel, ha úgy találja, hogy az átadott konfiguráció nem lesz alkalmazva, ugorjon a [4. lépésre](). 

## <a name="step-2-review-image-requirements"></a>2. lépés: a rendszerképre vonatkozó követelmények áttekintése
A virtuális gép kiépítési hibájának elsődleges oka az, hogy az operációs rendszer rendszerképe nem felel meg az Azure-ban való futtatás előfeltételeinek. Győződjön meg arról, hogy a lemezképek megfelelően vannak előkészítve, mielőtt üzembe helyezi őket az Azure-ban. 


A következő cikkek bemutatják az Azure-ban támogatott különböző Linux-disztribúciók előkészítésének lépéseit:

- [CentOS-alapú disztribúciók](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES és openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Egyebek: nem támogatott disztribúciók](create-upload-generic.md)

A [támogatott Azure Cloud-init rendszerképek](./using-cloud-init.md)esetében a Linux-disztribúciók már rendelkeznek az összes szükséges csomaggal és konfigurációval, hogy megfelelően kiépítse a lemezképet az Azure-ban. Ha úgy találja, hogy a virtuális gép nem jön létre a saját betanított rendszerképből, próbáljon ki egy olyan támogatott Azure Marketplace-rendszerképet, amely már be van állítva a Cloud-init használatára, a választható módon `customData` . Ha a `customData` megfelelően működik az Azure Marketplace-lemezképpel, akkor valószínűleg probléma van a kurátori képpel.

## <a name="step-3-collect--review-vm-logs"></a>3. lépés: a virtuális gépek naplófájljainak összegyűjtése & áttekintése

Ha a virtuális gép nem tud kiépíteni, az Azure a "létrehozás" állapotot jeleníti meg 20 percen belül, majd újraindítja a virtuális gépet, és még egy 20 percet várnia kell, mielőtt véglegesen megjelöli a virtuális gép telepítését `OSProvisioningTimedOut` .

Amíg a virtuális gép fut, szüksége lesz a virtuális gép naplóira, hogy megtudja, miért nem sikerült a kiépítés.  Ha meg szeretné tudni, miért nem sikerült a virtuális gép üzembe helyezése, ne állítsa le a virtuális gépet. Tartsa meg a virtuális gépet. A naplók gyűjtéséhez a meghibásodott virtuális gép futási állapotban kell maradnia. A naplók gyűjtéséhez használja az alábbi módszerek egyikét:

- [Soros konzol](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- A virtuális gép létrehozása előtt [engedélyezze a rendszerindítási diagnosztikát](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics) , majd [tekintse meg](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics) őket a rendszerindítás során.

- [Futtassa az az VM Repair parancsot](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands) az operációsrendszer-lemez csatlakoztatásához és csatlakoztatásához, amely lehetővé teszi a naplók összegyűjtését:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
A kezdeti hibaelhárítás megkezdéséhez Kezdje a Cloud-init naplókkal, és Ismerje meg, hogy hol történt a hiba, majd használja a többi naplót a mélyebb merüléshez, és adjon meg további elemzéseket. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Soros/rendszerindítási naplók

Az összes naplóban indítsa el a "sikertelen", a "figyelmeztetés", a "WARN", a "err", a "hiba", a "hiba" kifejezést. A konfiguráció beállításával figyelmen kívül hagyhatja a kis-és nagybetűket megkülönböztető kereséseket. 

> [!TIP]
> Ha egyéni rendszerképet használ, érdemes lehet felhasználókat hozzáadni a rendszerképhez. Ha a kiépítés nem tudja beállítani a rendszergazda felhasználót, továbbra is bejelentkezhet az operációs rendszerbe.

## <a name="analyzing-the-logs"></a>A naplók elemzése

Itt talál további információt arról, hogy mit kell keresni az egyes Cloud-init-naplókban.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Alapértelmezés szerint az összes olyan felhő-inicializálási esemény, amelynek prioritása hibakeresés vagy magasabb, a következőre íródik: `/var/log/cloud-init.log` . Ez részletes naplókat biztosít a Cloud-init inicializálásakor előforduló összes eseményről. 

Például:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Ha hibát vagy figyelmeztetést talált, olvassa el a visszafelé a Cloud-init naplóban, hogy megtudja, mi történt a Cloud-init futtatása előtt a hiba vagy a figyelmeztetés megtalálása előtt. Sok esetben a Cloud-init futtatja az operációs rendszer parancsait, vagy a hiba előtt elvégezte a kiépítési műveleteket, így elemzéseket készíthet arról, hogy a hibák miért jelentek meg a naplókban. Az alábbi példa azt mutatja, hogy a Cloud-init megkísérelt egy eszköz csatlakoztatását közvetlenül a hiba előtt.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Ha rendelkezik hozzáféréssel a [soros konzolhoz](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode), próbálja meg újra futtatni a Cloud-init futtatására irányuló parancsot.

A naplózása a `/var/log/cloud-init.log` /etc/cloud/cloud.cfg.d/05_logging. cfg-n belül is újrakonfigurálható. A Cloud-init naplózással kapcsolatos további részletekért tekintse meg a [Cloud-init dokumentációját](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

Az információkat a `stdout` és a `stderr` [Cloud-init szakaszaiban](cloud-init-deep-dive.md)szerezheti be. Ez általában magában foglalja az útválasztási táblázat adatait, a hálózati információkat, az SSH-gazdagép kulcs-ellenőrzési információit, `stdout` valamint a `stderr` Cloud-init minden szakaszát, valamint az egyes szakaszok időbélyegét. Ha szükséges, `stderr` és `stdout` a naplózás újrakonfigurálható a alkalmazásból `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Soros/rendszerindítási naplók 

A Cloud-init több függőséggel rendelkezik, ezek az Azure-beli rendszerképek szükséges előfeltételei, például a hálózatkezelés, a tárolás, az ISO csatlakoztatása és az ideiglenes lemez csatlakoztatása és formázása. Ezek bármelyike hibákat okozhat, és a Cloud-init művelet sikertelen lesz. Ha például a virtuális gép nem tud DHCP-bérletet beszerezni, a Cloud-init művelet sikertelen lesz.

Ha továbbra sem tudja elkülöníteni, hogy miért nem sikerült kiépíteni a Cloud-init eszközt, meg kell ismernie a felhő-init fázisokat és a modulok futtatását. További részletekért lásd: a [Cloud-init mélyebb betekintése](cloud-init-deep-dive.md) .


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>4. lépés: annak vizsgálata, hogy a konfiguráció miért nincs alkalmazva
A Cloud-init nem minden hibája végzetes kiépítési hibát eredményez. Ha például a `runcmd` modult egy Cloud-init konfigurációban használja, akkor a futtatott parancsból nem nulla kilépési kód jelenik meg, mert a virtuális gép üzembe helyezése sikertelen lesz. Ennek az az oka, hogy az alapszintű üzembe helyezési funkció után fut, amely a Cloud-init első 3 fázisában történik. A konfiguráció által nem alkalmazott hibák megoldásához tekintse át a 3. lépésben és a Cloud-init modulok naplóit manuálisan. Például:

- `runcmd` – a parancsfájlok hibák nélkül futnak? Futtassa manuálisan a konfigurációt a terminálról, hogy biztosan a várt módon fussanak.
- Csomagok telepítése – a virtuális gép hozzáférhet a csomagok tárházához?
- Ellenőriznie kell a `customData` virtuális gép számára biztosított adatkonfigurációt is, amely a következő helyen található: `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Következő lépések

Ha továbbra sem tudja elkülöníteni, hogy a Cloud-init miért nem futtatta a konfigurációt, jobban meg kell vizsgálnia, hogy mi történik az egyes felhő-init fázisokban, és mikor futnak a modulok. További információkért lásd: a [Cloud-init-konfiguráció mélyebbre váltása](./cloud-init-deep-dive.md) .