---
title: Introduzione agli effetti
description: Effetti consentono controlli nativi in ciascuna piattaforma da personalizzare e vengono in genere utilizzati per lo stile piccole modifiche. In questo articolo viene fornita un'introduzione agli effetti, descrive il limite tra renderer personalizzati e gli effetti e viene descritta la classe PlatformEffect.
ms.topic: article
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b66f47ecb8f955f6558df6fff18af92a7a8b97cf
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-effects"></a>Introduzione agli effetti

_Effetti consentono controlli nativi in ciascuna piattaforma da personalizzare e vengono in genere utilizzati per lo stile piccole modifiche. In questo articolo viene fornita un'introduzione agli effetti, descrive il limite tra renderer personalizzati e gli effetti e viene descritta la classe PlatformEffect._

Xamarin. Forms [controlli, layout e pagine](~/xamarin-forms/user-interface/controls/index.md) presenta un'API comune per descrivere le interfacce utente per dispositivi mobili multipiattaforma. Ogni pagina, layout e il controllo viene eseguito il rendering in modo diverso in ogni piattaforma usando un `Renderer` classe che a sua volta crea un controllo nativo (corrispondente nella rappresentazione di xamarin. Forms), li dispone sullo schermo e aggiunge il comportamento specificato in condiviso codice.

Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Tuttavia, implementare una classe renderer personalizzato per eseguire una controllo semplice di personalizzazione è spesso una risposta pesante. Effetti semplificano questo processo, consentendo di controlli nativi in ciascuna piattaforma più facilmente da personalizzare.

Effetti vengono creati nei progetti specifici della piattaforma creando sottoclassi di `PlatformEffect` controllo e quindi gli effetti vengono utilizzati, associarle a un controllo appropriato in un progetto libreria di xamarin. Forms classe portabile (PCL) o libreria condivisa.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Perché usare un effetto su un Renderer personalizzato?

Effetti semplificano la personalizzazione di un controllo, sono riutilizzabili e possono essere parametrizzati per migliorare ulteriormente il riutilizzo.

Tutto ciò che può essere ottenuta con un effetto può essere ottenuta con un renderer personalizzato. Tuttavia, un renderer personalizzato offerta maggiore flessibilità e personalizzazione di effetti. Di seguito sono elencate le circostanze in cui si desidera scegliere un effetto su un renderer personalizzato:

- Quando si modificano le proprietà di un controllo specifico della piattaforma consente di ottenere il risultato desiderato, è consigliabile un effetto.
- Un renderer personalizzato è obbligatorio quando è necessario eseguire l'override di un controllo specifico della piattaforma.
- Un renderer personalizzato è obbligatorio quando è necessario sostituire il controllo specifico della piattaforma che implementa un controllo di xamarin. Forms.

## <a name="subclassing-the-platformeffect-class"></a>Creazione di una sottoclasse della classe PlatformEffect

La tabella seguente elenca lo spazio dei nomi per il `PlatformEffect` classe per ogni piattaforma e i tipi delle proprietà:

<table>
 <thead>
   <tr>
     <td><strong>Piattaforma</strong></td>
     <td><strong>Namespace</strong></td>
     <td><strong>contenitore</strong></td>
     <td><strong>Controllo</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td>iOS</a></td>
     <td>Xamarin.Forms.Platform.iOS</td>
     <td>UIView</td>
     <td>UIView</td>
   </tr>
   <tr>
     <td>Android</a></td>
     <td>Xamarin.Forms.Platform.Android</td>
     <td>ViewGroup</td>
     <td>Visualizza</td>
   </tr>
   <tr>
     <td>Windows Phone 8.1</a></td>
     <td>Xamarin.Forms.Platform.WinRT</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td>Piattaforma UWP (Universal Windows Platform)</a></td>
     <td>Xamarin.Forms.Platform.UWP</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
 </tbody>
</table>

Ogni specifico della piattaforma `PlatformEffect` classe espone le proprietà seguenti:

- `Container` : fa riferimento al controllo specifico della piattaforma in uso per implementare il layout.
- `Control` : fa riferimento al controllo specifico della piattaforma in uso per implementare il controllo di xamarin. Forms.
- `Element` : fa riferimento al controllo di xamarin. Forms che viene eseguito il rendering.

Effetti non dispone di informazioni sul tipo relative il contenitore, un controllo o un elemento che a cui sono collegate in quanto è possibile che siano collegati ad alcun elemento. Pertanto, quando un effetto è collegato a un elemento che non supporta deve adattarsi o genera un'eccezione. Tuttavia, il `Container`, `Control`, e `Element` proprietà possono eseguire il cast al tipo di implementazione. Per ulteriori informazioni su questi tipi vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Ogni specifico della piattaforma `PlatformEffect` classe espone i metodi seguenti, che devono essere sottoposto a override per implementare un effetto:

- [`OnAttached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnAttached()/) : viene chiamato quando un effetto è collegato a un controllo di xamarin. Forms. Una versione sottoposta a override di questo metodo, in ogni classe effetto Platform specifici, è possibile eseguire la personalizzazione del controllo, con la gestione delle eccezioni nel caso in cui l'effetto non può essere applicato al controllo specificato xamarin. Forms.
- [`OnDetached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnDetached()/) : viene chiamato quando un effetto viene disconnesso da un controllo di xamarin. Forms. Una versione sottoposta a override di questo metodo, in ogni classe effetto specifico della piattaforma, è possibile eseguire le operazioni di pulitura effetto, ad esempio la registrazione di un gestore eventi.

Inoltre, il `PlatformEffect` espone il [ `OnElementPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E.OnElementPropertyChanged/p/System.ComponentModel.PropertyChangedEventArgs/) metodo, che è anche possibile eseguire l'override. Questo metodo viene chiamato quando viene modificata una proprietà dell'elemento. Una versione sottoposta a override di questo metodo, in ogni classe effetto specifico della piattaforma, è possibile rispondere alle modifiche di proprietà associabile nel controllo xamarin. Forms. Un controllo per la proprietà che è stato modificato deve essere sempre effettuato, come la sostituzione può essere chiamata più volte.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
