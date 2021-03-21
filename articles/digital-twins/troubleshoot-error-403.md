---
title: 'Az Azure Digital Twins-kérelem meghiúsult a következő állapottal: 403 (tiltott)'
description: 'Sikertelen volt a szolgáltatási kérelem oka és megoldása. Állapot: 403 (tiltott) "az Azure Digital Twins-on.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: ad28b3300e9107c7f6d4b1987205583ef60ed658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545700"
---
# <a name="service-request-failed-status-403-forbidden"></a>A szolgáltatási kérelem sikertelen. Állapot: 403 (tiltott)

Ez a cikk az okokat és a megoldási lépéseket ismerteti, amelyekkel 403-es hibát fogadhat az Azure Digital Twins szolgáltatástól érkező kérelmekből. 

## <a name="symptoms"></a>Hibajelenségek

Ez a hiba számos, hitelesítést igénylő szolgáltatási kérelem esetében fordulhat elő. A hatás az, hogy az API-kérelem sikertelen, a hiba állapotát adja vissza `403 (Forbidden)` .

## <a name="causes"></a>Okok

### <a name="cause-1"></a>Ok #1

Ez a hiba általában azt jelzi, hogy az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyei nincsenek megfelelően beállítva a szolgáltatáshoz. Az Azure Digital Twins-példányok számos művelete megköveteli, hogy az *Azure Digital Twins adattulajdonosi* szerepköre legyen a **felügyelni kívánt példányon**. 

### <a name="cause-2"></a>Ok #2

Ha egy ügyfélalkalmazás használatával kommunikál az Azure Digital Twins szolgáltatással, amely az [alkalmazás regisztrálásával](how-to-create-app-registration.md)van hitelesítve, akkor ez a hiba akkor fordulhat elő, ha az alkalmazás regisztrációja nem rendelkezik az Azure Digital Twins szolgáltatáshoz beállított engedélyekkel.

Az alkalmazás regisztrációjának az Azure Digital Twins API-khoz konfigurált hozzáférési engedélyekkel kell rendelkeznie. Ezután, amikor az ügyfélalkalmazás az alkalmazás regisztrálásakor hitelesíti magát, megkapja az alkalmazás-regisztrációhoz megadott engedélyeket.

## <a name="solutions"></a>Megoldások

### <a name="solution-1"></a>Megoldás #1

Az első megoldás annak ellenőrzése, hogy az Azure-felhasználó rendelkezik-e az _**Azure Digital Twins adattulajdonosi**_ szerepkörrel a felügyelni kívánt példányon. Ha nem rendelkezik ezzel a szerepkörrel, állítsa be.

Vegye figyelembe, hogy ez a szerepkör eltér a következőtől:...
* a szerepkör előző neve az előzetes verzióban, az *Azure digitális Twins tulajdonosa (előzetes verzió)* (a szerepkör ugyanaz, de a név megváltozott)
* a *tulajdonosi* szerepkör a teljes Azure-előfizetésben. Az Azure Digital Twins- *adattulajdonos* az Azure digitális ikrek egyik szerepköre, és ezen egyedi Azure digitális Twins-példányra van korlátozva.
* a *tulajdonosi* szerepkör az Azure digitális ikrekben. Ez két különböző Azure digitális Twins-felügyeleti szerepkör, és az *Azure Digital Twins-adattulajdonos* a felügyelethez használt szerepkör.

#### <a name="check-current-setup"></a>Aktuális beállítás keresése

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Hibák elhárítása 

Ha nem rendelkezik ezzel a szerepkör-hozzárendeléssel, az **Azure-előfizetésben** lévő tulajdonosi szerepkörrel rendelkező személynek az alábbi paranccsal kell futtatnia az Azure-beli *digitális Twins-adattulajdonosi* szerepkört az Azure **Digital Twins-példányon**. 

Ha Ön az előfizetés tulajdonosa, saját maga is futtathatja ezt a parancsot. Ha nem, forduljon a tulajdonoshoz, és futtassa ezt a parancsot az Ön nevében.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

További információ erről a szerepkör-követelményről és a hozzárendelési folyamatról: a [ *felhasználó hozzáférési engedélyeinek beállítása* szakasz](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) , *útmutató: példány és hitelesítés beállítása (CLI vagy portál)*.

Ha már rendelkezik ezzel a szerepkör-hozzárendeléssel, *és* Azure ad-alkalmazás-regisztrációt használ egy ügyfélalkalmazás hitelesítéséhez, folytassa a következő megoldással, ha a megoldás nem oldotta meg a 403 problémát.

### <a name="solution-2"></a>Megoldás #2

Ha Azure AD-alkalmazás-regisztrációt használ egy ügyfélalkalmazás hitelesítéséhez, a második lehetséges megoldás annak ellenőrzése, hogy az alkalmazás regisztrációja rendelkezik-e az Azure Digital Twins szolgáltatáshoz konfigurált engedélyekkel. Ha ezek nincsenek konfigurálva, állítsa be őket.

#### <a name="check-current-setup"></a>Aktuális beállítás keresése

Annak ellenőrzéséhez, hogy az engedélyek megfelelően vannak-e konfigurálva, keresse meg az [Azure ad-alkalmazás regisztrációjának áttekintése lapot](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) a Azure Portal. Ezt a lapot saját maga is elérheti, ha a portálon a *Alkalmazásregisztrációk* keres rá.

Váltson a *minden alkalmazás* lapra, és tekintse meg az előfizetésében létrehozott összes regisztrációs alkalmazást.

Ekkor megjelenik az imént létrehozott alkalmazás-regisztráció a listában. Válassza ki, hogy megnyissa a részleteit.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Alkalmazásregisztrációk lap a Azure Portal":::

Először ellenőrizze, hogy az Azure Digital Twins engedélyeinek beállításai megfelelően vannak-e beállítva a regisztrációhoz. Ehhez a menüsávban válassza a *jegyzékfájl* lehetőséget az alkalmazás regisztrációs jegyzékfájljának megtekintéséhez. Görgessen a Code (kód) ablak aljára, és keresse meg a következő mezőket `requiredResourceAccess` . Az értékeknek meg kell egyezniük az alábbi képernyőképen láthatókkal:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Az Azure AD-alkalmazás regisztrálási jegyzékfájljának portál nézete":::

Ezután válassza az *API-engedélyek* lehetőséget a menüsávon annak ellenőrzéséhez, hogy az alkalmazás regisztrálása olvasási/írási engedélyt tartalmaz az Azure Digital Twins szolgáltatáshoz. Ehhez a következőhöz hasonló bejegyzést kell megjelennie:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Az Azure AD-alkalmazás regisztrálásához szükséges API-engedélyek portál nézete, amely az Azure Digital Twins olvasási/írási hozzáférését mutatja":::

#### <a name="fix-issues"></a>Hibák elhárítása

Ha bármelyike másképp jelenik meg, mint a leírtak, kövesse az alkalmazás regisztrációjának beállítása című témakör útmutatását [*: alkalmazás regisztrációjának létrehozása*](how-to-create-app-registration.md).

## <a name="next-steps"></a>Következő lépések

Olvassa el a telepítési lépéseket az új Azure Digital Twins-példány létrehozásához és hitelesítéséhez:
* [*Útmutató: példány és hitelesítés beállítása (CLI)*](how-to-set-up-instance-cli.md)

További információ az Azure Digital Twins biztonságáról és engedélyeiről:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)
