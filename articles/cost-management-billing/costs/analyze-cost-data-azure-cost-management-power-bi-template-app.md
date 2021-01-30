---
title: Az Azure költségeinek elemzése a Power BI-alkalmazással
description: Ez a cikk ismerteti az Azure Cost Management Power BI-alkalmazás telepítését és használatát.
author: bandersmsft
ms.author: banders
ms.date: 1/29/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: d3439f5a86c445898d95191a32496b5f0c86be0b
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070157"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Az Azure költségeinek elemzése a Nagyvállalati Szerződésekhez (EA) készült Azure Cost Management Power BI-alkalmazással

Ez a cikk ismerteti az Azure Cost Management Power BI-alkalmazás telepítését és használatát. Az alkalmazás segít az Azure-költségek a Power BI-ban való elemzésében és kezelésében. Az alkalmazással monitorozhatja a költségeket és a használati trendeket, továbbá költségoptimalizálási lehetőségeket azonosíthat a kiadások csökkentése érdekében.

A Azure Cost Management Power BI alkalmazás jelenleg csak [nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)rendelkező ügyfeleket támogat.

Az alkalmazás korlátozza a testreszabhatóság kiszabását. Ha szeretné módosítani és kiterjeszteni az alapértelmezett szűrőket, nézeteket és vizualizációkat az igényeinek megfelelően testre szabva, használja a [Azure Cost Management-összekötőt Power bi Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management) helyett. Az Azure Cost Management-összekötővel más forrásokból származó további adatokhoz is csatlakozhat, így testreszabott jelentéseket hozhat létre, amelyekkel átfogó képet kaphat a teljes üzleti díjszabásról. Az összekötő támogatja a Microsoft ügyfél-szerződéseit is.

> [!NOTE]
> A Power BI-sablonalkalmazások nem támogatják a PBIX-fájl letöltését.

## <a name="prerequisites"></a>Előfeltételek

- [Power BI Pro-licenc](/power-bi/service-self-service-signup-for-power-bi) szükséges az alkalmazás letöltéséhez és használatához.
- Az adatokhoz való kapcsolódáshoz [vállalati rendszergazdai](../manage/understand-ea-roles.md) fiókot kell használnia. A Vállalati rendszergazda (csak olvasási) szerepkör támogatott.

## <a name="installation-steps"></a>A telepítés lépései

Az alkalmazás telepítése:

