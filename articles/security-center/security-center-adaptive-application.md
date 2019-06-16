---
title: Adaptív alkalmazásvezérlők az Azure Security Centerben | Microsoft Docs
description: Ebből a dokumentumból megismerheti, hogyan használható az adaptív alkalmazásvezérlés az Azure Security Centerben az Azure-beli virtuális gépeken futó alkalmazások engedélyezési listákra való felvételéhez.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 4a8a241df38c258dc1747f04c6079d29ee25b3ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968863"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Adaptív alkalmazásvezérlők az Azure Security Centerben
Az útmutató azt ismerteti, hogyan konfigurálható az alkalmazásvezérlés az Azure Security Centerben.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Mire szolgálnak a Security Center adaptív alkalmazásvezérlői?
Adaptív Alkalmazásvezérlés egy intelligens, automatizált teljes körű engedélyezési megoldás az Azure Security Centerben. Ez segít vezérlőelem, mely alkalmazások futhatnak az Azure és a nem Azure (Windows és Linux), amely az egyéb előnyök, többek között segít felvértezni virtuális gépeit a kártevők ellen. A Security Center gépi tanulás segítségével elemezheti a virtuális gépeken futó alkalmazásokat, és segít az adott engedélyezési szabályok alkalmazásában. Ez a képesség nagyban leegyszerűsíti az konfigurálásának és karbantartásának alkalmazás-engedélyezési házirendeket, lehetővé téve:

- Blokkolások vagy riasztások rosszindulatú alkalmazásokat is beleértve, amelyeket a kártevővédelmi megoldások egyébként nem észlelnének tett kísérleteket.
- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.
- A nem kívánt szoftverek adott környezetben való használatának megelőzését.
- Az elavult és nem támogatott alkalmazások futtatásának letiltását.
- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.
- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

> [!NOTE]
> A nem Azure- és Linux rendszerű virtuális gépek az adaptív alkalmazásvezérlők csak naplózási módban támogatott.

