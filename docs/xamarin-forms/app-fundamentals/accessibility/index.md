---
title: Accessibilità
description: Compilazione di un'applicazione accessibile assicura che l'applicazione sia utilizzabile da persone che approccio dell'interfaccia utente con una gamma di esigenze ed esperienze.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: e4fb151b9664df7236d2c22ed54db09bf7bc65b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility"></a>Accessibilità

_Compilazione di un'applicazione accessibile assicura che l'applicazione sia utilizzabile da persone che approccio dell'interfaccia utente con una gamma di esigenze ed esperienze._

Rendere accessibile un'applicazione di xamarin. Forms significa pensare di layout e la progettazione di molti elementi dell'interfaccia utente. Per indicazioni sugli aspetti da considerare, vedere il [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md). È possibile risolvere numerosi problemi di accessibilità, ad esempio i tipi di carattere di grandi dimensioni e le impostazioni di colore e contrasto appropriate già Xamarin.Forms APIs.

Il [accessibilità Android](~/android/app-fundamentals/accessibility.md) e [iOS accessibilità](~/ios/app-fundamentals/accessibility.md) Guide contengono i dettagli delle API native esposti da Xamarin e [Guida accessibilità UWP in MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) illustra il approccio nativo su questa piattaforma. Queste API vengono utilizzate per l'implementazione completa di applicazioni accessibili in ciascuna piattaforma.

Xamarin. Forms non dispone attualmente *incorporato* il supporto per le API disponibili in ognuna delle piattaforme sottostanti l'accessibilità. Tuttavia, supporta valori dell'impostazione di accessibilità sugli elementi di interfaccia utente per screen reader e navigazione assistenza degli strumenti di supporto, ovvero una delle parti più importanti di compilazione di applicazioni accessibili. Per ulteriori informazioni, vedere [valori dell'impostazione di accessibilità in elementi dell'interfaccia utente](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md).

Le API di accessibilità (ad esempio [PostNotification in iOS](~/ios/app-fundamentals/accessibility.md)) può essere adeguato a un [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) o [Renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) implementazione. Questi non sono illustrati in questa Guida.

## <a name="testing-accessibility"></a>Verifica dell'accessibilità

Applicazioni di xamarin. Forms in genere più piattaforme, ovvero le funzionalità di accessibilità in base alla piattaforma di test. Seguire i collegamenti seguenti per informazioni su come testare l'accessibilità in ogni piattaforma:

- [**iOS test**](~/ios/app-fundamentals/accessibility.md)
- [**Test di Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)


## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Impostazione dei valori di accessibilità per elementi dell'interfaccia utente](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md)
