---
title: Oggetto visivo xamarin. Forms
description: Questo articolo presenta xamarin. Forms visivo, che esegue il rendering di visualizzazioni in modo identico, o in gran parte identico in iOS e Android.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2018
ms.openlocfilehash: df2351e35817a6468fed236752eea8e5ad11024f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061886"
---
# <a name="xamarinforms-visual"></a>Oggetto visivo xamarin. Forms

![Anteprima](~/media/shared/preview.png)

_Questo articolo presenta xamarin. Forms visivo, che esegue il rendering di visualizzazioni in modo identico, o in gran parte identico in iOS e Android._

Molti sviluppatori desiderano creare applicazioni di xamarin. Forms con un aspetto identica, o in gran parte identici, in iOS e Android. Xamarin. Forms. 4.0-pre1 include un meccanismo per includere altri renderer che implementano un aspetto visivo, con le applicazioni di consenso esplicito all'aspetto tramite un `Visual` proprietà:

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>    
```

I renderer che implementano l'aspetto visivo vengono quindi utilizzati per le visualizzazioni di renderer, anziché il renderer predefinito. In fase di selezione del renderer, il `Visual` proprietà della vista vengono ispezionate e inclusi nel processo di selezione del renderer. Inoltre, se il `Visual` proprietà viene modificata in fase di esecuzione, il renderer specificato viene ricreato insieme a eventuali elementi figlio.

> [!IMPORTANT]
> Il `Visual` proprietà definita nel `VisualElement` (classe), con le visualizzazioni che eredita il `Visual` valore della proprietà dai relativi oggetti padre. Pertanto, l'impostazione di `Visual` proprietà su un `ContentPage` assicura che eventuali visualizzazioni supportate nella pagina userà tale aspetto visivo. Inoltre, il `Visual` proprietà può essere sottoposto a override in una vista.

Xamarin. Forms. 4.0-pre1 include un aspetto visivo sperimentale basato alla progettazione dei materiali, con i renderer noti come i renderer di materiale. Renderer Material sono attualmente incluse per le viste seguenti in iOS e Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)

A livello funzionale, il renderer materiale non sono diversi per i renderer predefinito. Tuttavia, sono attualmente in fase sperimentale e può essere usati solo aggiungendo la riga seguente di codice per il `AppDelegate` classe in iOS o per il `MainActivity` classe in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("Visual_Experimental");
```

Inoltre, su iOS, il progetto di piattaforma deve avere il [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents/) pacchetto NuGet installato. In Android, Visual funziona solo con API 29, il progetto della piattaforma deve usare v28 delle librerie di supporto e impostare il tema di ereditare da un tema Material componenti oppure continuare a ereditare da un tema AppCompat durante l'aggiunta di alcuni nuovi attributi di tema a tema. Per altre informazioni, vedere [Guida introduttiva per Android con i componenti di materiale](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Le schermate seguenti illustrano un'interfaccia utente che include le quattro viste per le quali materiali renderer esistano, ma viene eseguito il rendering tramite i renderer predefinito:

[![Default renderer](visual-images/default-renderers.png "le viste usando il renderer predefinito")](visual-images/default-renderers-large.png#lightbox)

Le schermate seguenti illustrano la stessa interfaccia utente viene eseguito il rendering tramite i renderer di materiale:

[![I renderer materiali](visual-images/material-renderers.png "le viste usando il renderer di materiale")](visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Con i renderer materiali i controlli di rendering rimangono controlli nativi e di conseguenza è comunque presente le differenze di interfaccia utente tra piattaforme per le aree, ad esempio i tipi di carattere, ombreggiature, colori e l'elevazione dei privilegi.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
