---
title: Accessibilità di xamarin. Forms
description: Creazione di un'applicazione accessibile assicura che l'applicazione sia utilizzabile da persone che l'interfaccia utente con una gamma di esigenze e le esperienze di approccio.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: ac0ffbdce6b0c55e8ad9d774d80e3d9b8bf84089
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116446"
---
# <a name="xamarinforms-accessibility"></a>Accessibilità di xamarin. Forms

_Creazione di un'applicazione accessibile assicura che l'applicazione sia utilizzabile da persone che l'interfaccia utente con una gamma di esigenze e le esperienze di approccio._

Rendere accessibile un'applicazione xamarin. Forms significa pensare il layout e progettazione di molti elementi dell'interfaccia utente. Per linee guida sugli aspetti da considerare, vedere la [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md). È possibile risolvere diversi problemi di accessibilità, ad esempio i tipi di carattere di grandi dimensioni e le impostazioni di colore e il contrasto appropriate già Xamarin.Forms APIs.

Il [Android accessibilità](~/android/app-fundamentals/accessibility.md) e [iOS accessibilità](~/ios/app-fundamentals/accessibility.md) Guide contengono i dettagli delle API native esposte da Xamarin e il [Guida accessibilità UWP su MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) spiega il approccio nativo nella piattaforma. Queste API vengono usate per l'implementazione completa di applicazioni accessibili per ogni piattaforma.

Xamarin. Forms non ha attualmente alcuna *incorporati* il supporto per tutte le API disponibili in ognuna delle piattaforme sottostanti di accessibilità. Tuttavia, supporta impostazione delle proprietà di automazione in elementi dell'interfaccia utente per supportare screen reader e navigazione strumenti di assistenza, vale a dire una delle parti più importanti della compilazione di applicazioni accessibili. Per altre informazioni, vedere [le proprietà di automazione](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Le applicazioni xamarin. Forms possono avere anche l'ordine di tabulazione dei controlli specificata. Per altre informazioni, vedere [navigazione tramite tastiera](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Le API di accessibilità (ad esempio [PostNotification in iOS](~/ios/app-fundamentals/accessibility.md)) potrebbe essere più adatto a un [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) o [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) implementazione. Questi non sono trattati in questa Guida.

## <a name="testing-accessibility"></a>Verifica dell'accessibilità

Le applicazioni xamarin. Forms in genere più piattaforme di destinazione, ovvero le funzionalità di accessibilità a seconda della piattaforma di test. Usare i collegamenti seguenti per informazioni su come testare l'accessibilità in ogni piattaforma:

- [**iOS test**](~/ios/app-fundamentals/accessibility.md)
- [**Test di Android**](~/android/app-fundamentals/accessibility.md)
- [**AccScope Windows (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Proprietà di automazione](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Accessibilità dalla tastiera](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)
