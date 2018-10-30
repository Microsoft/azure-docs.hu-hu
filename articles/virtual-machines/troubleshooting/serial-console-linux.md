---
title: Az Azure virtuális gépek soros konzolja |} A Microsoft Docs
description: Azure-beli virtuális gépek soros konzol és a kétirányú.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/11/2018
ms.author: harijay
ms.openlocfilehash: 57abb01d70929144a8457a04ebc0caf9aecaa61c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212403"
---
# <a name="virtual-machine-serial-console"></a>Virtuális gépek soros konzolja


A virtuális gépek soros konzolja az Azure-ban Linux rendszerű virtuális gépek egy szöveges alapú konzol hozzáférést biztosít. A soros kapcsolat, hogy a virtuális gép, a virtuális gép, amely független a virtuális gépek hálózati vagy operációs rendszer állapotának való hozzáférés biztosítása a COM1 soros port. A soros konzol elérhető virtuális gépet is jelenleg csak az Azure Portalon keresztül történik, és csak azok számára, akik rendelkeznek a virtuális gép közreműködő vagy újabb a virtuális géphez való hozzáférés engedélyezett. 

A soros konzol dokumentációja a Windows virtuális gépek esetében [ide](../windows/serial-console.md).

> [!Note] 
> Globális Azure-régiókban található virtuális gépek soros konzol szolgáltatás általánosan elérhető. Ezen a ponton soros konzol még nem érhető el az Azure Government vagy Azure China-felhőkben.


## <a name="prerequisites"></a>Előfeltételek 

