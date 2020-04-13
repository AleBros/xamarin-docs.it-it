---
title: Accessibilità di Xamarin.Forms
description: La creazione di un'applicazione accessibile garantisce che l'applicazione possa essere usata da persone che si avvicinano all'interfaccia utente con una gamma di esigenze ed esperienze diverse.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 2aaa61400d3775c4d622b805d24ff0b338017de5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "66354039"
---
# <a name="xamarinforms-accessibility"></a>Accessibilità di Xamarin.Forms

_La creazione di un'applicazione accessibile garantisce che l'applicazione possa essere usata da persone che si avvicinano all'interfaccia utente con una gamma di esigenze ed esperienze diverse._

Rendere accessibile un'applicazione Xamarin.Forms significa progettare il layout e la struttura di molti elementi dell'interfaccia utente. Per le linee guida sugli aspetti da considerare, vedere l'[elenco di controllo per l'accessibilità](~/cross-platform/app-fundamentals/accessibility.md). Molti aspetti dell'accessibilità, ad esempio i tipi di carattere di grandi dimensioni e le impostazioni di colore e contrasto appropriate, sono gestibili direttamente tramite le API di Xamarin.Forms.

Le guide [Accessibilità in Android](~/android/app-fundamentals/accessibility.md) e [Accessibilità in iOS](~/ios/app-fundamentals/accessibility.md) includono dettagli delle API native disponibili in Xamarin, mentre la [guida all'accessibilità UWP in MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) illustra l'approccio nativo in tale piattaforma. Queste API vengono usate per implementare completamente le applicazioni accessibili in ogni piattaforma.

Xamarin.Forms non dispone attualmente di supporto *incorporato* per tutte le API di accessibilità disponibili in ogni piattaforma sottostante. Supporta invece l'impostazione di proprietà di automazione in elementi dell'interfaccia utente per utilità per la lettura dello schermo e strumenti di assistenza alla navigazione, che sono tra i componenti più importanti nella costruzione di applicazioni accessibili. Per altre informazioni, vedere [Proprietà di automazione](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Nelle applicazioni Xamarin.Forms è anche possibile specificare l'ordine di tabulazione dei controlli per migliorare usabilità e accessibilità. Per altre informazioni, vedere [Accessibilità tramite tastiera](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Altre API per l'accessibilità (ad esempio [PostNotification in iOS](~/ios/app-fundamentals/accessibility.md)) possono essere più adatte a un'implementazione [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) o [Renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Questi argomenti non sono trattati nella presente guida.

## <a name="testing-accessibility"></a>Test dell'accessibilità

Le applicazioni Xamarin.Forms in genere sono destinate a più piattaforme. Di conseguenza è necessario eseguire il test delle funzionalità di accessibilità per ogni piattaforma. Per informazioni su come eseguire il test dell'accessibilità in ogni piattaforma, visitare i collegamenti seguenti:

- [**Test in iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Test in Android**](~/android/app-fundamentals/accessibility.md)
- [**Visualizzatore ambito accessibilità di Windows (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Proprietà di automazione](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Accessibilità tramite tastiera](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
