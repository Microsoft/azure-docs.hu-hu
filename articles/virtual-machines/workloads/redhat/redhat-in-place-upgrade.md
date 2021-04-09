---
title: Red Hat Enterprise Linux lemezképek helyben történő frissítése az Azure-ban
description: Ismerje meg, hogyan végezheti el a Red Hat Enterprise 7. x lemezképek helyben történő frissítését a legújabb 8. x verzióra.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676075"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Helyi frissítések Red Hat Enterprise Linux

Ez a cikk útmutatást nyújt arról, hogyan végezheti el a helyben történő frissítését Red Hat Enterprise Linux (RHEL) 7-es verzióról Red Hat Enterprise Linux 8-ra. Az útmutató az eszközt használja az `leapp` Azure-ban. A helyben történő frissítés során a meglévő RHEL 7 operációs rendszert a RHEL 8 verziója váltja fel.

>[!Note] 
> A SQL Server on Red Hat Enterprise Linux kínálata nem támogatja az Azure-ban végzett helyi frissítést.

## <a name="what-to-expect-during-the-upgrade"></a>Mire számíthat a frissítés során
A frissítés során a rendszer néhányszor újraindul. A végső újraindítás a virtuális gépet a RHEL 8 legújabb másodlagos kiadására frissíti. 

A frissítési folyamat 20 perctől akár 2 óráig is eltarthat. A teljes idő számos tényezőtől függ, például a virtuális gép méretétől és a rendszerre telepített csomagok számától.

## <a name="preparations"></a>Előkészítések
A Red Hat és az Azure ajánlott helyi frissítéssel áttérni a rendszerre a következő főverzióra. 

A frissítés megkezdése előtt vegye figyelembe a következő szempontokat. 

>[!Important] 
> A frissítés megkezdése előtt készítsen pillanatképet a rendszerképről.

* Győződjön meg arról, hogy a legújabb RHEL 7 verziót használja. Jelenleg a legújabb verzió a RHEL 7,9. Ha zárolt verziót használ, és nem tud frissíteni a RHEL 7,9-re, akkor kövesse az [alábbi lépéseket a nem EUs (bővített frissítés támogatása) adattárra való váltáshoz](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Futtassa a következő parancsot a frissítés vizsgálatához, és ellenőrizze, hogy a rendszer sikeresen befejeződik-e. A parancsnak */var/log/leapp/leapp-report.txt* fájlt kell előállítania. Ez a fájl ismerteti a folyamatot, mi történik, és hogy lehetséges-e a frissítés.

    >[!NOTE]
    > A jelen cikkben szereplő parancsok futtatásához használja a root fiókot. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Győződjön meg arról, hogy a soros konzol működik. A frissítési folyamat során ezt a konzolt fogja használni a figyeléshez.

* SSH-gyökér hozzáférésének engedélyezése a */etc/ssh/-sshd_configban*:
    1. Nyissa meg a */etc/ssh/sshd_config* fájlt.
    1. Keressen a `#PermitRootLogin yes` kifejezésre.
    1. Távolítsa el a `#` sztringet () a karakterlánc megjegyzésének törléséhez.

## <a name="upgrade-steps"></a>Frissítési lépések

Kövesse ezeket a lépéseket körültekintően. Javasoljuk, hogy az éles példányok kipróbálása előtt próbálja meg a frissítést a tesztelési gépen.

1. `yum`A legújabb ügyfél-csomagok beolvasásához végezze el a frissítést.
    ```bash
    yum update -y
    ```

1. Telepítse a programot `leapp-client-package` .
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. A [Red Hat portálon](https://access.redhat.com/articles/3664871)szerezze be a *repomap.csv* és *pes-events.jst* tartalmazó *leapp-Data. tar. gz* fájlt. Bontsa ki a *leapp-Data. tar. gz* fájlt.
    1. Töltse le a *leapp-Data. tar. gz* fájlt.
    1. Bontsa ki a tartalmat, és távolítsa el a fájlt. Használja az alábbi parancsot:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Adjon hozzá egy `answers` fájlt a következőhöz: `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Indítsa el a frissítést.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  A `leapp upgrade` parancs sikeres befejeződése után manuálisan indítsa újra a rendszert a folyamat befejezéséhez. A rendszer nem érhető el, mert újraindul néhányszor. Figyelje meg a folyamatot a soros konzol használatával.

1.  Ellenőrizze, hogy a frissítés sikeresen befejeződött-e.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. A frissítés befejeződése után távolítsa el a root SSH-hozzáférést:
    1. Nyissa meg a */etc/ssh/sshd_config* fájlt.
    1. Keressen a `#PermitRootLogin yes` kifejezésre.
    1. Írjon be egy számot ( `#` ) a karakterlánc megjegyzéséhez.

1. Indítsa újra az SSHD szolgáltatást a módosítások alkalmazásához.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Gyakori problémák

A következő hibák gyakran történnek, ha a `leapp preupgrade` folyamat meghiúsul, vagy a `leapp upgrade` folyamat meghiúsul:

* **Hiba**: a következő letiltott beépülő modulok esetében nem találhatók egyezések.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Megoldás**: tiltsa le az előfizetés-kezelő beépülő modult. Tiltsa le a fájlt a */etc/yum/pluginconf.d/Subscription-Manager.conf* szerkesztésével, és váltson a következőre: `enabled` `enabled=0` .

    Ez a hiba akkor fordul elő, ha az engedélyezett előfizetés-kezelő `yum` beépülő modul nem használatos `PAYG` virtuális gépekhez.

* **Hiba**: lehetséges problémák a távoli bejelentkezéssel a root használatával.

    Ez a hiba akkor fordulhat elő, ha sikertelen a következő `leapp preupgrade` :

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Megoldás**: engedélyezze a legfelső szintű hozzáférést a */etc/sshd_config*.

    Ez a hiba akkor fordul elő, ha a root SSH-hozzáférés nincs engedélyezve a */etc/sshd_configban*. További információt a jelen cikk [előkészületek](#preparations) című szakaszában talál. 


## <a name="next-steps"></a>Következő lépések
* További információ [Az Azure-beli Red Hat-lemezképekről](./redhat-images.md).
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* További információ a [RHEL BYOS ajánlatáról](./byos.md).
* A Red Hat helyszíni frissítési folyamataival kapcsolatos további információkért lásd: a Red Hat dokumentációjában a [RHEL 7 és a RHEL 8 közötti frissítés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) .
* Ha többet szeretne megtudni a Red Hat támogatási szabályzatáról a RHEL összes verziójára vonatkozóan, tekintse meg az [Red Hat Enterprise Linux életciklusát](https://access.redhat.com/support/policy/updates/errata) a Red Hat dokumentációjában.