* A resource management üzemi modellhez kell használnia. Klasszikus üzemi modellben nem támogatottak. 
* A virtuális gépnek rendelkeznie kell [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve – lásd az alábbi képernyőképet.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* A soros konzol használata Azure-fiókkal kell rendelkeznie [közreműködői szerepkört](../../role-based-access-control/built-in-roles.md) a virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiókot. 
* A virtuális gép, amelyekhez le a soros konzol acessing is jelszóalapú fiókkal kell rendelkeznie. Létrehozhat egyet a [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) a Virtuálisgép-hozzáférési bővítmény - funkciójának lásd az alábbi képernyőképet.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Linux-disztribúciók jellemző beállításait, lásd: [soros konzolon Linux-disztribúció rendelkezésre állása](#serial-console-linux-distro-availability)



## <a name="get-started-with-serial-console"></a>Soros konzol használatának első lépései
A virtuális gépek soros konzolon keresztül csak érhető el [az Azure portal](https://portal.azure.com). Győződjön meg arról, hogy teljesülnek a [Előfeltételek](#prerequisites) felett. Az alábbiakban a lépéseket a portálon keresztül a virtuális gépek soros konzol eléréséhez:

  1. Nyissa meg az Azure Portalon
  1. (Hagyja ki ezt, ha a virtuális gép rendelkezik a jelszó-hitelesítést használó felhasználó) Kattintson a "Jelszó visszaállítása" panelen felhasználóneves és jelszavas hitelesítéssel rendelkező felhasználó hozzáadása
  1. A bal oldali menüben válassza ki a virtuális gépeket.
  1. Kattintson a listában a virtuális gépen. A virtuális gép áttekintő oldala nyílik meg.
  1. Görgessen le a támogatás és hibaelhárítás szakaszhoz, és kattintson a "Soros konzol" lehetőséget. A soros konzol segítségével egy új panel megnyitja és indítsa el a kapcsolatot.

![](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### 

> [!NOTE] 
> Soros konzol beállított jelszóval rendelkező helyi felhasználó szükséges. Jelenleg csak a nyilvános SSH-kulcsot a konfigurált virtuális gépek nem fogja tudni bejelentkezni a soros konzol. Hozzon létre egy helyi felhasználót felhasználónévvel és jelszóval, használja a [Virtuálisgép-hozzáférési bővítmény](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), a portálon kattintson a "Jelszó visszaállítása" a portálon, és hozzon létre egy helyi felhasználót egy jelszó.
> Előfordulhat, hogy is visszaállítani a rendszergazdai jelszót a fiók által [egyfelhasználós módban is a grub-HIBÁT segítségével](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distro-availability"></a>Soros konzol Linux-disztribúció rendelkezésre állása
Ahhoz, hogy megfelelően működnek a soros konzol a vendég operációs rendszer olvasása és írása az üzenetek konzol a soros port kell állítani. A legtöbb [támogatott az Azure Linux-Disztribúció](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) a soros konzol alapértelmezés szerint konfigurálva van. A soros konzol szakaszban az Azure Portalon egyszerűen kattintson a konzol hozzáférést biztosít. 

Disztribúció      | Soros konzolhoz
:-----------|:---------------------
Red Hat Enterprise Linux    | Red Hat Enterprise Linux-rendszerképeket az Azure-ban elérhető hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. 
CentOS      | CentOS lemezkép érhető el az Azure-ban hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. 
Ubuntu      | Ubuntu-rendszerképek az Azure-ban elérhető hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van.
CoreOS      | CoreOS lemezkép érhető el az Azure-ban hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van.
SUSE        | Újabb SLES rendszerképek az Azure-ban elérhető hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. Ha régebbi verzióit (10 vagy az alábbi) SLES használ az Azure-ban, hajtsa végre a [tudásbáziscikk](https://www.novell.com/support/kb/doc.php?id=3456486) engedélyezése a soros konzol. 
Oracle Linux        | Az Azure-ban elérhető Oracle Linux-rendszerképeket hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van.
Egyéni Linux-rendszerképek     | A Linux rendszerű virtuális gép egyéni rendszerkép soros konzol engedélyezéséhez hozzáféréséhez a konzolhoz a `/etc/inittab` , futtassa a parancsot egy terminálban `ttyS0`. Íme egy példa hozzáadja a a inittab fájlban: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Megfelelően az egyéni lemezképek létrehozásával kapcsolatos további információkért lásd: [létrehozása és feltöltése az Azure-ban Linux rendszerű virtuális merevlemez](https://aka.ms/createuploadvhd). Egy egyéni kernel épít, érdemes lehet engedélyezni az egyes kernel jelzőket-e `CONFIG_SERIAL_8250=y` és `CONFIG_MAGIC_SYSRQ_SERIAL=y`. A konfigurációs fájl gyakran további feltárási /boot/ alatt található.

## <a name="common-scenarios-for-accessing-serial-console"></a>Általános forgatókönyvek a soros konzol eléréséhez 
Forgatókönyv          | A soros konzol műveletek                
:------------------|:-----------------------------------------
Megszakadt az FSTAB fájl | `Enter` billentyűt a folytatáshoz javítsa ki az fstab fájlt egy szövegszerkesztőben. Szükség lehet a egyfelhasználós módban kell. Lásd: [fstab-hibák megoldása](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) és [használatával soros konzol eléréséhez a grub-HIBÁT, és az egyfelhasználós módot](serial-console-grub-single-user-mode.md) a kezdéshez.
Helytelen tűzfal-szabályok | Soros konzol eléréséhez, és engedélyezze az iptables javítása. 
Fájlrendszer sérülése és ellenőrzése | Soros konzol eléréséhez, és a fájlrendszer helyreállítani. 
Az RDP/SSH konfigurációs problémák | Soros konzol eléréséhez, és módosíthatja a beállításait. 
Rendszer hálózati zárolása| Hozzáférés soros konzolon kezelheti a rendszer a portálon keresztül. 
A rendszertöltő használata | Hozzáférés grub-HIBÁT a soros konzolon keresztül. Lépjen a [használatával soros konzol eléréséhez a grub-HIBÁT, és az egyfelhasználós módot](serial-console-grub-single-user-mode.md) a kezdéshez. 

## <a name="disable-serial-console"></a>Tiltsa le a soros konzol
Alapértelmezés szerint minden előfizetés rendelkezik a soros konzol hozzáférés engedélyezve van az összes virtuális gép. Soros konzol vagy az előfizetés szintjén, vagy a virtuális gép szintjén letiltható.

> [!Note] 
> Annak érdekében, hogy engedélyezi vagy letiltja a soros konzol-előfizetéssel, az előfizetés írási engedéllyel kell rendelkeznie. Ez magában foglalja, de a rendszergazda vagy tulajdonos szerepkörök nem korlátozódik. Egyéni szerepkörök is rendelkezhetnek írási jogosultsággal.

### <a name="subscription-level-disable"></a>Előfizetés-szintű letiltása
Soros konzolon keresztül szerint az egész előfizetésre letiltható a [tiltsa le a konzolon REST API-hívás](https://aka.ms/disableserialconsoleapi). Használhatja a "Kipróbálom" elérhető funkciók az API-dokumentáció oldalon letiltása és engedélyezése a soros konzol egy előfizetés. Adja meg a `subscriptionId`, az "alapértelmezett" a `default` mezőben, majd kattintson az OK gombra. Az Azure CLI-parancsok még nem érhető el, és a egy későbbi időpontban fog megérkezni. [Próbálja ki a REST API-hívást Itt](https://aka.ms/disableserialconsoleapi).

![](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Azt is megteheti, előfordulhat, hogy használja az alábbi parancsokat készletét a Cloud Shellben (bash-parancsok látható) letiltása, engedélyezése és egy előfizetést a soros konzol biztonságtiltott állapotának megtekintése. 

* Előfizetés a soros konzol letiltott állapotának lekéréséhez:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Soros konzol egy előfizetés letiltása:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Soros konzol egy előfizetés engedélyezése:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Virtuálisgép-szintű letiltása
Soros konzol az adott virtuális gép rendszerindítási diagnosztikai beállítás letiltásával letiltható adott virtuális gépek. Egyszerűen kapcsolja ki a rendszerindítási diagnosztika az Azure Portalról, és a soros konzol a virtuális gép le lesz tiltva.

## <a name="serial-console-security"></a>Soros konzol biztonsága 

### <a name="access-security"></a>Hozzáférés-biztonságot 
Soros konzoljához való hozzáférés a felhasználók, akik rendelkeznek korlátozódik [virtuális gép közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy a virtuális géphez való hozzáférés felett. Ha az AAD-bérlő többtényezős hitelesítést igényel, akkor a soros konzoljához való hozzáférés is szükség van egy MFA-n keresztül van a hozzáférés [az Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Csatorna biztonsági
Hálózati oda-vissza küldött összes adatot titkosított.

### <a name="audit-logs"></a>Naplók
A soros konzoljához való hozzáférés teljes jelenleg be van jelentkezve a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure-beli virtuálisgép-rendszergazda határozza meg.  

>[!CAUTION] 
A konzol hozzáférés jelszavakat nem jelentkezik, ha a konzolon belül futó parancsok tartalmaznak, vagy a jelszavak, titkos kódok, felhasználói neveket vagy bármely más, személyazonosításra alkalmas adatok (PII) kimeneti, amíg azok lesz írva a virtuális gép rendszerindítási diagnosztikája naplózza, és minden egyéb látható szöveg, a soros konzol scrollback funkció részeként. Ezeket a naplókat. kör alakú, és csak egyéni felhasználók számára a diagnosztikai tárfiók olvasási jogosultsággal rendelkező hozzáférése, de javasoljuk, hogy kövesse az ajánlott eljárás az összes adat az SSH-konzol használatával, amelyek magukban foglalhatják a titkos kódok és/vagy a személyazonosításra alkalmas adatok. 

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és a egy másik felhasználó sikeresen kéri, hogy ugyanahhoz a virtuális géphez való hozzáférés, az első felhasználó le lesz választva, és a második felhasználó csatlakoztatott oly módon, mintha az első felhasználó, hogy mobilplatformonként és a fizikai konzol és a egy új elhagyása a felhasználó úgy.

>[!CAUTION] 
Ez azt jelenti, hogy a felhasználó, aki megszakad nem kijelentkeztetésével! Képes érvényesíteni a kijelentkezési (SIGHUP vagy hasonló mechanizmus) keresztül kapcsolat bontása esetén továbbra is szerepel az ütemterv részét képezi. A Windows-automatikus időtúllépés, SAC engedélyezve van azonban Linux konfigurálhatja a Terminálszolgáltatások időkorlátozási beállítást. Ez egyszerűen adja hozzá `export TMOUT=600` a .bash_profile fájlt vagy a felhasználó .profile bejelentkezési időkorlátja 10 perc után a munkamenet-, a konzolon.

## <a name="accessibility"></a>Kisegítő lehetőségek
Kisegítő lehetőségek egy kulcsfontosságú fókuszának az Azure-soros konzolon. Ebből a célból hogy gondoskodott róla, hogy a soros konzolon érhető el a vizualizációt és Várjuk visszajelzését látási, valamint azokat, akik nem tudják az egérrel rendelkezők.

### <a name="keyboard-navigation"></a>Billentyűzetnavigációt
Használja a `tab` billentyűt a billentyűzeten történő navigációhoz a soros konzol felület az Azure Portalon. A hely kiemelten jelenik meg a képernyőn. A lépéseknek az ismertetése, a soros konzol panelen hagyja, nyomja le a `Ctrl + F6` a billentyűzeten.

### <a name="use-serial-console-with-a-screen-reader"></a>A képernyőolvasó soros konzol használata
Soros konzol tartalmaz beépített képernyőolvasó támogatása. Kapcsolva képernyőolvasóval való navigálni lehetővé teszi a helyettesítő szöveget a jelenleg kijelölt gombra kattintva a képernyőolvasó felolvassa olvasni.

## <a name="errors"></a>Hibák
A legtöbb hiba átmeneti jellegű, és ezek gyakran a soros konzol kapcsolat újrapróbálkozás címek. Az alábbi táblázat azon hibákat és kezelési lehetőségeiket listáját

Hiba                            |   Kezelés 
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait "<VMNAME>". A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van. 
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. | Virtuális gépnek kell lennie a soros konzol eléréséhez a elindított állapotú
Nem rendelkezik a szükséges engedélyekkel a virtuális gép a soros konzol használata. Győződjön meg arról, hogy Virtuálisgép-közreműködői szerepkör engedélyei.| Soros konzol eléréséhez az egyes hozzáférési engedélyt kell. Lásd: [a hozzáférési követelmények](#prerequisites) részletekért
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának "<STORAGEACCOUNTNAME>". Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | Soros konzol eléréséhez az egyes hozzáférési engedélyt kell. Lásd: [a hozzáférési követelmények](#prerequisites) részletekért
Web socket le van zárva, vagy nem nyitható meg. | Szükség lehet az engedélyezési listára `*.console.azure.com`. Egy részletesebb, de hosszabb megközelítés az engedélyezési listára a [a Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/en-us/download/details.aspx?id=41653), amelyek viszonylag sűrűn változnak.
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nincs egy fiók tűzfal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges.

## <a name="known-issues"></a>Ismert problémák 
A soros konzol problémák ismerjük. Ezekről a problémákról és kockázatcsökkentési lépések listáját itt látható.

Probléma                           |   Kezelés 
:---------------------------------|:--------------------------------------------|
Szerezze meg, miután a kapcsolaton transzparens nem jeleníti meg a napló-parancssorban | Tekintse át ezt oldal: [Hitting adja meg a hatástalan](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez akkor fordulhat elő, ha egy egyéni virtuális Gépet, megerősített készülék vagy megfelelően csatlakozni a soros port sikertelen Linux okozó GRUB konfigurációs futtatja.
Soros konzol szöveg mindössze egy részét a képernyő méretétől (gyakran után egy szövegszerkesztő használatával) | Soros konzol nem támogatják a vonatkozó ablakméret egyeztetése ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), ez azt jelenti, nem lesznek nincs SIGWINCH jel küldött frissíteni a képernyőméret és a virtuális gép nincs tudomásuk a terminálban mérete fog rendelkezni. Javasoljuk, hogy instaling xterm vagy valamilyen más hasonló segédprogram, amely biztosítja a "átméretezése" parancsot. Futó átméretezése"elháríthatja ezt a hibát.
Illessze be a nagyon hosszú karakterláncok nem működik | Soros konzol illeszthetők be a terminál 2048 karakter karakterláncok hossza korlátozza. Ez az a soros port sávszélesség túlterhelésének elkerülése érdekében.


## <a name="frequently-asked-questions"></a>Gyakori kérdések 
**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése egy problémát, a https://aka.ms/serialconsolefeedback. Másik lehetőségként (kisebb elsődleges), küldjön visszajelzést azserialhelp@microsoft.com vagy a virtuális gép kategóriáját http://feedback.azure.com

**Q. Támogatja a soros konzol másolja és illessze be?**

A. Igen hajtja végre. Ctrl + Shift + C és a Ctrl + Shift + V használatával másolja és illessze be a terminálba.

**Q. Használható a soros konzol helyett az SSH-kapcsolatot?**

A. Bár technikailag lehetséges tűnhet, soros konzol szolgáló elsősorban a hibaelhárító eszköz helyzetekben, ahol kapcsolat SSH-kapcsolaton keresztül nem alkalmas szolgál. Azt javasoljuk, szemben a soros konzol segítségével egy SSH-helyettesítője két oka:

1. Soros konzol nem rendelkezik a legtöbb sávszélességet SSH -, nem csak szöveg kapcsolat, így több grafikus felhasználói Felülettel műveltekből interakciók soros konzolon nehéz lesz.
1. Soros hozzáférés jelenleg csak a felhasználónevet és jelszót. Az SSH-kulcsok biztonságosabbak sokkal felhasználónév/jelszó kombináció, javasolt a bejelentkezési biztonság szempontjából SSH soros konzolon keresztül.

**Q. Ki engedélyezheti vagy letilthatja a soros konzol az előfizetésemet?**

A. Annak érdekében, hogy engedélyezi vagy letiltja a soros konzol egy előfizetési szintű szintjén, az előfizetés írási engedéllyel kell rendelkeznie. Írási engedéllyel rendelkező szerepek közé tartozik, de nem kizárólagosan, a rendszergazda vagy tulajdonos szerepkörök. Egyéni szerepkörök is rendelkezhetnek írási jogosultsággal.

**Q. Virtuális gép soros konzol férhet hozzá?**

A. Közreműködője szintű hozzáféréssel kell rendelkeznie legalább egy virtuális géphez a virtuális gép soros konzol eléréséhez. 

**Q. A soros konzol nem látható semmi, mi a teendő?**

A. A rendszerkép valószínűleg hibásan konfigurált, soros hozzáféréshez. Lásd: [soros konzolon Linux-disztribúció rendelkezésre állási](#serial-console-linux-distro-availability) engedélyezése a soros konzol lemezképeiben konfigurációval kapcsolatos részletekért.

**Q. Soros konzolon érhető el a Virtual Machine Scale Sets?**

A. A virtuális gép méretezési csoport példányaihoz a soros konzoljához való hozzáférés jelenleg nem támogatott.

**Q. Beállítása a virtuális gépem csak SSH kulcsos hitelesítést használ, akkor is használhatom soros konzoljához való csatlakozáshoz a virtuális gépem?** A. Igen. Soros konzol nem szükséges SSH-kulcsokat, így az összes kell tennie egy felhasználónév/jelszó kombináció. Ez a "Jelszó visszaállítása" panel használatával a portálon, és ezeket a hitelesítő adatok segítségével jelentkezzen be a soros konzol teheti meg.

## <a name="next-steps"></a>További lépések
* Használja a soros konzol [indítsa el a grub-HIBÁT, és adja meg az egyfelhasználós mód](serial-console-grub-single-user-mode.md)
* A soros konzol [NMI és SysRq hívások](serial-console-nmi-sysrq.md)
* Ismerje meg, hogyan használható a soros konzol [GRUB engedélyezése a különböző disztribúciók](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* A soros konzolon érhető el is [Windows](../windows/serial-console.md) virtuális gépek
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md)