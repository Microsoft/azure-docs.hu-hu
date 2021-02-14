---
title: A partner AZONOSÍTÓjának összekapcsolása a delegált erőforrásokra gyakorolt hatás nyomon követéséhez
description: Megtudhatja, hogyan rendelheti hozzá partner-AZONOSÍTÓját az Azure Lighthouse használatával kezelt felhasználói erőforrásokhoz kapcsolódó partneri kreditek (PEC) fogadásához.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372093"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>A partner AZONOSÍTÓjának összekapcsolása a delegált erőforrásokra gyakorolt hatás nyomon követéséhez 

Ha Ön a [Microsoft Partner Network](https://partner.microsoft.com/)tagja, összekapcsolhatja a Partner azonosítóját a delegált ügyfelek erőforrásainak kezeléséhez használt hitelesítő adatokkal, így a Microsoft azonosíthatja és felismerheti az Azure-ügyfelek sikerességét biztosító partnereket. Ez a hivatkozás azt is lehetővé teszi, hogy a [CSP (Cloud Solution Provider)](/partner-center/csp-overview) partnerei megkapják a [felügyelt szolgáltatásokra (PEC) vonatkozó partneri kreditet](/partner-center/partner-earned-credit) azon ügyfelek számára, akik [aláírták a Microsoft Customer szerződést (MCA)](/partner-center/confirm-customer-agreement) , és [Az Azure-csomag alá](/partner-center/azure-plan-get-started)tartoznak.

Az Azure Lighthouse-tevékenységek elismerésének megszerzéséhez az MPN-azonosítót legalább egy felhasználói fiókkal kell [összekapcsolni](../../cost-management-billing/manage/link-partner-id.md) a felügyeleti bérlőben, és biztosítania kell, hogy a társított fiók hozzáférjen a beérkező előfizetésekhez.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Partner-azonosító hozzárendelése új ügyfelek bevezetéséhez

A következő eljárással kapcsolhatja össze partneri AZONOSÍTÓját (és ha szükséges, a partner által létrehozott kreditet is engedélyezheti). A lépések elvégzéséhez ismernie kell az [MPN-Partner azonosítóját](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Ügyeljen arra, hogy a partnerprofilon szereplő **társított MPN-azonosítót** használja.

Az egyszerűség kedvéért javasoljuk, hogy hozzon létre egy egyszerű szolgáltatásnevet a bérlőben, csatolja a **társított MPN-azonosítóhoz**, majd minden ügyfél számára hozzáférést biztosítson a [PEC-re jogosult Azure beépített szerepkörhöz](/partner-center/azure-roles-perms-pec).

1. [Hozzon létre egy egyszerű szolgáltatásnév felhasználói fiókot](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) a felügyeleti bérlőben. Ebben a példában az egyszerű szolgáltatásnév fiókhoz tartozó name *Provider Automation-fiókot* fogjuk használni.
1. Az egyszerű szolgáltatás fiókjának használatával [csatolja a társított MPN-azonosítót](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) a felügyeleti bérlőben. Ezt csak egyszer kell elvégeznie.
1. Amikor [ARM-sablonokkal](onboard-customer.md) vagy [felügyelt szolgáltatás-ajánlatokkal](publish-managed-services-offers.md)végez egy ügyfelet, ügyeljen arra, hogy olyan hitelesítést tartalmazzon, amely tartalmazza a szolgáltató Automation-fiókját a [PEC használatára jogosult Azure beépített szerepkörrel](/partner-center/azure-roles-perms-pec)rendelkező felhasználóként.

A lépéseket követve minden Ön által kezelt ügyfél-bérlő társítva lesz a partner-AZONOSÍTÓhoz. A szolgáltatói Automation-fióknak nem kell hitelesítenie vagy végrehajtania semmilyen műveletet az ügyfél bérlője számára.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="A partner-azonosító összekapcsolási folyamatát bemutató ábra az Azure Lighthouse-val.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>A partner-azonosító hozzáadása a korábban bekészített ügyfelekhez

Ha már előkészített egy ügyfelet, előfordulhat, hogy nem kíván újabb központi telepítést végrehajtani a szolgáltatói Automation-fiók egyszerű telepítéséhez. Ehelyett összekapcsolhatja a **társított MPN-azonosítót** egy olyan felhasználói fiókkal, amelynek már van hozzáférése az ügyfél bérlője munkahelyéhez. Győződjön meg arról, hogy a fiók olyan [Azure beépített szerepkört kapott, amely jogosult a PEC](/partner-center/azure-roles-perms-pec)használatára.

Ha a fiók a [társított MPN-azonosítóhoz van társítva](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) a felügyeleti bérlőben, nyomon követheti az adott ügyfélre gyakorolt hatás felismerését.

## <a name="confirm-partner-earned-credit"></a>Partner által létrehozott kredit megerősítése

[A PEC részleteit a Azure Portalban tekintheti](/partner-center/partner-earned-credit-explanation#azure-cost-management) meg, és megerősítheti, hogy a PEC Milyen költségekkel részesült. Ne feledje, hogy a PEC csak azokra a CSP-ügyfelekre vonatkozik, akik aláírták az MCA-t, és az Azure-csomagban vannak.

Ha követte a fenti lépéseket, és nem látja a várt társítást, nyisson meg egy támogatási kérést a Azure Portal.

Használhatja a [partner Center SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) -t is, és SZŰRHETI a `rateOfPartnerEarnedCredit` PEC-ellenőrzés automatizálását az előfizetéshez.

## <a name="next-steps"></a>Következő lépések

- További információ a [Microsoft partner Networkról](/partner-center/mpn-overview).
- Útmutató [a PEC kiszámításához és kifizetéséhez](/partner-center/partner-earned-credit-explanation).
- Az [ügyfelek](onboard-customer.md) bevonása az Azure Lighthouse-ba.