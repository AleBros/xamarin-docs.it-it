---
title: Uso di gruppi di App in Xamarin watchOS
description: Questo documento descrive i gruppi di app e il relativo utilizzo in un'applicazione watchOS. Illustra come configurare un gruppo di app, provisioning requisiti, considerazioni entitlements. plist e la distribuzione.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 78f6c03f73f0e4d8a74f826dd7bc25bbe325d545
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61411706"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Uso di gruppi di App in Xamarin watchOS


Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

- Apple Watch [impostazioni](~/ios/watchos/app-fundamentals/settings.md).
- Shared [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults).
- Shared [file](~/ios/watchos/app-fundamentals/parent-app.md#files).

## <a name="configure-an-app-group"></a>Configurare un gruppo di App

Il percorso condiviso viene configurato usando un [gruppo di App](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), in cui viene configurato il **certificati, identificatori e profili** sezione [iOS Dev Center](https://developer.apple.com/devcenter/ios/). Questo valore è necessario fare riferimento anche in ogni progetto **entitlements. plist**.

### <a name="provisioning"></a>Provisioning

Il gruppo di app avrà un identificatore, che in genere è l'ID Bundle con una `group.` prefisso. Ad esempio, potremmo usiamo l'ID Bundle `com.xamarin.WatchSettings` e il gruppo di app `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Usare l'ID Bundle com.xamarin.WatchSettings e group.com.xamarin.WatchSettings di gruppo di app")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Oltre a configurare il profilo di provisioning, **abilitare i gruppi di App** nel **entitlements. plist** e immettere l'ID si è scelto:

[![](app-groups-images/entitlements-sml.png "Configurare il file plist e immettere l'ID")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>Distribuzione

Verificare che si configura il gruppo di App in modo corretto le [distribuzione](~/ios/watchos/deploy-test/index.md#App_Groups) il provisioning.


Per altre informazioni, vedere la [le funzionalità di gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.


## <a name="related-links"></a>Collegamenti correlati

- [Apple condivisione dei dati con l'App che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Documentazione di gruppo di App di Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
