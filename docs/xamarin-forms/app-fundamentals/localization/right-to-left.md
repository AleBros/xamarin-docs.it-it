---
title: ''
description: La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra alle Xamarin.Forms applicazioni.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 212674472706a36c66436a3955ab7b988f8e246b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137553"
---
# <a name="right-to-left-localization"></a>Localizzazione da destra a sinistra

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra alle Xamarin.Forms applicazioni._

> [!NOTE]
> La localizzazione da destra a sinistra richiede l'uso di iOS 9 o versione successiva e l'API 17 o versione successiva in Android.

La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. Alcuni lingue, come l'arabo e l'ebraico, richiedono che gli elementi dell'interfaccia utente vengano disposti da destra a sinistra. Questa operazione può essere eseguita impostando la [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà. Questa proprietà Ottiene o imposta la direzione di flusso degli elementi dell'interfaccia utente all'interno di qualsiasi elemento padre che ne controlla il layout e deve essere impostata su uno dei [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) valori di enumerazione:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Impostando la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) su su un elemento, in genere viene impostato l'allineamento a destra, l'ordine di lettura da destra a sinistra e il layout del controllo da destra a sinistra:

[![TodoItemPage in arabo con direzione di flusso da destra a sinistra](rtl-images/TodoItemPage-Arabic.png "TodoItemPage in arabo con direzione di flusso da destra a sinistra")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage in arabo con direzione di flusso da destra a sinistra")

> [!TIP]
> È necessario impostare la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà solo nel layout iniziale. La modifica di questo valore in fase di esecuzione genera un laborioso processo di elaborazione del layout che influirà sulle prestazioni.

Il valore predefinito della [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà per un elemento senza padre è [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight) , mentre l'impostazione predefinita `FlowDirection` per un elemento con un elemento padre è [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) . Pertanto, un elemento eredita il valore della proprietà `FlowDirection` dal relativo elemento padre nella struttura ad albero visuale e qualsiasi elemento può sostituire il valore ereditato dal relativo elemento padre.

> [!TIP]
> Quando si localizza un'app per le lingue da destra a sinistra, impostare la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà in una pagina o un layout radice. In questo modo, tutti gli elementi contenuti all'interno del layout di pagina o radice risponderanno in modo appropriato alla direzione del flusso.

## <a name="respecting-device-flow-direction"></a>Rispetto della direzione del flusso di un dispositivo

Il rispetto della direzione del flusso del dispositivo in funzione della lingua e dell'area geografica selezionata è una scelta esplicita che deve operare lo sviluppatore e, pertanto, non avviene automaticamente. È possibile ottenere questo risultato impostando la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà in una pagina o un layout radice sul `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valore:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Per impostazione predefinita, tutti gli elementi figlio della pagina o del layout radice ereditano il [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valore.

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

![Lingue supportate di info. plist](rtl-images/ios-locales.png "Lingue supportate di info. plist")

Per altre informazioni, vedere [Nozioni fondamentali di localizzazione in iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

La localizzazione da destra a sinistra può essere testata cambiando la lingua e l'area geografica nel dispositivo o simulatore e scegliendo le impostazioni locali per la direzione da destra a sinistra specificate in **Info.plist**.

> [!WARNING]
> Si noti che quando si modifica la lingua e l'area in impostazioni locali da destra a sinistra in iOS, eventuali [`DatePicker`](xref:Xamarin.Forms.DatePicker) visualizzazioni genereranno un'eccezione se non si includono le risorse necessarie per le impostazioni locali. Ad esempio, durante il test di un'app in arabo con un `DatePicker`, verificare che **mideast** sia selezionato nella sezione **Internazionalizzazione** del riquadro **Compilazione iOS**.

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

Xamarin.Formsla localizzazione da destra a sinistra presenta attualmente una serie di limitazioni:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)il percorso dei pulsanti, la posizione dell'elemento della barra degli strumenti e l'animazione della transizione sono controllati dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)la direzione di scorrimento non viene invertita.
- [`Image`](xref:Xamarin.Forms.Image)il contenuto visivo non viene invertito.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))e [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) l'orientamento sono controllati dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.
- [`WebView`](xref:Xamarin.Forms.WebView)il contenuto non rispetta la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.
- È necessario aggiungere una proprietà `TextDirection` per controllare l'allineamento del testo.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper)l'orientamento è controllato dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)l'allineamento del testo è controllato dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)i movimenti e l'allineamento non sono invertiti.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar)l'orientamento è controllato dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)la selezione host viene controllata dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.

### <a name="uwp"></a>Piattaforma UWP

- [`Editor`](xref:Xamarin.Forms.Editor)l'allineamento del testo è controllato dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)la proprietà non viene ereditata dagli [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) elementi figlio.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)l'allineamento del testo è controllato dalle impostazioni locali del dispositivo anziché dalla [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.

## <a name="force-right-to-left-layout"></a>Forza layout da destra a sinistra

Le applicazioni Novell. iOS e Novell. Android possono essere forzate a usare sempre un layout da destra a sinistra, indipendentemente dalle impostazioni del dispositivo, modificando i rispettivi progetti di piattaforma.

### <a name="ios"></a>iOS

Le applicazioni Novell. iOS possono essere forzate a usare sempre un layout da destra a sinistra modificando la classe **AppDelegate** come indicato di seguito:

1. Dichiarare la `IntPtr_objc_msgSend` funzione come prima riga della `AppDelegate` classe:

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. Chiamare la `IntPtr_objc_msgSend` funzione dal `FinishedLaunching` metodo, prima di restituire dal `FinshedLaunching` Metodo:

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

Questo approccio è utile per le applicazioni che richiedono sempre un layout da destra a sinistra e rimuove il requisito per impostare la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.

Per ulteriori informazioni sul `IntrPtr_objc_msgSend` metodo, vedere [selettori Objective-C in Novell. iOS](~/ios/internals/objective-c-selectors.md).

### <a name="android"></a>Android

Le applicazioni Novell. Android possono essere forzate a usare sempre un layout da destra a sinistra modificando la classe **MainActivity** in modo da includere la riga seguente:

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

Questo approccio è utile per le applicazioni che richiedono sempre un layout da destra a sinistra e rimuove il requisito per impostare la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) Proprietà.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Supporto delle lingue da destra a sinistra con Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Formsvideo di supporto da destra a sinistra 3,0**

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
