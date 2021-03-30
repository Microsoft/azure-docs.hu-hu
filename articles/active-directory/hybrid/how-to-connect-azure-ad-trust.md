---
title: Azure AD Connect – AD FS megbízhatóság kezelése az Azure AD-vel Azure AD Connect használatával | Microsoft Docs
description: Az Azure AD-megbízhatóság működési adatai az Azure AD-kapcsolaton keresztül.
keywords: AD FS, ADFS, AD FS felügyelet, HRE-kapcsolat, csatlakozási, Azure AD, megbízhatóság, HRE, jogcím, jogcím, jogcím szabályai, kiadás, átalakítás, szabályok, biztonsági mentés, visszaállítás
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360095"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>AD FS-megbízhatóság Azure AD általi kezelése az Azure AD Connect használatával

## <a name="overview"></a>Áttekintés

A Azure AD Connect felügyelheti a helyszíni Active Directory összevonási szolgáltatás (AD FS) és az Azure AD közötti összevonást. Ez a cikk áttekintést nyújt a következőről:

* Az Azure AD Connect által a megbízhatóságra konfigurált különböző beállítások
* Az Azure AD Connect által beállított kiállítási átalakítási szabályok (jogcím-szabályok)
* A jogcím-szabályok biztonsági mentése és visszaállítása a frissítések és a konfigurációs frissítések között. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Azure AD Connect által vezérelt beállítások

A Azure AD Connect **csak** az Azure ad-megbízhatósághoz kapcsolódó beállításokat kezeli. A Azure AD Connect nem módosítja a többi függő entitás megbízhatóságának beállításait AD FSban. A következő táblázat a Azure AD Connect által vezérelt beállításokat tartalmazza.

| Beállítás | Leírás |
| :--- | :--- |
| Jogkivonat-aláíró tanúsítvány | Azure AD Connect segítségével alaphelyzetbe állíthatja és újból létrehozhatja a megbízhatóságot az Azure AD-vel. Azure AD Connect a jogkivonat-aláíró tanúsítványok egyszeri azonnali átváltását AD FS és frissíti az Azure AD-tartomány összevonási beállításait.|
| Jogkivonat-aláírási algoritmus | A Microsoft az SHA-256 használatát javasolja jogkivonat-aláírási algoritmusként. A Azure AD Connect képes megállapítani, hogy a jogkivonat-aláíró algoritmus az SHA-256-nél kevésbé biztonságos értékre van-e beállítva. A következő lehetséges konfigurációs művelet során frissíti a beállítást az SHA-256 értékre. Az új jogkivonat-aláíró tanúsítvány használatához frissíteni kell a függő entitás megbízhatóságát. |
| Azure AD-megbízhatósági azonosító | Azure AD Connect beállítja az Azure AD-megbízhatóság helyes azonosító értékét. AD FS egyedileg azonosítja az Azure AD-megbízhatóságot az azonosító érték használatával. |
| Azure AD-végpontok | Azure AD Connect biztosítja, hogy az Azure AD-megbízhatósághoz konfigurált végpontok mindig a lehető legutóbb ajánlott értékek legyenek a rugalmasság és a teljesítmény szempontjából. |
| Kiadás átalakítási szabályai | Az Azure AD egy összevont beállításban elérhető funkcióinak optimális teljesítményéhez számos jogcím-szabályra van szükség. Azure AD Connect biztosítja, hogy az Azure AD-megbízhatóság mindig a javasolt jogcím-szabályok megfelelő készletével legyen konfigurálva. |
| Alternatív azonosító | Ha a szinkronizálás alternatív azonosító használatára van konfigurálva, Azure AD Connect konfigurálja a AD FS, hogy alternatív azonosító használatával végezzen hitelesítést. |
| Automatikus metaadatok frissítése | Az Azure AD-vel való megbízhatósági kapcsolat automatikus metaadat-frissítésre van konfigurálva. AD FS rendszeresen ellenőrzi az Azure AD-beli megbízhatósági kapcsolat metaadatait, és naprakészen tartja, ha az Azure AD-oldalon módosul. |
| Integrált Windows-hitelesítés (IWA) | A hibrid Azure AD-csatlakozási művelet során a IWA engedélyezve van az eszközök regisztrálásához, hogy elősegítse a hibrid Azure AD-csatlakozást az alacsonyabb szintű eszközökhöz |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>A Azure AD Connect által konfigurált végrehajtási folyamatok és összevonási beállítások

