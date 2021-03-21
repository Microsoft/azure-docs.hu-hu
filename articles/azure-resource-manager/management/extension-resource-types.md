---
title: Bővítményerőforrás-típusok
description: Felsorolja az Azure-erőforrástípusok használatát a más erőforrástípusok képességeinek bővítésére.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658451"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Más erőforrások képességeit kiterjesztő erőforrástípusok

A kiterjesztési erőforrás olyan erőforrás, amely egy másik erőforrás képességeihez járul hozzá. Például az erőforrás-zárolás egy bővítmény-erőforrás. Egy erőforrás-zárolást egy másik erőforráshoz alkalmazva megakadályozhatja a törlését vagy módosítását. Nincs értelme létrehozni erőforrás-zárolást önmagában. A bővítmény erőforrásait mindig egy másik erőforrásra alkalmazza a rendszer.

## <a name="microsoftadvisor"></a>Microsoft. Advisor

- Microsoft. Advisor/konfigurációk
- Microsoft. Advisor/javaslatok
- Microsoft. Advisor/tiltások

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

- Microsoft. AlertsManagement/riasztások

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft. Authorization/denyAssignments
- Microsoft. engedélyezés/zárolások
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions

## <a name="microsoftautomanage"></a>Microsoft. automanage

- Microsoft. automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft. számlázás

- Microsoft. Billing/billingPeriods
- Microsoft. Billing/billingPermissions
- Microsoft. Billing/billingRoleAssignments
- Microsoft. Billing/billingRoleDefinitions
- Microsoft. Billing/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

- Microsoft. Blueprint/blueprintAssignments
- Microsoft. Blueprint/tervrajzok

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

- Microsoft. fogyasztás/AggregatedCost
- Microsoft. fogyasztás/egyenlegek
- Microsoft. fogyasztás/költségvetés
- Microsoft. fogyasztás/díjak
- Microsoft. fogyasztás/CostTags
- Microsoft. felhasználás/kreditek
- Microsoft. felhasználás/események
- Microsoft. felhasználás/előrejelzések
- Microsoft. fogyasztás/tételek
- Microsoft. fogyasztás/piactér
- Microsoft. fogyasztás/Pricesheets
- Microsoft. fogyasztás/termékek
- Microsoft. fogyasztás/ReservationDetails
- Microsoft. fogyasztás/ReservationRecommendationDetails
- Microsoft. fogyasztás/ReservationRecommendations
- Microsoft. fogyasztás/ReservationSummaries
- Microsoft. fogyasztás/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

- Microsoft. ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

- Microsoft. CostManagement/riasztások
- Microsoft. CostManagement/költségvetés
- Microsoft. CostManagement/méretek
- Microsoft. CostManagement/export
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/előrejelzés
- Microsoft. CostManagement/bepillantások
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/jelentések
- Microsoft. CostManagement/nézetek

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

- Microsoft. CustomProviders/társítások

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/szoftver

## <a name="microsoftinsights"></a>Microsoft. bepillantások

- Microsoft. bepillantások/alapterv
- Microsoft. bepillantások/dataCollectionRuleAssociations
- Microsoft. bepillantások/diagnosticSettings
- Microsoft. bepillantások/diagnosticSettingsCategories
- Microsoft. bepillantások/eventtypes
- Microsoft. bepillantások/extendedDiagnosticSettings
- Microsoft. bepillantások/guestDiagnosticSettingsAssociation
- Microsoft. bepillantások/logDefinitions
- Microsoft. elemzések/naplók
- Microsoft. bepillantások/metricbaselines
- Microsoft. bepillantások/metricDefinitions
- Microsoft. bepillantások/metricNamespaces
- Microsoft. bepillantások/mérőszámok
- Microsoft. bepillantások/myWorkbooks
- Microsoft. bepillantások/topológia
- Microsoft. bepillantások/tranzakciók

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

- Microsoft. KubernetesConfiguration/bővítmények
- Microsoft. KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

- Microsoft. ManagedIdentity/identitások

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

- Microsoft. OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

- Microsoft. OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

- Microsoft. PolicyInsights/igazolások
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/szervizelések

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

- Microsoft. Recoveryservices szolgáltatónál/backupProtectedItems
- Microsoft. Recoveryservices szolgáltatónál/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/események
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/értesítések

## <a name="microsoftresources"></a>Microsoft. Resources

- Microsoft. Resources/hivatkozások
- Microsoft. Resources/Tags

## <a name="microsoftsecurity"></a>Microsoft. Security

- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/értékelések
- Microsoft. Security/megfelelőség
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/eszközök
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

- Microsoft. SecurityInsights/összesítések
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/könyvjelzők
- Microsoft. SecurityInsights/esetek
- Microsoft. SecurityInsights/dataConnectors
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/entitások
- Microsoft. SecurityInsights/incidensek
- Microsoft. SecurityInsights/beállítások
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/listák

## <a name="microsoftserialconsoleppe"></a>Microsoft. SerialConsole. PPE

- Microsoft. SerialConsole. PPE/serialPorts

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

- Microsoft. SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>Microsoft. support

- Microsoft. support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

- Microsoft. WorkloadMonitor/összetevők
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/figyelők
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Következő lépések

- Ha egy Azure Resource Manager-sablonban szeretné lekérni a bővítmény erőforrásának erőforrás-AZONOSÍTÓját, használja a [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- A bővítmény-erőforrások sablonban való létrehozásával kapcsolatos példát a [Event Grid esemény-előfizetések](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)című témakörben talál.
