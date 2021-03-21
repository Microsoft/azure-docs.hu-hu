---
title: Biztonsági áttekintés
description: Az Azure arc használatára képes kiszolgálók biztonsági információi.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: aa8653b783e7eb3e211b7514831604dd5642cfbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117027"
---
# <a name="azure-arc-for-servers-security-overview"></a>Az Azure arc for Servers biztonsági áttekintése

Ez a cikk ismerteti azokat a biztonsági konfigurációkat és szempontokat, amelyeket az Azure arc-kompatibilis kiszolgálók vállalaton belüli üzembe helyezése előtt ki kell értékelni.

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

Minden egyes Azure-beli arc-kiszolgáló felügyelt identitással rendelkezik egy Azure-előfizetésben található erőforráscsoport részeként, ez az identitás a helyszíni vagy más felhőalapú környezetet futtató kiszolgálót jelenti. Az erőforráshoz való hozzáférést szabványos [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md)szabályozza. A Azure Portal [**Access Control (iam)**](../../role-based-access-control/role-assignments-portal.md) lapján ellenőrizheti, hogy ki férhet hozzá az Azure arc-kompatibilis kiszolgálóhoz.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Azure arc-kompatibilis kiszolgáló hozzáférés-vezérlése" border="false" lightbox="./media/security-overview/access-control-page.png":::

Azok a felhasználók és alkalmazások, akik [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) vagy rendszergazda szerepkörhöz férnek hozzá az erőforráshoz, módosíthatják az erőforrást, beleértve a [bővítmények](manage-vm-extensions.md) telepítését vagy törlését a gépen. A bővítmények tartalmazhatnak olyan tetszőleges parancsfájlokat, amelyek egy emelt szintű környezetben futnak, ezért az Azure-erőforrás bármely közreműködőjét a kiszolgáló közvetett rendszergazdájaként kell figyelembe venni.

Az **Azure Connected Machine** bevezetési szerepköre elérhető az Azure-ban, és csak új arc-kompatibilis kiszolgálókat tud olvasni vagy létrehozni az Azure-ban. Nem használható a már regisztrált vagy a bővítményeket kezelő kiszolgálók törlésére. Ajánlott eljárásként Azt javasoljuk, hogy csak akkor rendelje hozzá ezt a szerepkört ahhoz a Azure Active Directory (Azure AD) szolgáltatáshoz, amelyet a gépek nagy léptékű előkészítéséhez használnak.

Az **Azure Connected machine erőforrás-rendszergazdai** szerepkör tagjaként a felhasználók elolvashatják, módosíthatják, áthelyezhetik és törölhetik a gépeket. Ez a szerepkör az arc-kompatibilis kiszolgálók felügyeletének támogatására szolgál, de az erőforráscsoport vagy az előfizetés nem rendelkezik más erőforrásokkal.

## <a name="agent-security-and-permissions"></a>Ügynök biztonsága és engedélyei

Az Azure Connected Machine Agent (azcmagent) Windows rendszeren való kezeléséhez a felhasználói fióknak a helyi Rendszergazdák csoport tagjának kell lennie. Linux rendszeren rendszergazdai jogosultságokkal kell rendelkeznie.

Az Azure-beli csatlakoztatott gépi ügynök három szolgáltatásból áll, amelyek a gépen futnak.

* A Hybrid Instance Metadata Service (himds) szolgáltatás felelős az arc összes alapvető funkciójának. Ez magában foglalja a szívverések Azure-ba küldését, a helyi példány metaadatainak szolgáltatását más alkalmazások számára, amelyekkel megismerheti a gép Azure-erőforrás-AZONOSÍTÓját, és beolvashatja az Azure AD-jogkivonatokat más Azure-szolgáltatásokban való hitelesítéshez. Ez a szolgáltatás nem rendszerjogosultságú virtuális szolgáltatásfiókként fut a Windowson, és a **himds** felhasználója a Linuxon.

* A vendég konfigurációs szolgáltatás (GCService) feladata a számítógép Azure Policyának kiértékelése.

* A vendég konfigurációs bővítmény szolgáltatás (ExtensionService) felelős a bővítmények (ügynökök, parancsfájlok vagy más szoftverek) telepítéséhez, frissítéséhez és törléséhez a gépen.

A vendég-konfiguráció és a bővítmények helyi rendszerként futnak a Windows rendszeren, és a Linux root-ként működnek.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Felügyelt identitás használata ív-kompatibilis kiszolgálókkal

Alapértelmezés szerint az arc által használt Azure Active Directory rendszerhez rendelt identitás csak az arc-kompatibilis kiszolgáló állapotának az Azure-ban való frissítésére használható. Például az *utolsó látható* szívverés állapota. Ha a kiszolgálón lévő alkalmazás a rendszerhez rendelt identitás használatával fér hozzá más Azure-szolgáltatásokhoz, további szerepköröket is hozzárendelhet az identitáshoz.

Míg a hibrid Instance Metadata Service a gépen futó bármely alkalmazásban elérhető, csak a jogosult alkalmazások igényelhetnek Azure AD-jogkivonatot a rendszerhez rendelt identitáshoz. A jogkivonat URI-azonosítójának első elérésére tett kísérlet során a szolgáltatás véletlenszerűen generált titkosítási blobot hoz létre a fájlrendszer azon helyén, amelyet csak megbízható hívók tudnak olvasni. A hívónak el kell olvasnia a fájlt (amely igazolja, hogy rendelkezik a megfelelő engedélyekkel), majd próbálja megismételni a kérelmet az engedélyezési fejléc fájljának tartalmával, hogy sikeresen beolvassa az Azure AD-tokent.

* Windows rendszeren a hívónak a helyi **rendszergazdák** csoport tagjának kell lennie, vagy a **hibrid ügynök bővítmény alkalmazások** csoportjába kell tartoznia a blob olvasásához.

* Linux rendszeren a hívónak a **himds** csoport tagjának kell lennie ahhoz, hogy beolvassa a blobot.

## <a name="using-disk-encryption"></a>A lemez titkosításának használata

Az Azure-beli csatlakoztatott gépi ügynök nyilvános kulcsú hitelesítést használ az Azure szolgáltatással való kommunikációhoz. Miután bevezette a kiszolgálót az Azure ív szolgáltatásba, a rendszer egy titkos kulcsot ment a lemezre, és akkor használja, amikor az ügynök kommunikál az Azure-ban. Ha ellopják, a titkos kulcs egy másik kiszolgálón is használható a szolgáltatással való kommunikációhoz, és úgy viselkedik, mintha az eredeti kiszolgáló lenne. Ebbe beletartozik a rendszerhez rendelt identitás és az identitáshoz hozzáférő erőforrások hozzáférésének beszerzése. A titkos kulcsfájl védett, hogy csak a **himds** fiók számára engedélyezze a hozzáférést az olvasáshoz. Az offline támadások megelőzése érdekében javasoljuk, hogy a kiszolgáló operációs rendszerének kötetén teljes lemezes titkosítást (például BitLocker, dm-crypt stb.) használjon.

## <a name="next-steps"></a>Következő lépések

Az arc-kompatibilis kiszolgálók több hibrid gépen való kiértékelése vagy engedélyezése előtt tekintse át a [csatlakoztatott gép ügynökének áttekintése című témakört](agent-overview.md) a követelmények megismeréséhez, az ügynök műszaki részleteihez és a telepítési módszerekhez.
