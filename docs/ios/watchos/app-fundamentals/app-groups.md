---
title: Uso dei gruppi di app watchos in Novell
description: Questo documento descrive i gruppi di app e il relativo uso in un'applicazione watchos. Viene descritto come configurare un gruppo di app, i requisiti di provisioning, i diritti e le considerazioni su plist e la distribuzione.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: fec6cef22ddbc47aca0027aa026e0524b38bec4e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768068"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Uso dei gruppi di app watchos in Novell

Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

- [Impostazioni](~/ios/watchos/app-fundamentals/settings.md)Apple Watch.
- [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)condiviso.
- [File](~/ios/watchos/app-fundamentals/parent-app.md#files)condivisi.

## <a name="configure-an-app-group"></a>Configurare un gruppo di app

Il percorso condiviso viene configurato usando un [gruppo di app](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), configurato nella sezione **certificati, identificatori & profili** in [iOS Dev Center](https://developer.apple.com/devcenter/ios/). È anche necessario fare riferimento a questo valore nel file **titles. plist**di ogni progetto.

### <a name="provisioning"></a>Provisioning

Il gruppo di app avrà un identificatore, che corrisponde in genere all'ID del bundle `group.` con un prefisso. Ad esempio, è possibile usare l'ID `com.xamarin.WatchSettings` bundle e il gruppo `group.com.xamarin.WatchSettings`di app.

[![](app-groups-images/app-group-sml.png "Usare l'ID bundle com. Novell. WatchSettings e il gruppo app Group. com. Novell. WatchSettings")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Oltre a configurare il profilo di provisioning, **abilitare i gruppi di app** nel file **titles. plist** e immettere l'ID scelto:

[![](app-groups-images/entitlements-sml.png "Configurare il file plist e immettere l'ID")](app-groups-images/entitlements.png#lightbox)

### <a name="deployment"></a>Distribuzione

Assicurarsi di configurare correttamente il gruppo di app nel provisioning della [distribuzione](~/ios/watchos/deploy-test/index.md#App_Groups) .

Per altre informazioni, vedere la [le funzionalità di gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.

## <a name="related-links"></a>Collegamenti correlati

- [Apple condivisione dei dati con l'App che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [DOC del gruppo di app di Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
