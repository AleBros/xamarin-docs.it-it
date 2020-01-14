---
title: Uso dei gruppi di app watchos in Xamarin
description: Questo documento descrive i gruppi di app e il relativo uso in un'applicazione watchos. Viene descritto come configurare un gruppo di app, i requisiti di provisioning, i diritti e le considerazioni su plist e la distribuzione.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: e117fce77e9cdc8d9e9dc8b9ed7b3aa22eca4e39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001705"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Uso dei gruppi di app watchos in Xamarin

Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

- [Impostazioni](~/ios/watchos/app-fundamentals/settings.md)Apple Watch.
- [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)condiviso.
- [File](~/ios/watchos/app-fundamentals/parent-app.md#files)condivisi.

## <a name="configure-an-app-group"></a>Configurare un gruppo di app

Il percorso condiviso viene configurato usando un [gruppo di app](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), configurato nella sezione **certificati, identificatori & profili** in [iOS Dev Center](https://developer.apple.com/devcenter/ios/). È anche necessario fare riferimento a questo valore nel file **titles. plist**di ogni progetto.

### <a name="provisioning"></a>Provisioning

Il gruppo di app avrà un identificatore, che in genere corrisponde all'ID bundle con un prefisso `group.`. Ad esempio, è possibile usare l'ID bundle `com.xamarin.WatchSettings` e il gruppo di app `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Use the Bundle ID com.xamarin.WatchSettings and the app group   group.com.xamarin.WatchSettings")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Oltre a configurare il profilo di provisioning, **abilitare i gruppi di app** nel file **titles. plist** e immettere l'ID scelto:

[![](app-groups-images/entitlements-sml.png "Configure the plist and enter the ID")](app-groups-images/entitlements.png#lightbox)

### <a name="deployment"></a>Distribuzione

Assicurarsi di configurare correttamente il gruppo di app nel provisioning della [distribuzione](~/ios/watchos/deploy-test/index.md#App_Groups) .

Per ulteriori informazioni, vedere la documentazione relativa alle [funzionalità del gruppo di app](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Apple condivide i dati con l'app che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [DOC del gruppo di app di Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
