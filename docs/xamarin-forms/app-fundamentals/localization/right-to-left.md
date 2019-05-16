---
title: Localizzazione da destra a sinistra
description: La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra nelle applicazioni Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: e74231133c0a52ba0fe4e5589f09aab15e1b9924
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978610"
---
# <a name="right-to-left-localization"></a>Localizzazione da destra a sinistra

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)

_La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra nelle applicazioni Xamarin.Forms._

> [!NOTE]
> La localizzazione da destra a sinistra richiede l'uso di iOS 9 o versione successiva e l'API 17 o versione successiva in Android.

La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. Alcuni lingue, come l'arabo e l'ebraico, richiedono che gli elementi dell'interfaccia utente vengano disposti da destra a sinistra. A questo scopo, è possibile impostare la proprietà [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection). Questa proprietà ottiene o imposta la direzione nella quale disporre gli elementi dell'interfaccia utente all'interno di qualsiasi elemento padre che ne controlla il layout e deve essere impostata su uno dei valori di enumerazione [`FlowDirection`](xref:Xamarin.Forms.FlowDirection):

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

L'impostazione della proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) per un elemento su [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) determina, da un punto di vista generale, l'allineamento a destra, l'ordine di lettura da destra a sinistra e il layout del controllo da destra a sinistra:

[![TodoItemPage in arabo con direzione di flusso da destra a sinistra](rtl-images/TodoItemPage-Arabic.png "TodoItemPage in arabo con direzione di flusso da destra a sinistra")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage in arabo con direzione di flusso da destra a sinistra")

> [!TIP]
> Si consiglia di impostare la proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) solo nel layout iniziale. La modifica di questo valore in fase di esecuzione genera un laborioso processo di elaborazione del layout che influirà sulle prestazioni.

Il valore predefinito della proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) di un elemento senza elemento padre è [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight), mentre il valore predefinito `FlowDirection` di un elemento con un elemento padre è [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Pertanto, un elemento eredita il valore della proprietà `FlowDirection` dal relativo elemento padre nella struttura ad albero visuale e qualsiasi elemento può sostituire il valore ereditato dal relativo elemento padre.

> [!TIP]
> Durante la localizzazione di un'app per le lingue con flusso da destra a sinistra, impostare la proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) in un layout di pagina o radice. In questo modo, tutti gli elementi contenuti all'interno del layout di pagina o radice risponderanno in modo appropriato alla direzione del flusso.

## <a name="respecting-device-flow-direction"></a>Rispetto della direzione del flusso di un dispositivo

Il rispetto della direzione del flusso del dispositivo in funzione della lingua e dell'area geografica selezionata è una scelta esplicita che deve operare lo sviluppatore e, pertanto, non avviene automaticamente. Può essere ottenuto impostando la proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) in un layout di pagina o radice sul valore `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection):

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Tutti gli elementi figlio del layout di pagina o radice erediteranno quindi, per impostazione predefinita, il valore [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection).

## <a name="platform-setup"></a>Impostazione della piattaforma

Per abilitare le impostazioni locali da destra a sinistra, è necessario prevedere un'impostazione specifica della piattaforma.

### <a name="ios"></a>iOS

Le impostazioni locali necessarie per la direzione da destra a sinistra devono essere aggiunte come lingua supportata agli elementi matrice della chiave `CFBundleLocalizations` in **Info.plist**. L'esempio seguente mostra l'arabo aggiunto alla matrice della chiave `CFBundleLocalizations`:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Lingue supportate da Info.plist](rtl-images/ios-locales.png "Lingue supportate da Info.plist")

Per altre informazioni, vedere [Nozioni fondamentali di localizzazione in iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

La localizzazione da destra a sinistra può essere testata cambiando la lingua e l'area geografica nel dispositivo o simulatore e scegliendo le impostazioni locali per la direzione da destra a sinistra specificate in **Info.plist**.

> [!WARNING]
> Si noti che quando si cambia la lingua e l'area geografica scegliendo delle impostazioni locali per la direzione da destra a sinistra su iOS, qualsiasi visualizzazione [`DatePicker`](xref:Xamarin.Forms.DatePicker) genererà un'eccezione se le risorse necessarie per le impostazioni locali non sono state incluse. Ad esempio, durante il test di un'app in arabo con un `DatePicker`, verificare che **mideast** sia selezionato nella sezione **Internazionalizzazione** del riquadro **Compilazione iOS**.

### <a name="android"></a>Android

Il file **AndroidManifest.xml** dell'app deve essere aggiornato in modo che il nodo `<uses-sdk>` imposti l'attributo `android:minSdkVersion` su 17 e il nodo `<application>` imposti l'attributo `android:supportsRtl` su `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

La localizzazione da destra a sinistra può essere testata impostando una lingua con direzione da destra a sinistra nel dispositivo o emulatore oppure abilitando **Force RTL layout direction** (Forza direzione layout da destra a sinistra) nelle **Impostazioni > Opzioni per sviluppatori**.

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Le risorse della lingua necessarie devono essere specificate nel nodo `<Resources>` del file **Package.appxmanifest**. L'esempio seguente mostra l'arabo aggiunto al nodo `<Resources>`:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Inoltre, la piattaforma UWP richiede che le impostazioni cultura predefinite dell'app siano definite esplicitamente nella libreria .NET Standard. Questa operazione può essere eseguita impostando l'attributo `NeutralResourcesLanguage` nella classe `AssemblyInfo.cs`, o in un'altra classe, sulle impostazioni cultura predefinite:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

La localizzazione da destra a sinistra può essere testata cambiando la lingua e l'area geografica nel dispositivo e scegliendo le impostazioni locali per la direzione da destra a sinistra appropriate.

## <a name="limitations"></a>Limitazioni

La localizzazione da destra a sinistra di Xamarin.Forms presenta attualmente alcune limitazioni:

- La posizione del pulsante [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), la posizione degli elementi della barra degli strumenti e l'animazione della transizione sono controllate dalle impostazioni locali del dispositivo anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- La direzione di scorrimento rapido di [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) non si inverte.
- Il contenuto visivo di [`Image`](xref:Xamarin.Forms.Image) non si capovolge.
- L'orientamento di [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) e [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) è controllato dalle impostazioni locali del dispositivo, anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Il contenuto di [`WebView`](xref:Xamarin.Forms.WebView) non rispetta la proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- È necessario aggiungere una proprietà `TextDirection` per controllare l'allineamento del testo.

### <a name="ios"></a>iOS

- L'orientamento di [`Stepper`](xref:Xamarin.Forms.Stepper) è controllato dalle impostazioni locali del dispositivo, anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- L'allineamento del testo di [`EntryCell`](xref:Xamarin.Forms.EntryCell) è controllato dalle impostazioni locali del dispositivo, anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- I movimenti e l'allineamento di [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) non vengono invertiti.

### <a name="android"></a>Android

- L'orientamento di [`SearchBar`](xref:Xamarin.Forms.SearchBar) è controllato dalle impostazioni locali del dispositivo, anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Il posizionamento di [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) è controllato dalle impostazioni locali del dispositivo, anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

### <a name="uwp"></a>UWP

- L'allineamento del testo di [`Editor`](xref:Xamarin.Forms.Editor) è controllato dalle impostazioni locali del dispositivo, anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- La proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) non viene ereditata dagli elementi figlio di [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage).
- L'allineamento del testo di [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) è controllato dalle impostazioni locali del dispositivo, anziché dalla proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Supporto delle lingue da destra a sinistra con Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 Right-to-Left Support video** (Supporto da destra a sinistra di Xamarin.Forms 3.0 - video)

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio TodoLocalizedRTL](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
