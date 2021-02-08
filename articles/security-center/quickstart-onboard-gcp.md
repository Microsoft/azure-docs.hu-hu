---
title: A GCP-fiók összekötése Azure Security Center
description: A GCP-erőforrások figyelése Azure Security Center
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8ee7b37861be299dd36a596ae1cd4899b0ebffab
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809405"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>A GCP-fiókok összekapcsolásának Azure Security Center

A Felhőbeli számítási feladatok gyakran több felhőalapú platformra is kiterjednek, a Cloud Security servicesnek ugyanezt kell tennie.

Azure Security Center védi a munkaterheléseket az Azure-ban, a Amazon Web Services (AWS) és a Google Cloud Platformban (GCP).

A GCP-fiókok Security Centerba való bevezetésével integrálható a GCP biztonsági parancs és a Azure Security Center. Security Center így biztosítja a következő felhőalapú környezetek láthatóságát és védelmét:

- Biztonsági konfigurációs beállítások észlelése
- Egyetlen nézet, amely Security Center ajánlásokat és a GCP Security Command Center eredményeit mutatja
- A GCP-erőforrások beépítése a Security Center biztonságos pontszámának számításaiba
- A GCP Security Command Center ajánlásainak integrációja a CIS standard alapján a Security Center szabályozási megfelelőségi irányítópultján

Az alábbi képernyőképen megtekintheti az Security Center áttekintési irányítópultján megjelenő GCP-projekteket.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 GCP-projekt szerepel a Security Center áttekintési irányítópultján" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender for Servers](defender-for-servers-introduction.md) használatát igényli|
|Szükséges szerepkörök és engedélyek:|A kapcsolódó Azure-előfizetéshez tartozó **tulajdonos** vagy **közreműködő**|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="connect-your-gcp-account"></a>A GCP-fiók csatlakoztatása

Kövesse az alábbi lépéseket a GCP Cloud Connector létrehozásához a Google Cloud-erőforrások a szervezet vagy a projekt szintjén való összekapcsolásához. 