Az Azure AD-csatlakozás nem frissíti az Azure AD-megbízhatóság összes beállítását a konfigurációs folyamatok során. A módosítva beállítások attól függnek, hogy melyik feladatot vagy végrehajtási folyamatot hajtja végre a rendszer. A következő táblázat a különböző végrehajtási folyamatokban érintett beállításokat sorolja fel.

| Végrehajtási folyamat | Érintett beállítások |
| :--- | :--- |
| Első lépés telepítése (expressz) | Nincsenek |
| Első lépés telepítése (új AD FS Farm) | Létrejön egy új AD FS Farm, és az Azure AD-vel való megbízhatósági kapcsolat teljesen létre lett hozva. |
| Első lépés telepítése (meglévő AD FS Farm, meglévő Azure AD-beli megbízhatóság) | Azure AD-megbízhatósági azonosító, kiállítási átalakítási szabályok, Azure AD-végpontok, alternatív azonosító (ha szükséges), automatikus metaadatok frissítése |
| Azure AD-megbízhatóság alaphelyzetbe állítása | Jogkivonat-aláíró tanúsítvány, jogkivonat-aláírási algoritmus, Azure AD megbízhatósági azonosító, kiállítási átalakítási szabályok, Azure AD-végpontok, alternatív azonosító (ha szükséges), automatikus metaadatok frissítése |
| Összevonási kiszolgáló hozzáadása | Nincsenek |
| WAP-kiszolgáló hozzáadása | Nincsenek |
| Eszközbeállítások | Kiállítási átalakítási szabályok, IWA az eszközök regisztrálásához |
| Összevont tartomány hozzáadása | Ha a tartomány első alkalommal lett hozzáadva, azaz a beállítás az egytartományos összevonás és a többtartományos összevonás között változik – Azure AD Connect újra létrehozza a bizalmi kapcsolatot. Ha az Azure AD-val való megbízhatóság már konfigurálva van több tartományhoz, a rendszer csak a kiállítási átalakítási szabályokat módosítja |
| TLS frissítése | Nincsenek |

Minden olyan művelet során, amelyben minden beállítás módosul, Azure AD Connect biztonsági másolatot készít az aktuális megbízhatósági beállításokról a **%ProgramData%\AADConnect\ADFS** címen.

