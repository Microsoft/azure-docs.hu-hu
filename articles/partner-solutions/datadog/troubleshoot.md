---
title: Hibaelhárítás a Datadoggal-hez – Azure partneri megoldások
description: Ez a cikk az Azure-beli Datadoggal hibaelhárításával kapcsolatos információkat tartalmaz.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a8bb28892fe42215876b5cc8771ae73c7d2aab7f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746016"
---
# <a name="troubleshooting-datadog-on-azure"></a>Az Azure-beli Datadoggal hibaelhárítása

Ez a dokumentum a Datadoggal-t használó megoldások hibaelhárításával kapcsolatos információkat tartalmaz.

## <a name="unable-to-create-datadog-resource"></a>Nem sikerült létrehozni a Datadoggal-erőforrást

Az Azure Datadoggal-integráció beállításához **tulajdonosi** hozzáféréssel kell rendelkeznie az Azure-előfizetéshez. A telepítés megkezdése előtt győződjön meg arról, hogy rendelkezik a megfelelő hozzáféréssel.

## <a name="single-sign-on-errors"></a>Egyszeri bejelentkezési hibák

**Nem lehet menteni az egyszeri bejelentkezési beállításokat** – ez a hiba akkor fordul elő, ha egy másik, a datadoggal SAML-azonosítót használó vállalati alkalmazás van. Ha szeretné megkeresni, hogy melyik alkalmazást használja, válassza a **Szerkesztés** lehetőséget az alapszintű SAML-konfiguráció szakaszban.

A probléma megoldásához tiltsa le a másik alkalmazást, vagy használja a másik alkalmazást vállalati alkalmazásként az SAML egyszeri bejelentkezés beállításához a Datadoggal. Ha úgy dönt, hogy a másik alkalmazást használja, győződjön meg arról, hogy az alkalmazás rendelkezik a [szükséges beállításokkal](create.md#configure-single-sign-on).

Az alkalmazás **nem jelenik meg az egyszeri bejelentkezés beállítása lapon** – először keresse meg az alkalmazás azonosítóját. Ha nem jelenik meg eredmény, ellenőrizze az alkalmazás SAML-beállításait. A rács csak a megfelelő SAML-beállításokkal rendelkező alkalmazásokat jeleníti meg. 

Az azonosító URL-címnek a értéknek kell lennie `https://us3.datadoghq.com/account/saml/metadata.xml` .

A válasz URL-címének kell lennie `https://us3.datadoghq.com/account/saml/assertion` .

A következő képen a megfelelő értékek láthatók.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="A Datadoggal alkalmazás SAML-beállításainak vizsgálatához a HRE-ben." border="true":::

**A bérlőnek meghívott vendég felhasználók nem férhetnek hozzá az egyszeri bejelentkezéshez** – egyes felhasználók két e-mail-címmel rendelkeznek Azure Portal. Általában az egyik e-mail-cím az egyszerű felhasználónév (UPN), a másik e-mail pedig egy másik e-mail-cím.

Vendég felhasználó meghívásakor használja a Kezdőlap bérlői egyszerű felhasználónevet. Az egyszerű felhasználónév használatával az egyszeri bejelentkezés során megtarthatja az e-mail-cím szinkronizálását. Az egyszerű felhasználónevet a felhasználó Azure Portal jobb felső sarkában található e-mail-cím megkeresésével érheti el.
  
## <a name="logs-not-being-emitted"></a>Nem kibocsátott naplók

Csak a Azure Monitor erőforrás-naplózási kategóriákban felsorolt erőforrások bocsátanak ki naplókat a Datadoggal. Annak ellenőrzéséhez, hogy az erőforrás Datadoggal-e a naplókat, navigáljon az Azure diagnosztikai beállításához az adott erőforráshoz. Ellenőrizze, hogy van-e Datadoggal diagnosztikai beállítás.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Datadoggal diagnosztikai beállítás az Azure-erőforráson" border="true":::

## <a name="metrics-not-being-emitted"></a>Nem kibocsátott metrikák

A Datadoggal erőforráshoz a megfelelő Azure-előfizetéshez tartozó **figyelési olvasó** szerepkör van rendelve. Ez a szerepkör lehetővé teszi a Datadoggal-erőforrás számára a metrikák gyűjtését és a metrikák küldését a Datadoggal.

Az erőforrás megfelelő szerepkör-hozzárendelésének ellenőrzéséhez nyissa meg a Azure Portal, és válassza ki az előfizetést. A bal oldali ablaktáblán válassza a **Access Control (iam)** lehetőséget. Keresse meg a Datadoggal-erőforrás nevét. Győződjön meg arról, hogy a Datadoggal-erőforrás rendelkezik a **figyelési olvasó** szerepkör-hozzárendeléssel, az alább látható módon.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Datadoggal szerepkör-hozzárendelés az Azure-előfizetésben" border="true":::

## <a name="datadog-agent-installation-fails"></a>A datadoggal-ügynök telepítése sikertelen

Az Azure Datadoggal integrációja lehetővé teszi, hogy a Datadoggal-ügynököt virtuális gépre vagy app Service-re telepítse. A Datadoggal-ügynök konfigurálásához a rendszer az API-kulcsok képernyő **alapértelmezett kulcsaként** kiválasztott API-kulcsát használja. Ha nincs kiválasztva alapértelmezett kulcs, a Datadoggal-ügynök telepítése sikertelen lesz.

Ha a Datadoggal-ügynök helytelen kulccsal lett konfigurálva, lépjen az API-kulcsok képernyőre, és módosítsa az **alapértelmezett kulcsot**. A virtuális gép új API-kulcsokkal való konfigurálásához el kell távolítania a Datadoggal-ügynököt, és újra kell telepítenie.

## <a name="next-steps"></a>Következő lépések

További információ a Datadoggal [példányának kezeléséről](manage.md) .
