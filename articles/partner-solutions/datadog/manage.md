---
title: Datadoggal-erőforrás kezelése – Azure partneri megoldások
description: Ez a cikk a Azure Portal Datadoggal-erőforrásának kezelését ismerteti. Egyszeri bejelentkezés beállítása, egy Fluent szervezet törlése és támogatás kérése.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046351"
---
# <a name="manage-the-datadog-resource"></a>A Datadoggal-erőforrás kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-integráció beállításait a Datadoggal használatával.

## <a name="resource-overview"></a>Erőforrás-áttekintés

A Datadoggal-erőforrás részleteinek megtekintéséhez válassza az **Áttekintés** lehetőséget a bal oldali ablaktáblán.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Datadoggal-erőforrás áttekintése" border="true" lightbox="media/manage/resource-overview.png":::

A részletek a következők:

- Erőforráscsoport neve
- Hely/régió
- Előfizetés
- Címkék
- Egyszeri bejelentkezési hivatkozás a Datadoggal-szervezetre
- Datadoggal ajánlat/csomag
- Számlázási időszak

Emellett az irányítópultokra, naplókra és Datadoggal mutató hivatkozásokat is tartalmaz.

Az áttekintő képernyő összefoglalja a naplókat és mérőszámokat küldő erőforrások Datadoggal.

- Erőforrástípus – Azure-Erőforrás típusa.
- Összes erőforrás – az erőforrástípus összes erőforrásának száma.
- Naplók küldésére szolgáló erőforrások – az integráción keresztül Datadoggal naplókat küldő erőforrások száma.
- Mérőszámokat küldő erőforrások – az integráción keresztül Datadoggal mérőszámokat küldő erőforrások száma.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Metrikák és naplók szabályainak újrakonfigurálása

A metrikák és naplók konfigurációs szabályainak módosításához válassza a **metrikák és naplók** elemet a bal oldali ablaktáblán.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="A naplók és a metrikák konfigurációjának módosítása a Datadoggal-erőforráshoz." border="true":::