> [!TIP]
> Ismerkedjen meg a Google Cloud Resource-hierarchiával az online docs [itt](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>1. lépés A GCP Security Command Center beállítása a biztonsági állapot elemzésével

A szervezet összes GCP-projektje esetén a következőket kell tennie:

1. A **GCP Security Command Center** beállítása [a GCP dokumentációjában](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)ismertetett utasítások alapján.
1. Engedélyezze a **biztonsági állapot elemzését** [a GCP dokumentációjában található utasítások](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)alapján.
1. Ellenőrizze, hogy van-e adatfolyam a biztonsági parancs központjához.

A GCP-környezet biztonsági konfigurációhoz való csatlakoztatására vonatkozó utasítások a Google által a biztonsági konfigurációs javaslatok felhasználására vonatkozó ajánlásokat követve jelennek meg. Az integráció kihasználja a Google Security parancssori központot, és további erőforrásokat fog használni, amelyek hatással lehetnek a számlázásra.

A biztonsági állapot elemzésének első engedélyezésekor több óráig is eltarthat, amíg az adathozzáférés elérhetővé válik.


### <a name="step-2-enable-gcp-security-command-center-api"></a>2. lépés A GCP Security Command Center API engedélyezése

1. A Google **Cloud Console API könyvtárából** válassza ki azt a projektet, amelyhez csatlakozni szeretne Azure Security Centerhoz.
1. Az API-függvénytárban keresse meg és válassza ki a **Security Command Center API**-t.
1. Az API lapon válassza az **Engedélyezés** lehetőséget.

További információ a [Security Command Center API](https://cloud.google.com/security-command-center/docs/reference/rest/)-ról.


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>3. lépés Dedikált szolgáltatásfiók létrehozása a biztonsági konfiguráció integrálásához

1. A **GCP-konzolon** válassza ki azt a projektet, amelyhez csatlakozni szeretne Security Centerhoz.
1. A **navigációs menü** **iam & felügyeleti** beállítások területén válassza a **szolgáltatásfiókok** elemet.
1. Válassza a **szolgáltatásfiók létrehozása** lehetőséget.
1. Adja meg a fiók nevét, majd válassza a **Létrehozás** lehetőséget.
1. Adja meg a **szerepkört** **Security Center felügyeleti megjelenítőként**, és válassza a **Folytatás** lehetőséget.
1. A **szolgáltatásfiók-hozzáférés engedélyezése a felhasználóknak** nem kötelező. Válassza a **Kész** lehetőséget.
1. Másolja a létrehozott szolgáltatásfiók **e-mail-értékét** , és mentse későbbi használatra.
1. A **navigációs menü** **iam & felügyeleti** beállítások területén válassza a **iam** lehetőséget.
    1. Váltson a szervezeti szintre.
    1. Válassza a **Hozzáadás** lehetőséget.
    1. Az **új tagok** mezőben illessze be a korábban átmásolt **e-mail-értéket** .
    1. Adja meg a szerepkört **Security Center rendszergazda megjelenítőként** , majd válassza a mentés lehetőséget.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="A vonatkozó GCP engedélyek beállítása":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>4. lépés: Titkos kulcs létrehozása a dedikált szolgáltatásfiók számára
1. Váltson a projekt szintjére.
1. A **navigációs menü** **iam & felügyeleti** beállítások területén válassza a **szolgáltatásfiókok** elemet.
1. Nyissa meg a dedikált szolgáltatásfiókot, és válassza a szerkesztés lehetőséget.
1. A **kulcsok** szakaszban válassza a **Kulcs hozzáadása** lehetőséget, majd **hozzon létre új kulcsot**.
1. A titkos kulcs létrehozása képernyőn válassza a **JSON** lehetőséget, majd válassza a **Létrehozás** lehetőséget.
1. Mentse ezt a JSON-fájlt későbbi használatra.


### <a name="step-5-connect-gcp-to-security-center"></a>5. lépés A GCP Security Centerhoz való kapcsolódása 
1. A Security Center menüjében válassza a **Felhőbeli összekötők** lehetőséget.
1. Válassza a GCP-fiók hozzáadása lehetőséget.
1. A bevezetési lapon tegye a következőket, majd kattintson a **tovább** gombra.
    1. Érvényesítse a kiválasztott előfizetést.
    1. A **megjelenítendő név** mezőben adja meg az összekötő megjelenítendő nevét.
    1. A **szervezet azonosítója** mezőben adja meg a szervezet azonosítóját. Ha nem tudja, tekintse meg a [szervezetek létrehozásával és kezelésével](https://cloud.google.com/resource-manager/docs/creating-managing-organization)foglalkozó témakört.
    1. A **titkos kulcs** fájlja mezőben keresse meg a 4. lépésben letöltött JSON-fájlt [. Hozzon létre egy titkos kulcsot a dedikált szolgáltatásfiók számára](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>6. lépés Visszaigazolás

Ha az összekötő létrehozása sikeres volt, és a GCP biztonsági parancssori központ megfelelően van konfigurálva:

- A GCP CIS standard a Security Center szabályozási megfelelőségi irányítópultján jelenik meg.
- A GCP-erőforrásokra vonatkozó biztonsági javaslatok megjelennek a Security Center-Portálon és a szabályozási megfelelőségi irányítópulton 5-10 perccel a bevezetést követően: a :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Security Center ajánlásai oldalon található források és javaslatok"::: .


## <a name="monitoring-your-gcp-resources"></a>A GCP-erőforrások figyelése

Ahogy fent látható, Azure Security Center biztonsági javaslatai oldalon a GCP-erőforrások és az Azure-és AWS-erőforrások is megjelennek az igazi többfelhős nézetben.

Ha az erőforrás típusa szerint szeretné megtekinteni az összes aktív javaslatot az erőforrásokhoz, használja a Security Center eszköz leltározási lapját, és szűrje a GCP erőforrástípus, amelyben érdekli:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="A tárgyieszköz-leltár oldal erőforrástípus-szűrője, amely a GCP beállításokat jeleníti meg"::: 


## <a name="faq-for-connecting-gcp-accounts-to-azure-security-center"></a>A GCP-fiókok Azure Security Centerhoz való csatlakoztatásával kapcsolatos gyakori kérdések

### <a name="can-i-connect-multiple-gcp-accounts-to-security-center"></a>Csatlakozhatok több GCP-fiókhoz is Security Center?
Igen. A fent említettek szerint a Google Cloud-erőforrásokat a szervezet vagy a projekt szintjén is összekapcsolhatjuk. Ismerkedjen meg a Google Cloud Resource-hierarchiával az online docs [itt](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-security-center"></a>Van egy API a GCP-erőforrások Security Centerhez való csatlakoztatásához?
Igen. Security Center felhőalapú összekötők létrehozásához, szerkesztéséhez vagy törléséhez REST API használatával tekintse meg az [Összekötők API](/rest/api/securitycenter/connectors)részleteit.

## <a name="next-steps"></a>Következő lépések

A GCP-fiók csatlakoztatása a Azure Security Centerben elérhető többfelhős felület része. A kapcsolódó információk a következő oldalon találhatók:

- [AWS-fiókok összekötése Azure Security Center](quickstart-onboard-aws.md)
