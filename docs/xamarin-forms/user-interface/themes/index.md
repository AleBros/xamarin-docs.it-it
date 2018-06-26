---
title: Temi di xamarin. Forms
description: Questo articolo descrive i temi di xamarin. Forms, che consentono di definire specifica l'aspetto visivo per le visualizzazioni standard.
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0f49eeba072d6aeb7ead40d5d56d4af9e9bf5e27
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245732"
---
# <a name="xamarinforms-themes"></a>Temi di xamarin. Forms

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

Xamarin. Forms temi sono stati annunciati in 2016 Evolve e sono disponibili come anteprima ai clienti di provare e fornire commenti e suggerimenti.

Un tema verrà aggiunto a un'applicazione di xamarin. Forms includendo il **Xamarin.Forms.Theme.Base** pacchetto Nuget, oltre a un pacchetto aggiuntivo che definisce un tema specifico (ad es. Xamarin.Forms.Theme.Light) o altrimenti un tema locale può essere definito per l'applicazione.

Vedere il [tema chiaro](light.md) e [tema scuro](dark.md) pagine per istruzioni su come aggiungerli a un'app o estrarre il [tema personalizzato di esempio](custom.md).

**Importante:** è necessario seguire anche i passaggi per [caricare gli assembly di tema (sotto)](#loadtheme) aggiungendo codice boilerplate per iOS `AppDelegate` e Android `MainActivity`. Questo verrà migliorato in una versione futura di anteprima.


## <a name="control-appearance"></a>Aspetto del controllo

Il [luce](light.md) e [scuro](dark.md) entrambi i temi definiscono un aspetto visivo specifico per i controlli standard. Dopo aver aggiunto un tema al dizionario di risorse dell'applicazione, verrà modificato l'aspetto dei controlli standard.

Il markup XAML seguente mostra alcuni controlli comuni:

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

Questi screenshot mostrano questi controlli con:

* Nessun tema
* Tema chiaro (solo piccole differenze per non avere alcun tema)
* Tema scuro

![](images/standard-none-sml.png "Controlli senza temi") ![](images/standard-light-sml.png "controlli con tema chiaro") ![](images/standard-dark-sml.png "controlli con tema scuro")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

Il `StyleClass` proprietà consente l'aspetto di una vista da modificare in base a una definizione di un tema.

Il [luce](light.md) e [scuro](dark.md) temi sia definiscono tre aspetti diversi per un `BoxView`: `HorizontalRule`, `Circle`, e `Rounded`. In questo markup vengono indicate tre diversi `BoxView`con classi di stile diverso applicato:

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Si esegue il rendering con chiaro e scuro come indicato di seguito:

![](images/boxview-light-sml.png "BoxView con StyleClass un tema chiaro") ![](images/boxview-dark-sml.png "BoxView con StyleClass un tema scuro")

<a name="builtin" />

## <a name="built-in-classes"></a>Classi predefinite

Oltre a automaticamente stile comune controlla la luce e temi scuri supportano attualmente le classi seguenti che possono essere applicate tramite l'impostazione di `StyleClass` su questi controlli:

**BoxView**

* HorizontalRule
* Cerchio
* Arrotondato

**Immagine**

* Cerchio
* Arrotondato
* Immagine di anteprima

**Pulsante**

* Impostazione predefinita
* Primario
* Riuscito
* Info
* Avviso
* Rischio
* Collegamento
* Piccola
* Grande

**Etichetta**

* Header
* Sottotitolo
* Body
* Collegamento
* Inverso


## <a name="troubleshooting"></a>Risoluzione dei problemi

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Non è stato possibile caricare file o l'assembly 'Xamarin.Forms.Theme.Light' o una delle relative dipendenze

Nella versione di anteprima, temi potrebbero non essere in grado di caricare in fase di esecuzione. Aggiungere il codice riportato di seguito nei progetti rilevanti per correggere l'errore.

**iOS**

Nel **appdelegate. cs** aggiungere le seguenti righe dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Nel **Mainactivity** aggiungere le seguenti righe dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>Collegamenti correlati

- [Esempio ThemesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
