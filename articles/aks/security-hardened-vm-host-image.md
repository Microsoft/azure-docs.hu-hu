---
title: Biztonság megerősítése az AK-beli virtuális gépek gazdagépei számára
description: Tudnivalók a biztonság megerősítéséről az AK VM-gazdagép operációs rendszerében
services: container-service
author: mlearned
ms.topic: article
ms.date: 03/29/2021
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b0866905d0228d2304ebf5c8ef930a629979d2da
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012078"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Biztonság megerősítése az AK-ügynök Node gazda operációs rendszeréhez

Biztonságos szolgáltatásként az Azure Kubernetes Service (ak) megfelel a SOC, az ISO, a PCI DSS és a HIPAA szabványoknak. Ez a cikk az AK-beli virtuális gépek (VM) gazdagépekre alkalmazott biztonsági megerősítést ismerteti. További információ az AK biztonságáról: [alkalmazások és fürtök biztonsági fogalmai az Azure Kubernetes szolgáltatásban (ak)](./concepts-security.md).

> [!Note]
> Ez a dokumentum csak az AK-ban található Linux-ügynökökre terjed ki.

Az AK-fürtök üzembe helyezése a gazdagépen futó virtuális gépeken történik, ami egy olyan biztonsági optimalizált operációs rendszert futtat, amely az AK-on futó tárolók számára A gazda operációs rendszer egy **Ubuntu 16.04. LTS** rendszerképet használ, amely nagyobb [biztonságot](#security-hardening-features) és optimalizálást alkalmaz.

A biztonsági megerősített gazda operációs rendszer célja, hogy csökkentse a támadás felszíni területét, és optimalizálja a tárolók biztonságos üzembe helyezését.

> [!Important]
> A biztonsági megerősített operációs rendszer **nem** a CIS-re vonatkozó teljesítményteszt. Noha átfedésben van a CIS-referenciaértékekkel, a cél nem a CIS-kompatibilis. A gazdagép operációs rendszerének megtartására irányuló cél az, hogy a Microsoft saját belső biztonsági előírásaival összhangban álló biztonsági szintet biztosítson.

## <a name="security-hardening-features"></a>Biztonsági megerősítő funkciók

* Az AK-ban alapértelmezés szerint a biztonságra optimalizált gazda operációs rendszer van megadva, de nem választhat másik operációs rendszert.

* Az Azure napi javításokat alkalmaz (beleértve a biztonsági javításokat is) az AK-beli virtuális gépek gazdagépei számára. 
    * Ezen javítások némelyike újraindítást igényel, míg mások nem. 
    * Az AK-beli VM-gazdagépek újraindítására szükség van. 
    * Az AK-javítások automatizálásával kapcsolatos útmutatásért lásd: [AK-csomópontok javítása](./node-updates-kured.md).

## <a name="what-is-configured"></a>Mi van konfigurálva

| VIR  | Naplózás leírása|
|---|---|
| 1.1.1.1 |A CramFS-fájlrendszerek csatlakoztatásának biztosítása le van tiltva|
| 1.1.1.2 |A freevxfs-fájlrendszerek csatlakoztatásának biztosítása le van tiltva|
| 1.1.1.3 |A JFFS2-fájlrendszerek csatlakoztatásának biztosítása le van tiltva|
| 1.1.1.4 |Győződjön meg arról, hogy a HFS-fájlrendszerek csatlakoztatása le van tiltva|
| 1.1.1.5 |Győződjön meg arról, hogy a HFS és a fájlrendszerek csatlakoztatása le van tiltva|
|1.4.3 |Az egyfelhasználós üzemmódhoz szükséges hitelesítés biztosítása |
|1.7.1.2 |Győződjön meg arról, hogy a helyi bejelentkezési figyelmeztetés szalagcíme megfelelően van konfigurálva |
|1.7.1.3 |Győződjön meg arról, hogy a távoli bejelentkezés figyelmeztetési szalagcíme megfelelően van konfigurálva |
|1.7.1.5 |Győződjön meg arról, hogy a/etc/Issue engedélyei konfigurálva vannak |
|1.7.1.6 |Győződjön meg arról, hogy a/etc/Issue.net engedélyei konfigurálva vannak |
|2.1.5 |Győződjön meg arról, hogy a--streaming-kapcsolat – tétlen – időtúllépés értéke nem 0 |
|3.1.2 |A csomagok átirányítási küldésének ellenőrzése letiltva |
|3.2.1 |A forrásként átirányított csomagok elfogadásának biztosítása |
|3.2.2 |Az ICMP-átirányítások nem fogadhatók el |
|3.2.3 |Ügyeljen arra, hogy a biztonságos ICMP-átirányítások ne legyenek elfogadva |
|3.2.4 |Gyanús csomagok naplózásának biztosítása |
|3.3.1 |Az IPv6-útválasztó hirdetményei nem fogadhatók el |
|3.5.1 |Győződjön meg arról, hogy a DCCP le van tiltva |
|3.5.2 |Győződjön meg arról, hogy a SCTP le van tiltva |
|3.5.3 |Az RDS letiltása |
|3.5.4 |Győződjön meg arról, hogy a TIPC le van tiltva |
|4.2.1.2 |Győződjön meg arról, hogy a naplózás konfigurálva van |
|5.1.2 |Győződjön meg arról, hogy a/etc/crontab engedélyei konfigurálva vannak |
|5.2.4 |Győződjön meg róla, hogy az SSH X11 továbbítása le van tiltva |
|5.2.5 |Győződjön meg arról, hogy az SSH-MaxAuthTries 4 vagy kevesebb értékre van állítva |
|5.2.8 |Győződjön meg arról, hogy az SSH gyökérszintű bejelentkezés le van tiltva |
|5.2.10 |Győződjön meg arról, hogy az SSH-PermitUserEnvironment le van tiltva |
|5.2.11 |Győződjön meg arról, hogy csak jóváhagyott maximális algoritmusok vannak használatban |
|5.2.12 |Győződjön meg arról, hogy az SSH tétlen időtúllépési időköz konfigurálva van |
|5.2.13 |Győződjön meg arról, hogy az SSH-LoginGraceTime egy vagy kevesebb percre van beállítva |
|5.2.15 |Győződjön meg arról, hogy az SSH-figyelmeztetési szalagcím konfigurálva van |
|5.3.1 |Győződjön meg arról, hogy a jelszó-létrehozási követelmények konfigurálva vannak |
|5.4.1.1 |Győződjön meg arról, hogy a jelszó lejárati ideje 90 nap vagy kevesebb |
|5.4.1.4 |Inaktív jelszó zárolásának biztosítása 30 nap vagy kevesebb |
|5.4.4 |Győződjön meg arról, hogy az alapértelmezett felhasználói umask 027 vagy szigorúbb |
|5,6 |Győződjön meg arról, hogy a su parancshoz való hozzáférés korlátozott|

## <a name="additional-notes"></a>További megjegyzések
 
* A támadási felület további csökkentéséhez le kell tiltani néhány felesleges kernel-modul illesztőprogramját az operációs rendszerben.

* A biztonsági megerősített operációs rendszer kifejezetten AK-hoz készült, és **nem** támogatott az AK platformon kívül.

## <a name="next-steps"></a>Következő lépések  

További információt az AK biztonságáról a következő cikkekben talál: 

* [Azure Kubernetes Service (AKS)](./intro-kubernetes.md)
* [AK biztonsági megfontolások](./concepts-security.md)
* [AK ajánlott eljárások](./best-practices.md)
