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
ms.openlocfilehash: a6eb3167fd0880984a74245c4653642ea3979354
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "72678833"
---
# <a name="right-to-left-localization"></a>Localizzazione da destra a sinistra

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra nelle applicazioni Xamarin.Forms._

> [!NOTE]
> La localizzazione da destra a sinistra richiede l'uso di iOS 9 o versione successiva e l'API 17 o versione successiva in Android.

La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. Alcuni lingue, come l'arabo e l'ebraico, richiedono che gli elementi dell'interfaccia utente vengano disposti da destra a sinistra. Ciò può essere [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) ottenuto impostando la proprietà. Questa proprietà ottiene o imposta la direzione in cui gli elementi dell'interfaccia utente [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) scorrono all'interno di qualsiasi elemento padre che controlla il layout e deve essere impostata su uno dei valori di enumerazione:This property gets or sets the direction in which UI elements flow within any parent element that controls their layout, and should be set to one of the enumeration values:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

L'impostazione della [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) su un elemento in genere imposta l'allineamento a destra, l'ordine di lettura da destra a sinistra e il layout del controllo in modo che scorra da destra a sinistra:

[![TodoItemPage in arabo con una direzione di flusso da destra a sinistraTodoItemPage in Arabic with a right-to-left flow direction](rtl-images/TodoItemPage-Arabic.png "TodoItemPage in arabo con una direzione di flusso da destra a sinistraTodoItemPage in Arabic with a right-to-left flow direction")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage in arabo con una direzione di flusso da destra a sinistraTodoItemPage in Arabic with a right-to-left flow direction")

> [!TIP]
> È necessario impostare la proprietà solo nel [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) layout iniziale. La modifica di questo valore in fase di esecuzione genera un laborioso processo di elaborazione del layout che influirà sulle prestazioni.

Il [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valore predefinito della proprietà per [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)un elemento `FlowDirection` senza padre è [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent), mentre il valore predefinito per un elemento con un elemento padre è . Pertanto, un elemento eredita il valore della proprietà `FlowDirection` dal relativo elemento padre nella struttura ad albero visuale e qualsiasi elemento può sostituire il valore ereditato dal relativo elemento padre.

> [!TIP]
> Quando localizzi un'app per le lingue [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) da destra a sinistra, imposta la proprietà in una pagina o in un layout radice. In questo modo, tutti gli elementi contenuti all'interno del layout di pagina o radice risponderanno in modo appropriato alla direzione del flusso.

## <a name="respecting-device-flow-direction"></a>Rispetto della direzione del flusso di un dispositivo

Il rispetto della direzione del flusso del dispositivo in funzione della lingua e dell'area geografica selezionata è una scelta esplicita che deve operare lo sviluppatore e, pertanto, non avviene automaticamente. Può essere ottenuto [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) impostando la proprietà in una pagina, o layout radice, sul `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valore:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Per impostazione predefinita, tutti gli elementi figlio della [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) pagina, o layout radice, erediteranno il valore.

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

![Info.plist lingue supportate](rtl-images/ios-locales.png "Info.plist lingue supportate")

Per altre informazioni, vedere [Nozioni fondamentali di localizzazione in iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

La localizzazione da destra a sinistra può essere testata cambiando la lingua e l'area geografica nel dispositivo o simulatore e scegliendo le impostazioni locali per la direzione da destra a sinistra specificate in **Info.plist**.

> [!WARNING]
> Si noti che quando si modifica la lingua e l'area [`DatePicker`](xref:Xamarin.Forms.DatePicker) geografica in un'impostazione locale da destra a sinistra in iOS, tutte le visualizzazioni genereranno un'eccezione se non si includono le risorse necessarie per le impostazioni locali. Ad esempio, durante il test di un'app in arabo con un `DatePicker`, verificare che **mideast** sia selezionato nella sezione **Internazionalizzazione** del riquadro **Compilazione iOS**.

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

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)posizione del pulsante, posizione dell'elemento della barra degli [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) strumenti e animazione di transizione è controllata dalle impostazioni locali del dispositivo, anziché dalla proprietà .
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)direzione di scorrimento non si capovolge.
- [`Image`](xref:Xamarin.Forms.Image)contenuto visivo non si capovolge.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))e [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) l'orientamento è controllato dalle [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) impostazioni locali del dispositivo, anziché dalla proprietà .
- [`WebView`](xref:Xamarin.Forms.WebView)contenuto non rispetta [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) la proprietà.
- È necessario aggiungere una proprietà `TextDirection` per controllare l'allineamento del testo.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper)l'orientamento è controllato dalle [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) impostazioni locali del dispositivo, anziché dalla proprietà .
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)l'allineamento del testo è controllato [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) dalle impostazioni locali del dispositivo, anziché dalla proprietà .
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)i gesti e l'allineamento non vengono invertiti.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar)l'orientamento è controllato dalle [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) impostazioni locali del dispositivo, anziché dalla proprietà .
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)il posizionamento è controllato dalle [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) impostazioni locali del dispositivo, anziché dalla proprietà .

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor)l'allineamento del testo è controllato [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) dalle impostazioni locali del dispositivo, anziché dalla proprietà .
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)proprietà non viene [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ereditata da elementi figlio.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)l'allineamento del testo è controllato [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) dalle impostazioni locali del dispositivo, anziché dalla proprietà .

## <a name="force-right-to-left-layout"></a>Forzare il layout da destra a sinistra

Le applicazioni Xamarin.iOS e Xamarin.Android possono essere forzate a utilizzare sempre un layout da destra a sinistra, indipendentemente dalle impostazioni del dispositivo, modificando i rispettivi progetti di piattaforma.

### <a name="ios"></a>iOS

Le applicazioni Xamarin.iOS possono essere forzate per utilizzare sempre un layout da destra a sinistra modificando la classe **AppDelegate** come segue:

1. Dichiarare `IntPtr_objc_msgSend` la funzione come prima `AppDelegate` riga della classe:

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. Chiamare `IntPtr_objc_msgSend` la funzione `FinishedLaunching` dal metodo, prima `FinshedLaunching` di restituire dal metodo :

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

Questo approccio è utile per le applicazioni che richiedono sempre un layout [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) da destra a sinistra e rimuove il requisito per impostare la proprietà.

Per ulteriori informazioni `IntrPtr_objc_msgSend` sul metodo, vedere [Selettori Objective-C in Xamarin.iOS](~/ios/internals/objective-c-selectors.md).

### <a name="android"></a>Android

Xamarin.Android applicazioni possono essere costretti a utilizzare sempre un layout da destra a sinistra modificando il MainActivity classe per includere la riga seguente:Xamarin.Android applications can be forced to always use a right-to-left layout by modifying the **MainActivity** class to include the following line:

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

Questo approccio è utile per le applicazioni che richiedono sempre un layout [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) da destra a sinistra e rimuove il requisito per impostare la proprietà.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Supporto delle lingue da destra a sinistra con Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 Right-to-Left Support video** (Supporto da destra a sinistra di Xamarin.Forms 3.0 - video)

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