![A meglévő Azure AD Trust biztonsági mentéssel kapcsolatos üzenetet megjelenítő Azure AD Connect lap](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> A verzió 1.1.873.0 előtt a biztonsági mentés csak a kiállítási átalakítási szabályokból áll, és a varázsló nyomkövetési naplófájljában biztonsági mentést készített róluk.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Azure AD Connect által beállított kiállítási átalakítási szabályok

Azure AD Connect biztosítja, hogy az Azure AD-megbízhatóság mindig a javasolt jogcím-szabályok megfelelő készletével legyen konfigurálva. A Microsoft az Azure AD-kapcsolat használatát javasolja az Azure AD-megbízhatóság kezeléséhez. Ez a szakasz felsorolja a kiállítási átalakítási szabályokat és azok leírását.

| Szabály neve | Leírás |
| --- | --- |
| UPN-probléma | Ez a szabály a userPrincipalName értékét kérdezi le a userPrincipalName szinkronizálási beállításaiban konfigurált attribútum alapján.|
| Egyéni ImmutableId-jogcím ObjectGUID és msdsconsistencyguid lekérdezése | Ez a szabály egy ideiglenes értéket helyez el a folyamatban a ObjectGUID és a msdsconsistencyguid értékhez, ha létezik |
| Msdsconsistencyguid létezésének keresése | Annak alapján, hogy a msdsconsistencyguid értéke létezik-e, vagy sem, ideiglenes jelzőt állítunk be, hogy a ImmutableId |
| A msdsconsistencyguid-t nem módosítható AZONOSÍTÓként adja ki, ha létezik | Msdsconsistencyguid kibocsátása ImmutableId, ha az érték létezik |
| ObjectGuidRule probléma, ha a msdsConsistencyGuid-szabály nem létezik | Ha a msdsconsistencyguid értéke nem létezik, a ObjectGUID értéke ImmutableId lesz kiadva. |
| NameIdentifier probléma | Ez a szabály az NameIdentifier jogcím értékét adja ki.|
| Tartományhoz csatlakoztatott számítógépek AccountType kiadása | Ha a hitelesítés alatt álló entitás egy tartományhoz csatlakoztatott eszköz, akkor ez a szabály a fiók típusát adja meg DJ-ként, amely egy tartományhoz csatlakoztatott eszközt jelez. |
| Adja meg a AccountType, ha a felhasználó nem számítógépfiók | Ha a hitelesített entitás felhasználó, akkor ez a szabály felhasználóként adja ki a fiókot. |
| Issuerid probléma, ha nem számítógépfiók | Ez a szabály kiadja a issuerId értékét, ha a hitelesítő entitás nem eszköz. Az érték a Azure AD Connect által konfigurált regex használatával jön létre. A regex a Azure AD Connect használatával összevont összes tartomány figyelembevétele után jön létre. |
| Probléma issuerid a DJ számítógép-hitelesítéshez | Ez a szabály kiadja a issuerId értéket, ha a hitelesítő entitás eszköz |
| Tartományhoz csatlakoztatott számítógépek onpremobjectguid kiadása | Ha a hitelesített entitás egy tartományhoz csatlakoztatott eszköz, akkor ez a szabály az eszköz helyszíni ObjectGUID adja ki. |
| Az elsődleges SID továbbítása | Ez a szabály a hitelesítő entitás elsődleges biztonsági azonosítóját adja meg. |
| Továbbítási jogcím – insideCorporateNetwork | Ez a szabály olyan jogcímet bocsát ki, amely segít az Azure AD számára, hogy a hitelesítés a vállalati hálózaton belülről vagy kívülről érkezik-e |
| Továbbítási jogcím – psso |   |
| Jelszó lejárati jogcímeinek kiadása | Ez a szabály három jogcímet ad ki a jelszó lejárati idejére, a hitelesítő entitás érvényességének lejárta után a jelszó számát, valamint a jelszó módosítására szolgáló URL-címet.|
| Továbbítási jogcím – authnmethodsreferences | Az ebben a szabályban kiállított jogcímben szereplő érték azt jelzi, hogy milyen típusú hitelesítést hajtottak végre az entitásnál. |
| Továbbítási jogcím – multifactorauthenticationinstant | A jogcím értéke határozza meg az időt (UTC), amikor a felhasználó legutóbb több faktoros hitelesítést hajtott végre. |
| Továbbítási jogcím – AlternateLoginID | Ez a szabály kiadja a AlternateLoginID jogcímet, ha a hitelesítés alternatív bejelentkezési AZONOSÍTÓval lett elvégezve. |

> [!NOTE]
> A probléma UPN-és ImmutableId vonatkozó jogcím-szabályai eltérőek lehetnek, ha Azure AD Connect konfigurációban nem alapértelmezett választást használ

## <a name="restore-issuance-transform-rules"></a>Kiállítási átalakítási szabályok visszaállítása

Azure AD Connect a 1.1.873.0 vagy újabb verziója biztonsági másolatot készít az Azure AD megbízhatósági beállításairól, amikor frissítést végez az Azure AD megbízhatósági beállításaiban. Az Azure AD-megbízhatósági beállítások biztonsági mentése a következő helyen történik: **%ProgramData%\AADConnect\ADFS**. A fájl neve a következő formátumú: AadTrust &lt; &gt; - &lt; &gt; . txt, például: AadTrust-20180710-150216.txt

![Képernyőkép az Azure AD-beli megbízható biztonsági mentésről](./media/how-to-connect-azure-ad-trust/backup.png)

A kiadás átalakítási szabályait a javasolt lépések alapján állíthatja vissza

1. A Kiszolgálókezelő AD FS felügyeleti felhasználói felületének megnyitása
2. Nyissa meg az Azure AD-megbízhatósági tulajdonságokat **AD FS &gt; függő entitás megbízhatóságai &gt; Microsoft Office 365 Identity platform &gt; szerkesztési jogcímek kiállítási szabályzata**
3. Kattintson a **szabály hozzáadása** elemre.
4. A jogcím szabály sablonjában válassza a jogcímek küldése egyéni szabállyal elemet, és kattintson a **tovább** gombra.
5. Másolja a jogcím-szabály nevét a biztonságimásolat-fájlból, és illessze be a mezőbe a **jogcím-szabály nevében** .
6. Másolja a jogcím szabályt a biztonságimásolat-fájlból az **Egyéni szabály** szövegmezőbe, majd kattintson a **Befejezés** gombra.

> [!NOTE]
> Győződjön meg arról, hogy a további szabályok nem ütköznek a Azure AD Connect által konfigurált szabályokkal.

## <a name="next-steps"></a>Következő lépések
* [Active Directory összevonási szolgáltatások (AD FS) kezelése és testreszabása Azure AD Connect használatával](how-to-connect-fed-management.md)