## <a name="how-to-enable-adaptive-application-controls"></a>Az adaptív alkalmazásvezérlők engedélyezése
Az adaptív alkalmazásvezérlők segítségével meghatározhatja egy adott alkalmazásokat, amelyek futhatnak a konfigurált virtuális gépek csoportjai. Ez a funkció az Azure és nem – az Azure Windows (összes verzió, klasszikus vagy Azure Resource Manager) és a Linux rendszerű virtuális gépek és kiszolgálók érhető el. A következő lépésekkel konfigurálhatók az alkalmazások engedélyezési listái a Security Centerben:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali panelen válassza ki az **Advanced cloud defense** (Speciális felhővédelem) szakaszban található **Adaptive application controls** (Adaptív alkalmazásvezérlők) elemet.

    ![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Megjelenik az **Adaptív alkalmazásvezérlők** oldal.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

A **Virtuális gépek csoportjai** szakaszban három lap található:

* **Configured** (Konfigurált): Azon csoportok listája, amelyeken már konfigurálva van az alkalmazásvezérlés.
* **Recommended** (Ajánlott): Azon csoportok listája, amelyek esetében javasolt az alkalmazásvezérlés használata. A Security Center gépi tanulási módszerekkel azonosítja azokat a virtuális gépeket, amelyeken érdemes alkalmazásvezérlést beállítani, az alapján, hogy az adott virtuális gépek folyamatosan ugyanazokat az alkalmazásokat futtatják-e.
* **No recommendation** (Nincs javaslat): Azon csoportok listája, amelyek olyan virtuális gépeket tartalmaznak, amelyekhez nem tartoznak alkalmazásvezérlési javaslatok. például olyanokat, amelyeken az alkalmazások folyamatosan változnak, és még nem értek el egy stabil állapotot.

> [!NOTE]
> A Security Center saját fürtözési algoritmust használ virtuálisgép-csoportok létrehozásához, ezzel biztosítva, hogy a hasonló virtuális gépek a javasolt optimális alkalmazásvezérlési szabályzatot kapják meg.
>
>

### <a name="configure-a-new-application-control-policy"></a>Új alkalmazásvezérlési szabályzat konfigurálása
1. Kattintson a **Recommended** (Ajánlott) lapra azon csoportok listájáért, amelyeken javasolt bevezetni az alkalmazásvezérlést:

   ![Ajánlott](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista a következőket tartalmazza:

   - **Csoport neve**: Az előfizetés és a csoport neve
   - **Virtuális gépek és számítógépek**: A csoportban található virtuális gépek száma
   - **Állapot**: a javaslatok állapota
   - **Súlyosság**: a javaslatok súlyossági szintje

2. Egy csoport megnyitásához kattintson a **Alkalmazásvezérlési szabályok létrehozása** lehetőséget.

   ![Alkalmazásvezérlési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. Az a **válassza ki a virtuális gépek**, tekintse át a javasolt virtuális gépek listáját, és nem szeretné, hogy egy alkalmazás-engedélyezési házirend a alkalmazni mellől. Ezután két lista jelenik meg:

   - **Ajánlott alkalmazások**: gyakori csoporton belüli virtuális gépeken, és futtatható javasolt alkalmazások listáját.
   - **További alkalmazások**: alkalmazások, amelyek kevésbé gyakoriak a csoporton belüli virtuális gépeken, vagy kihasználhatóként ismertek listáját (lásd lejjebb), és a felülvizsgálatra javasolt.

4. Tekintse át az egyes listákon található alkalmazásokat, és törölje a jelölést azok mellől, amelyeket nem szeretne alkalmazni. A listák a következőket tartalmazzák:

   - **NÉV**: a tanúsítvány adatait vagy egy alkalmazás teljes elérési útja
   - **FILE TYPES** (Fájltípusok): az alkalmazás fájltípusa. Ez lehet EXE, szkript, MSI vagy bármely oszloptípus permutációkban kiválasztott.
   - **EXPLOITABLE**: egy figyelmeztető ikon jelzi, ha egy adott alkalmazás támadók megkerülhetik az alkalmazásengedélyezési megoldást. Érdemes áttekinteni ezeket az alkalmazásokat az engedélyezésük előtt.
   - **USERS** (Felhasználók): azok a felhasználók, akik számára javasolt az alkalmazás futtatásának engedélyezése

5. Ha végzett a kiválasztással, válassza a **Create** (Létrehozás) lehetőséget. <br>
   Miután a létrehozás lehetőséget választja, az Azure Security Center automatikusan létrehozza felül a beépített engedélyezési megoldás érhető el a megfelelő szabályokat a Windows-kiszolgálókon (az AppLocker).

> [!NOTE]
> - A Security Centernek legalább kétheti adatra van szüksége ahhoz, hogy létrehozhassa az alapkonfigurációt, és feltölthesse a virtuálisgép-csoportok szerinti egyéni javaslatokat. A Security Center standard szintű változatának új ügyfelei számíthatnak arra, hogy a virtuálisgép-csoportjaik először a *nincs javaslat* lapon jelennek meg.
> - A Security Center Adaptív alkalmazásvezérlői nem támogatják az olyan virtuális gépeket, amelyekhez egy GPO vagy egy helyi biztonsági szabályzat már engedélyezett AppLocker-szabályzatot.
> -  Ajánlott eljárásként a Security Center minden esetben megpróbálja biztonsági létrehozni egy közzétételi szabályt az alkalmazásokat, amelyek a kiválasztott számára engedélyezett, és csak akkor, ha egy alkalmazás nem rendelkezik közzétevői adatokkal (azaz nincs aláírva), egy elérésiút-szabály jön létre a teljes elérési útját a adott alkalmazás.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Alkalmazásvezérléssel konfigurált csoportok szerkesztése és monitorozása

1. Szerkesztéséhez és monitorozásához konfigurált egy engedélyezési szabályzatot, térjen vissza a **adaptív alkalmazásvezérlők** lapon, és válassza **KONFIGURÁLT** alatt **virtuális gépek csoportjai**:

   ![Csoportok](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista a következőket tartalmazza:

   - **Csoport neve**: az előfizetés és a csoport neve
   - **Virtuális gépek és számítógépek**: a csoportban található virtuális gépek száma
   - **Mód**: Vizsgálati üzemmód naplózza alkalmazások; futtatására tett kísérletek Érvényesíti a rendszer nem engedélyezi az alkalmazások futtatásához
   - **Riasztások**: aktuális szabálysértések

2. Kattintson a módosításokat a csoport a **Alkalmazásvezérlési szabályzat szerkesztése** lapot.

   ![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. A **Protection Mode** (Védelem módja) alatt a következő lehetőségek közül választhat:

   - **Audit** (Felügyelet): ebben a módban az alkalmazásvezérlési megoldás nem kényszeríti ki a szabályokat, és csak naplózza a tevékenységeket a védett virtuális gépeken. A használata olyan forgatókönyvek esetén javasolt, amikor először szeretné megfigyelni az általános működést, mielőtt blokkolná egy adott alkalmazás futtatását a cél virtuális gépen.
   - **Enforce** (Kényszerítés): ebben a módban az alkalmazásvezérlési megoldás kikényszeríti a szabályokat, és gondoskodik róla, hogy blokkolva legyenek azok az alkalmazások, amelyeknek a futtatása nem engedélyezett.

   > [!NOTE]
   > -  **Kényszerítése** védelmi mód le van tiltva, további értesítésig.
   > - Amint azt fent említettük, az új alkalmazásvezérlési szabályzatok alapértelmezés szerint minden esetben *Felügyelet* módban lesznek konfigurálva. 
   >

4. A **szabályzatbővítmény**, adjon hozzá bármilyen alkalmazás elérési útja, amely számára engedélyezni kívánja. Elérési utak felvétele után a Security Center frissíti az engedélyezési házirend a kiválasztott virtuális gépek csoportján belül a virtuális gépeken, és ezekhez az alkalmazásokhoz, amelyek már érvényben lévő szabályok mellett a megfelelő szabályokat hoz létre.

5. Tekintse át az aktuális szabálysértések szerepel a **legutóbbi riasztások** szakaszban. Átirányítja őket az egyes sorokra kattintva a **riasztások** belül az Azure Security Center lapon, és a kapcsolódó virtuális gépek az Azure Security Center által észlelt összes riasztás megtekintése.
   - **Riasztások**: naplózott szabálysértések.
   - **nem. virtuális gépek**: Ez a riasztástípus rendelkező virtuális gépek számát.

6. A **közzétevő-engedélyezési szabályok**, **elérésiút-engedélyezési szabályok**, és **ujjlenyomat-engedélyezési szabályok** láthatja, hogy mely szabályok jelenleg alkalmazásengedélyezés a szabálygyűjtemény-típusok megfelelően konfigurálta a csoporton belüli virtuális gépeken. Az egyes szabályokhoz tekintheti meg:

   - **A szabály**: Amely alapján határozza meg, ha az alkalmazás futtatásához jogosult az AppLocker által megvizsgál egy alkalmazás megadott paramétereket.
   - **Fájltípus**: A fájltípusok, egy adott szabály alá esnek. Ez a következők bármelyike lehet: EXE, szkript, MSI vagy ezek bármely permutációkban kiválasztott fájl típusa.
   - **Felhasználók**: Név vagy a felhasználók száma, akik egy alkalmazás, amelyet egy alkalmazás-engedélyezési szabály jelez futtatható.

   ![Engedélyezési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Kattintson az egyes sorok végén található három pontra, ha azt szeretné, törölheti az adott szabályokat, vagy szerkesztheti az engedélyezett felhasználókat.

8. Módosítása után egy **adaptív alkalmazásvezérlők** házirendet, kattintson a **mentése**.

### <a name="not-recommended-list"></a>Nem ajánlott gépek listája

Csak a Security Center javasolja a virtuális gépek, alkalmazások egy stabil készlete fut. alkalmazás-engedélyezési házirendek. Ha a virtuális gépeken az alkalmazások folyamatosan változnak, a rendszer nem hoz létre ajánlásokat.

![Ajánlás](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista a következőket tartalmazza:
- **Csoport neve**: az előfizetés és a csoport neve
- **Virtuális gépek és számítógépek**: a csoportban található virtuális gépek száma

Az Azure Security Center lehetővé teszi egy alkalmazás-engedélyezési házirend nem ajánlott azon virtuálisgép-csoportok, valamint adja meg. Ugyanezek az elvek kövesse korábban leírt, ezeket a csoportokat, valamint alkalmazás-engedélyezési házirend konfigurálása.

## <a name="move-a-vm-from-one-group-to-another"></a>Egy virtuális gép áthelyezése egy másik

 Ha egy csoportot a virtuális gép áthelyezése egy másik, a alkalmazni Alkalmazásvezérlési szabályzat változik, a csoport beállításait, amikor áthelyezték azt. Egy nem konfigurált csoporthoz, ami minden korábban alkalmazott a VM Alkalmazásvezérlési szabályzat eltávolítása a beállított csoportból is áthelyezheti egy virtuális Gépet.

 1. A a **adaptív alkalmazásvezérlők** lapon a a **KONFIGURÁLT** lapra, majd a csoport, amely a virtuális gép jelenleg lesz áthelyezve.
1. Kattintson a **konfigurált virtuális gépek és számítógépek**.
1. Kattintson a három pontra a virtuális gép áthelyezése, és kattintson a sor **áthelyezése**. A **számítógép áthelyezése a másik csoporthoz** ablak nyílik meg.

    ![Védelem](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Válassza ki a virtuális gép áthelyezése, majd kattintson a csoport **számítógép áthelyezése**, és kattintson a **mentése**.

    ![Védelem](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Ügyeljen arra, hogy kattintson **mentése** kattintás után **számítógép áthelyezése**. Ha nem kattint az **mentése**, akkor a számítógép nem helyezhető át.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan adaptív Alkalmazásvezérlés az Azure és a nem Azure-ban futó engedélyezett alkalmazások az Azure Security Center használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
