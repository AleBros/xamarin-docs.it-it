---
title: Xamarin.Forms Accessibilità
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: ''
ms.openlocfilehash: 7ac8b305ae09e005013aea9f83fb4a3e4740f2b2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129807"
---
# <a name="xamarinforms-accessibility"></a>Xamarin.Forms Accessibilità

_La creazione di un'applicazione accessibile garantisce che l'applicazione possa essere usata da persone che si avvicinano all'interfaccia utente con una gamma di esigenze ed esperienze diverse._

Per rendere Xamarin.Forms accessibile un'applicazione, è possibile considerare il layout e la progettazione di molti elementi dell'interfaccia utente. Per le linee guida sugli aspetti da considerare, vedere l'[elenco di controllo per l'accessibilità](~/cross-platform/app-fundamentals/accessibility.md). Molti problemi di accessibilità, ad esempio i tipi di carattere di grandi dimensioni, e le impostazioni di colore e contrasto appropriate possono essere già risolti dalle Xamarin.Forms API

Le guide [Accessibilità in Android](~/android/app-fundamentals/accessibility.md) e [Accessibilità in iOS](~/ios/app-fundamentals/accessibility.md) includono dettagli delle API native disponibili in Xamarin, mentre la [guida all'accessibilità UWP in MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) illustra l'approccio nativo in tale piattaforma. Queste API vengono usate per implementare completamente le applicazioni accessibili in ogni piattaforma.

Xamarin.FormsAttualmente non include il supporto *incorporato* per tutte le API di accessibilità disponibili in ognuna delle piattaforme sottostanti. Supporta invece l'impostazione di proprietà di automazione in elementi dell'interfaccia utente per utilità per la lettura dello schermo e strumenti di assistenza alla navigazione, che sono tra i componenti più importanti nella costruzione di applicazioni accessibili. Per altre informazioni, vedere [Proprietà di automazione](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Xamarin.Formsle applicazioni possono anche avere l'ordine di tabulazione dei controlli specificati, per migliorare l'usabilità e l'accessibilità. Per altre informazioni, vedere [Accessibilità tramite tastiera](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Altre API per l'accessibilità (ad esempio [PostNotification in iOS](~/ios/app-fundamentals/accessibility.md)) possono essere più adatte a un'implementazione [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) o [Renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Questi argomenti non sono trattati nella presente guida.

## <a name="testing-accessibility"></a>Test dell'accessibilità

Xamarin.Formsle applicazioni sono in genere destinate a più piattaforme, il che significa testare le funzionalità di accessibilità in base alla piattaforma. Per informazioni su come eseguire il test dell'accessibilità in ogni piattaforma, visitare i collegamenti seguenti:

- [**Test in iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Test in Android**](~/android/app-fundamentals/accessibility.md)
- [**Visualizzatore ambito accessibilità di Windows (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Proprietà di automazione](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Accessibilità tastiera](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