További információ: [mérőszámok és naplók konfigurálása](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>Figyelt erőforrások megtekintése

A Datadoggal naplókat kibocsátó erőforrások listájának megtekintéséhez válassza ki a **figyelt erőforrások** elemet a bal oldali ablaktáblán.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="A Datadoggal által figyelt erőforrások megtekintése" border="true":::

Az erőforrások listáját az erőforrástípus, az erőforráscsoport neve, a hely alapján szűrheti, valamint azt is, hogy az erőforrás naplófájlokat és metrikákat küld-e.

A **datadoggal** lévő oszlopok azt jelzik, hogy az erőforrás naplókat küld a datadoggal. Ha az erőforrás nem küld naplókat, ez a mező jelzi, hogy a naplók miért nem lesznek elküldve a Datadoggal. Ennek okai a következőket jelenthetik:

- Az erőforrás nem támogatja a naplók küldését. A naplók Datadoggal való elküldéséhez csak a figyelési naplók kategóriáit tartalmazó erőforrások állíthatók be.
- Elérte az öt diagnosztikai beállítás korlátját. Minden Azure-erőforráshoz legfeljebb öt diagnosztikai beállítás tartozhat. További információ: [diagnosztikai beállítások](../../azure-monitor/essentials/diagnostic-settings.md).
- HIba. Az erőforrás úgy van konfigurálva, hogy naplókat küldjön a Datadoggal, de egy hiba miatt blokkolva van.
- Nincsenek konfigurálva a naplók. Csak a megfelelő erőforrás-címkékkel rendelkező Azure-erőforrások vannak konfigurálva a naplók Datadoggal való elküldésére.
- A régió nem támogatott. Az Azure-erőforrás olyan régióban található, amely jelenleg nem támogatja a naplók küldését a Datadoggal.
- Nincs konfigurálva a datadoggal-ügynök. A Datadoggal-ügynököt nem futtató virtuális gépek nem bocsátanak ki naplókat a Datadoggal.

## <a name="api-keys"></a>API-kulcsok

A Datadoggal-erőforráshoz tartozó API-kulcsok listájának megtekintéséhez válassza ki a **kulcsokat** a bal oldali ablaktáblán. Megjelennek a kulcsokkal kapcsolatos információk.

:::image type="content" source="media/manage/keys.png" alt-text="A Datadoggal-szervezet API-kulcsai." border="true":::

A Azure Portal az API-kulcsok csak olvasható nézetét jeleníti meg. A kulcsok kezeléséhez válassza a Datadoggal-portál hivatkozást. A Datadoggal-portálon végzett módosítások után frissítse a Azure Portal nézetet.

Az Azure Datadoggal integrációja lehetővé teszi, hogy a Datadoggal-ügynököt virtuális gépre vagy app Service-re telepítse. Ha nincs kiválasztva alapértelmezett kulcs, a Datadoggal-ügynök telepítése sikertelen lesz.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Virtuális gépek monitorozása a Datadog-ügynök használatával

A Datadoggal-ügynököket bővítményként is telepítheti a virtuális gépekre. A bal oldali ablaktábla Datadoggal szervezeti beállításai területén nyissa meg a **virtuálisgép-ügynököt** . Ez a képernyő az előfizetésben lévő összes virtuális gép listáját jeleníti meg.

Minden virtuális gép esetében a következő adatértékek jelennek meg:

- Erőforrás neve – virtuális gép neve
- Erőforrás állapota – azt határozza meg, hogy a virtuális gép leáll-e vagy fut-e. A Datadoggal-ügynök csak a rendszert futtató virtuális gépekre telepíthető. Ha a virtuális gép le van állítva, a Datadoggal-ügynök telepítése le lesz tiltva.
- Ügynök verziója – a Datadoggal-ügynök verziószáma.
- Ügynök állapota – azt határozza meg, hogy a Datadoggal-ügynök fut-e a virtuális gépen.
- Integráció engedélyezve – a Datadoggal-ügynök által gyűjtött fő mérőszámok.
- Telepítési módszer – a Datadoggal-ügynök telepítéséhez használt konkrét eszköz. Például Chef vagy script.
- Naplók küldése – azt határozza meg, hogy a Datadoggal-ügynök naplókat küld-e a Datadoggal.

Válassza ki azt a virtuális gépet, amelyre telepíteni szeretné a Datadoggal-ügynököt. Válassza az **ügynök telepítése** lehetőséget.

A portál megerősítést kér, hogy az ügynököt az alapértelmezett kulccsal szeretné telepíteni. A telepítés megkezdéséhez kattintson **az OK gombra** . Az Azure az ügynök telepítése és üzembe **helyezése** után megjeleníti a telepítés állapotát.

A Datadoggal-ügynök telepítése után a rendszer telepíti az állapotot.

Ha látni szeretné, hogy a Datadoggal-ügynök telepítve van, válassza ki a virtuális gépet, és keresse meg a bővítmények ablakot.

A Datadoggal-ügynököket a virtuális gép **ügynökének** használatával távolíthatja el a virtuális gépen. Válassza ki a virtuális gépet, és **távolítsa el az ügynököt**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>App Services figyelése a Datadoggal-ügynök bővítményként való használatával

A Datadoggal-ügynököket bővítményként is telepítheti az App Servicesben. Nyissa meg **app Service bővítményt** a bal oldali ablaktáblán. Ez a képernyő az előfizetésben lévő összes app Services listáját jeleníti meg.

Az egyes app Service-szolgáltatásokhoz a következő adatelemek jelennek meg:

- Erőforrás neve – virtuális gép neve.
- Erőforrás állapota – azt határozza meg, hogy az App Service leáll vagy fut. A Datadoggal-ügynök csak a rendszert futtató app Services szolgáltatásban telepíthető. Ha az App Service leáll, a Datadoggal-ügynök telepítése le van tiltva.
- App Service-csomag – az App Service-hez konfigurált konkrét terv.
- Ügynök verziója – a Datadoggal-ügynök verziószáma.

A Datadoggal-ügynök telepítéséhez jelölje ki az App Service-t és a **telepítési bővítményt**. A legújabb Datadoggal-ügynök bővítményként van telepítve az App Service-ben.

A portál megerősíti, hogy telepíteni kívánja a Datadoggal-ügynököt. Emellett az adott app Service-alkalmazás beállításai is frissülnek az alapértelmezett kulccsal. Az App Service a Datadoggal-ügynök telepítésének befejeződése után újraindul.

A Datadoggal-ügynök telepítési folyamatának megkezdéséhez kattintson **az OK gombra** . A portál az ügynök telepítése előtt megjeleníti a **telepítés** állapotát. A Datadoggal-ügynök telepítése után a rendszer telepíti az állapotot.

A Datadoggal-ügynökök az App Service-ben való eltávolításához nyissa meg a **app Service bővítményt**. Az App Service és az **eltávolítási bővítmény** kiválasztása

## <a name="reconfigure-single-sign-on"></a>Egyszeri bejelentkezés újrakonfigurálása

Ha újra szeretné konfigurálni az egyszeri bejelentkezést, válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali ablaktáblán.

Ha az Azure Active Directoryn keresztül szeretne egyszeri bejelentkezést létrehozni, jelölje be az **egyszeri bejelentkezés engedélyezése Azure Active Directoryon keresztül** lehetőséget.

A portál lekéri a megfelelő Datadoggal alkalmazást a Azure Active Directoryról. Az alkalmazás az integráció beállításakor kiválasztott vállalati alkalmazás nevéből származik. Válassza ki a Datadoggal-alkalmazás nevét az alább látható módon:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Konfigurálja újra az egyszeri bejelentkezési alkalmazást." border="true":::
 
## <a name="disable-or-enable-integration"></a>Integráció letiltása vagy engedélyezése

Leállíthatja a naplók és metrikák küldését az Azure-ból a Datadoggal. Továbbra is díjat számítunk fel más olyan Datadoggal-szolgáltatásokra, amelyek nem kapcsolódnak a monitorozási mérőszámokhoz és naplókhoz.

Ha le szeretné tiltani az Azure-integrációt a Datadoggal-mel, ugorjon az **Áttekintés** elemre. Válassza a **Letiltás** és **az OK gombot**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Datadoggal erőforrás letiltása." border="true":::

Az Azure-integráció Datadoggal való engedélyezéséhez lépjen az **Áttekintés** elemre. Válassza az **Engedélyezés** és **az OK gombot**. Az **Engedélyezés** lehetőség kiválasztásával lekéri a metrikák és naplók korábbi konfigurációját. A konfiguráció határozza meg, hogy mely Azure-erőforrások bocsátanak ki metrikákat és naplókat a Datadoggal. A lépés elvégzése után a rendszer metrikákat és naplókat továbbít a Datadoggal.
 
:::image type="content" source="media/manage/enable.png" alt-text="Datadoggal erőforrás engedélyezése." border="true":::

## <a name="delete-datadog-resource"></a>Datadoggal-erőforrás törlése

Nyissa meg a bal oldali ablaktábla **Áttekintés** elemét, és válassza a **Törlés** lehetőséget. Erősítse meg, hogy törölni kívánja a Datadoggal-erőforrást. Válassza a **Törlés** elemet.

:::image type="content" source="media/manage/delete.png" alt-text="Datadoggal-erőforrás törlése" border="true":::

Ha csak egy Datadoggal-erőforrás van leképezve egy Datadoggal-szervezetre, a naplók és a metrikák már nem lesznek elküldve a Datadoggal. A Datadoggal az Azure Marketplace-en keresztül minden számlázás leáll.

Ha egynél több Datadoggal-erőforrás van leképezve a Datadoggal-szervezet számára, a Datadoggal-erőforrás törlése csak a naplók és a metrikák küldését állítja le a Datadoggal-erőforrás számára. Mivel a Datadoggal-szervezet más Azure-erőforrásokhoz van társítva, a számlázás az Azure Marketplace-en keresztül folytatódik.

## <a name="getting-support"></a>Támogatás igénylése

Ha kapcsolatba szeretne lépni az Azure Datadoggal-integrációval kapcsolatos támogatással, válassza a bal oldali ablaktábla **új support Request** elemét. Válassza ki a Datadoggal-portálra mutató hivatkozást.

:::image type="content" source="media/manage/support-request.png" alt-text="Új Datadoggal-támogatási kérelem létrehozása" border="true":::

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos segítségért lásd: [datadoggal-megoldások hibaelhárítása](troubleshoot.md).