1. Nyissa meg az [Azure Cost Management Power BI-alkalmazást](https://aka.ms/costmgmt/ACMApp).
2. A Power BI AppSource oldalán válassza a **Letöltés most** lehetőséget.
3. A használati feltételek és adatvédelmi szabályzat elfogadásához válassza a **Folytatás** lehetőséget.
4. Az **A Power BI-alkalmazás telepítése** mezőben válassza a **Telepítés** lehetőséget.
5. Ha szükséges, hozzon létre egy munkaterületet, majd válassza a **Folytatás** lehetőséget.
6. Ha a telepítés befejeződött, egy megjelenő értesítés jelzi, hogy az új alkalmazás készen áll.
7. Válassza az **Ugrás az alkalmazáshoz** lehetőséget.
8. A **Bevezetés az új alkalmazás használatába** terület **Az adatok csatlakoztatása** részén válassza a **Csatlakozás** lehetőséget.  
  ![Bevezetés az új alkalmazás használatába – Csatlakozás](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. A megjelenő párbeszédablakban adja meg EA regisztrációs számát a **BillingProfileIdOrEnrollmentNumber** értékeként. Adja meg, hogy hány hónapnyi adatot kíván lekérni. Hagyja meg a **Regisztrációs szám** alapértelmezett **Hatókör** értékét, majd válassza a **Következő** lehetőséget.  
  ![EA regisztrációs adatok megadása](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. A következő párbeszédablak csatlakozik az Azure-hoz, és beolvassa a fenntartott példányokra vonatkozó javaslatokhoz szükséges adatokat. *Hagyja meg az alapértelmezett értékek konfigurációját*, majd válassza a **Bejelentkezés** lehetőséget.  
  ![Képernyőkép a Csatlakozás az Azure Cost Managementhez alkalmazás párbeszédablakáról, az alapértelmezett értékekkel.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. A telepítés utolsó lépésében csatlakozni kell az EA-regisztrációhoz, amihez [vállalati rendszergazdai](../manage/understand-ea-roles.md) fiók szükséges. Válassza a **Bejelentkezés** elemet az EA-regisztrációval való hitelesítéshez. Ez a lépés egy adatfrissítési műveletet is elindít a Power BI-ban.  
  ![Csatlakozás EA-regisztrációhoz](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > Az adatfrissítési folyamat befejeződése eltarthat egy ideig. Az időtartam a hónapok megadott számától és a szinkronizálásra váró adatok mennyiségétől függ.
12. Az adatok frissítési állapotának ellenőrzéséhez válassza az **Adathalmazok** lapot a munkaterületén. Pillantson a Frissített időbélyeg melletti területre. Ha a frissítés még zajlik, látni fog egy kijelzőt, amely azt mutatja, hogy a frissítés folyamatban van.  
  ![Adatok frissítése](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

Az adatfrissítés befejezése után válassza ki az Azure Cost Management alkalmazást az előre létrehozott jelentések megtekintéséhez.

## <a name="reports-available-with-the-app"></a>Az alkalmazásban elérhető jelentések

Az alkalmazásban az alábbi jelentések érhetők el.

**Első lépések** – A dokumentációra mutató hasznos hivatkozások és hivatkozások a visszajelzéshez.

**Fiók áttekintése** – A jelentés az információk havi összegzését jeleníti meg, beleértve a következőket:

- Díjak a kreditekhez viszonyítva
- Új vásárlások
- Az Azure Marketplace díjai
- Kerettúllépések és teljes díjak

**Az előfizetések és az erőforráscsoportok szerinti használat** – Időalapú költségek nézete, valamint az előfizetések és erőforráscsoportok szerinti költségeket megjelenítő diagramok.

**Szolgáltatások szerinti használat** – Áttekintés a MeterCategory szerint a használat idejéről. Nyomon követheti a használati adatait, és részletesen megismerheti az anomáliákat a használati csúcsok vagy mélypontok értelmezéséhez.

**Az 5 legnépszerűbb használati illesztő** – A jelentés szűrt költségösszegzést mutat be az 5 leggyakoribb MeterCategory és a megfelelő MeterName szerint.

**Windows Server – AHB-használat** – A jelentés azon virtuális gépek számát jeleníti meg, amelyeken engedélyezve van az Azure Hybrid Benefit. Megjeleníti a virtuális gépek által használt magok/vCPU-k számát is.

![Azure Hybrid Benefit – Teljes jelentés](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

A jelentés azokat a Windows rendszerű virtuális gépeket is azonosítja, amelyeken a Hybrid Benefit **engedélyezett**, de _kevesebb, mint_ 8 vCPU-val rendelkeznek. Azt is megmutatja, ha a Hybrid Benefit **nincs engedélyezve**, de 8 _vagy több_ vCPU van. Ez az információ segíti a Hybrid Benefit teljes kihasználását. Használja az előnyt a legdrágább virtuális gépén, hogy maximalizálhassa a potenciális megtakarítást.

![Azure Hybrid Benefits – Kevesebb, mint 8 vCPU, és a vCPU-k nincsenek engedélyezve](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**RI – Költséghelyi elszámolás** – A jelentés segít megismerni, hogy a fenntartott példányokra (RI) vonatkozó előny hol és mekkora részben van alkalmazva régiónként, előfizetésenként, erőforráscsoportonként vagy erőforrásonként. A jelentés amortizált használati adatokat használ a nézet megjelenítéséhez.

Alkalmazhat szűrőt a _chargetype_ elemre az RI alacsony kihasználtságra vonatkozó adatainak megtekintéséhez.

Az amortizált adatokkal kapcsolatos további információkért tekintse meg a [Nagyvállalati Szerződés foglalási költségeinek és kihasználtságának lekérését](../reservations/understand-reserved-instance-usage-ea.md) ismertető szakaszt.

**RI – Megtakarítások** – A jelentés bemutatja az előfizetéshez, erőforráscsoporthoz és erőforrásszinthez kapcsolódó foglalások révén keletkezett megtakarításokat. A következőket jeleníti meg:

- Költség foglalással
- Becsült igény szerinti költségek, ha a foglalás nem vonatkozik a használatra
- A foglalás révén keletkező költségmegtakarítások

 A jelentés kivonja a kihasználatlan foglalásból eredő költséget a teljes megtakarításból. A pazarlás foglalás nélkül nem fordulna elő.

Az adatokra való építkezéshez használhat amortizált használati adatokat.

<a name="shared-recommendation"></a>
**VM RI-lefedettség (megosztott javaslat)** – A jelentés az igény szerinti VM-használat és az RI VM-használata között oszlik meg a megadott időszakra vonatkozóan. VM RI-vásárlásokhoz ad javaslatokat megosztott hatókörben.

A jelentés használatához válassza a részletes elemzés szűrőjét.

![VM RI-lefedettségi jelentés – A részletes elemzés kiválasztása](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Jelölje ki az elemezni kívánt régiót. Ezután válassza ki a példányméret-rugalmassági csoportot és egyebeket.

A rendszer minden részletezési szinten az alábbi szűrőket alkalmazza a jelentésre:

- A jobb oldali lefedettségi adat a szűrő, amely azt jeleníti meg, mennyi használat lesz felszámolva az igény szerinti díjszabás szerint a foglalás által lefedett mennyiséghez képest.
- A rendszer a javaslatokat is szűri.

A javaslatok táblázat a felhasznált virtuálisgép-méretek alapján nyújt javaslatokat a foglalások vásárlásához.

A _Normalizált méret_ és az _Ajánlott normalizált mennyiség_ értékek segítségével a vásárlást egy példányméret-rugalmassági csoport legkisebb méretére normalizálhatja. Ez az információ akkor hasznos, ha csak egy foglalást tervez vásárolni a példányméret-rugalmassági csoport minden méretéhez.

![RI-javaslatok](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**VM RI-lefedettség (egyetlen javaslat)** – A jelentés az igény szerinti VM-használat és az RI VM-használata között oszlik meg a megadott időszakra vonatkozóan. Egy előfizetés hatókörében ad javaslatokat VM RI-vásárlásokhoz.

A jelentés használatának részleteiről a [VM RI-lefedettség (megosztott javaslat)](#shared-recommendation) szakaszból tájékozódhat.

**RI-vásárlások** – A jelentés a megadott időszakra vonatkozó RI-vásárlásokat jeleníti meg.

**Árlista** – A jelentés a számlázási fiókra vagy az EA-regisztrációra vonatkozó részletes árlistát jeleníti meg.

## <a name="troubleshoot-problems"></a>Problémák elhárítása

Amennyiben problémái adódtak a Power BI alkalmazással, az alábbi hibaelhárítási információk a segítségére lehetnek.

### <a name="error-processing-the-data-in-the-dataset"></a>Hiba történt az adatkészlet adatainak feldolgozása során

A következő hibaüzenet jelenhet meg:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

A(z) `<TableName>` helyett egy táblanév jelenik meg.

#### <a name="cause"></a>Ok

A **Hatókör** alapértelmezett `Enrollment Number` értéke módosult a Cost Managementhez való csatlakozás során.

#### <a name="solution"></a>Megoldás

Csatlakozzon újra a Cost Managementhez, és állítsa a **Hatókör** értékét a következőre: `Enrollment Number`. Ne adja meg a szervezete regisztrációs számát, inkább írja be a(z) `Enrollment Number` értéket pont úgy, ahogy az alábbi képen megjelenik.

![EA-regisztrációs adatok megadása](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>BudgetAmount hiba

A következő hibaüzenet jelenhet meg:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Ok

Ez a hiba az alapul szolgáló metaadatok hibája miatt következik be. A probléma azért merül fel, mert nincs elérhető költségvetés a **Cost Management > Költségvetés** alatt az Azure Portalon. A hiba javítása folyamatban van a Power BI Desktop és a Power BI szolgáltatásban. 

#### <a name="solution"></a>Megoldás

- Amíg a hiba nincs kijavítva, megkerülheti a problémát egy tesztköltségvetés hozzáadásával az Azure Portalon a számlázási fiók/EA-regisztráció szintjén. A tesztköltségvetés megszünteti a Power BI-kapcsolat blokkolását. A költségvetés létrehozásával kapcsolatos további információkért lásd: [Oktatóanyag: Azure-költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md).


### <a name="invalid-credentials-for-azureblob-error"></a>Érvénytelen hitelesítő adatok az AzureBlobhoz hiba

A következő hibaüzenet jelenhet meg:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha módosítja az AutoFitComboMeter blobkapcsolat hitelesítési módszerét.

#### <a name="solution"></a>Megoldás

1. Csatlakozzon az adatokhoz.
1. Az EA-regisztráció és a hónapok számának megadása után bizonyosodjon meg arról, hogy az alapértelmezett **Névtelen** érték van beállítva a Hitelesítési módszerhez, és a **Nincs** érték az Adatvédelmi szinthez.  
  ![Képernyőkép a Csatlakozás az Azure Cost Managementhez alkalmazás párbeszédablakáról, amelyben Névtelen és Nincs értékek vannak megadva.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. A következő oldalon állítsa be az **OAuth2** értéket a Hitelesítési módszerhez, és a **Nincs** értéket az Adatvédelmi szinthez. Majd jelentkezzen be a regisztrációval való hitelesítéshez. Ez a lépés egy Power BI-adatfrissítési műveletet is elindít.


## <a name="data-reference"></a>Adatreferencia

Az alábbi információk az alkalmazáson keresztül elérhető adatokat foglalják össze. Olyan API-kra mutató hivatkozásokat is talál, amelyek részletesen bemutatják az adatmezőket és -értékeket.

| **Táblahivatkozás** | **Leírás** |
| --- | --- |
| **AutoFitComboMeter** | Az alkalmazásban foglalt adatok, amelyekkel a példánycsalád csoport legkisebb méretére normalizálható az RI- javaslat és -használat. |
| [**Egyenleg összegzése**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | A Nagyvállalati Szerződések egyenlegének összegzése. |
| [**Költségvetések**](/rest/api/consumption/budgets/get#definitions) | A költségvetés részletei a tényleges költségek és használat a meglévő költségvetéshez viszonyított megtekintéséhez. |
| [**Árlisták**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Vonatkozó fogyasztási díjak a megadott számlázási profilhoz vagy EA-regisztrációhoz. |
| [**RI-díjak**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | A fenntartott példányaihoz társított díjak az utolsó 24 hónap tekintetében. |
| [**RI-javaslatok (megosztott)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Az elmúlt 7 nap összes előfizetés-használati trendjén alapuló javaslatok a fenntartott példányok vásárlására vonatkozóan. |
| [**RI-javaslatok (egy előfizetéshez)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Az elmúlt 7 nap egyetlen előfizetésre vonatkozó használati trendjén alapuló javaslatok a fenntartott példányok vásárlásához. |
| [**RI-használati adatok**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Az elmúlt hónap felhasználási adatai a meglévő fenntartott példányokra vonatkozóan. |
| [**RI-használat összegzése**](/rest/api/consumption/reservationssummaries/list) | Az Azure-beli foglalások napi használatának százalékos aránya. |
| [**Használati adatok**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | A felhasznált mennyiségek és a becsült költségek részletezése az EA-regisztráció adott számlázási profiljához. |
| [**Amortizált használati adatok**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | A felhasznált mennyiségek és a becsült amortizált költségek részletezése az EA-regisztráció adott számlázási profiljához. |

## <a name="next-steps"></a>További lépések

Az adatok konfigurálásával, a frissítéssel, valamint a jelentések megosztásával és további testreszabásával kapcsolatos információkért tekintse meg az alábbi cikkeket:

- [Ütemezett frissítés beállítása](/power-bi/refresh-scheduled-refresh)
- [A Power BI-irányítópultok és -jelentések megosztása munkatársakkal és másokkal](/power-bi/service-share-dashboards)
- [Önmaga és mások feliratkoztatása jelentésekre és irányítópultokra a Power BI szolgáltatásban](/power-bi/service-report-subscribe)
- [Jelentés letöltése a Power BI szolgáltatásból a Power BI Desktopba](/power-bi/service-export-to-pbix)
- [Jelentés mentése a Power BI szolgáltatásban és a Power BI Desktopban](/power-bi/service-report-save)
- [Jelentés létrehozása a Power BI szolgáltatásban adathalmaz importálásával](/power-bi/service-report-create-new)