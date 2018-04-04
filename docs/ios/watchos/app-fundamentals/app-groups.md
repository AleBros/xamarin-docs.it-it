---
title: Utilizzo dei gruppi di App
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 341f6b4f5049a39817f2923901472f35b254bead
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-app-groups"></a>Utilizzo dei gruppi di App


Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

- Apple Watch [impostazioni](~/ios/watchos/app-fundamentals/settings.md).
- Condiviso [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults).
- Condiviso [file](~/ios/watchos/app-fundamentals/parent-app.md#files).

## <a name="configure-an-app-group"></a>Configurare un gruppo di App

La posizione condivisa viene configurata usando un [gruppo App](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), nel quale viene configurato il **certificati, gli identificatori e i profili** sezione [iOS Dev Center](https://developer.apple.com/devcenter/ios/). Questo valore è necessario fare riferimento anche in ogni progetto **Entitlements.plist**.

### <a name="provisioning"></a>Provisioning

Il gruppo dell'applicazione sarà necessario un identificatore, che in genere corrisponde all'ID di pacchetto con un `group.` prefisso. Ad esempio, è possibile usare l'ID Bundle `com.xamarin.WatchSettings` e il gruppo dell'applicazione `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Utilizzare il com.xamarin.WatchSettings ID Bundle e il group.com.xamarin.WatchSettings gruppo app")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Nonché la configurazione del profilo di provisioning, **abilitare gruppi di App** nel **Entitlements.plist** e immettere l'ID scelta:

[![](app-groups-images/entitlements-sml.png "Configurare il plist e immettere l'ID")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>Distribuzione

Assicurarsi di configurare il gruppo dell'applicazione in modo corretto il [distribuzione](~/ios/watchos/deploy-test/index.md#App_Groups) provisioning.


Per ulteriori informazioni, vedere il [le funzionalità delle App gruppo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.


## <a name="related-links"></a>Collegamenti correlati

- [Apple condivisione dei dati con l'App che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Documento di gruppo dell'applicazione di Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
