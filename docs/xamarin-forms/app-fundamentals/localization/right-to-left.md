---
title: Localizzazione da destra a sinistra
description: Localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra per le applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 6c0de68f974c704b5f43232a1fc98065c90ee4f7
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995723"
---
# <a name="right-to-left-localization"></a>Localizzazione da destra a sinistra

_Localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra per le applicazioni xamarin. Forms._

> [!NOTE]
> Localizzazione da destra a sinistra richiede l'uso di iOS 9 o versione successiva e l'API 17 o superiore in Android.

Direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente nella pagina vengono analizzati dall'occhio del lettore. Alcuni linguaggi, come l'arabo ed ebraico, richiedono che gli elementi dell'interfaccia utente vengono disposti in una direzione di flusso da destra a sinistra. Ciò può essere ottenuto impostando il [ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà. Questa proprietà ottiene o imposta la direzione nella quale si propagano gli elementi dell'interfaccia utente all'interno di qualsiasi elemento padre che controlla il layout e deve essere impostato su uno dei [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valori di enumerazione:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Impostando il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft) su un elemento in genere imposta l'allineamento a destra, l'ordine di lettura da destra a sinistra e il layout del controllo di flusso da right-to-left:

[![TodoItemPage in arabo con una direzione di flusso da destra a sinistra](rtl-images/TodoItemPage-Arabic.png "TodoItemPage in arabo con una direzione di flusso da destra a sinistra")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage in arabo con una direzione di flusso da destra a sinistra")

> [!TIP]
> È necessario impostare solo le [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà layout iniziale. Se si modifica questo valore in fase di esecuzione, un processo dispendiosi che influirà sulle prestazioni.

Il valore predefinito [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) è il valore della proprietà per un elemento senza un padre [ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight), mentre il valore predefinito `FlowDirection` per un elemento con un elemento padre è [ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Pertanto, un elemento eredita le `FlowDirection` valore della proprietà dal relativo elemento padre nell'albero visuale e qualsiasi elemento può sostituire il valore viene ottenuta dal relativo elemento padre.

> [!TIP]
> Durante la localizzazione di un'app per le lingue da destra a sinistra, impostare il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà in un layout di pagina o radice. In questo modo tutti gli elementi contenuti all'interno della pagina o layout radice, rispondere in modo appropriato per la direzione del flusso.

## <a name="respecting-device-flow-direction"></a>Rispettando la direzione di flusso di dispositivo

Rispettando la direzione di flusso del dispositivo in base alla lingua selezionata e area geografica è una scelta esplicita per gli sviluppatori e non viene eseguito automaticamente. Può essere ottenuto impostando il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà in una pagina o il layout di primo livello, per il `static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valore:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Tutti gli elementi figlio della pagina o il layout di primo livello, per impostazione predefinita quindi ereditano la [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valore.

## <a name="platform-setup"></a>Programma di installazione di piattaforma

Il programma di installazione specifico della piattaforma è necessario per abilitare le impostazioni locali da destra a sinistra.

### <a name="ios"></a>iOS

Le richieste dalle impostazioni locali da destra a sinistra devono essere aggiunti come una lingua supportata per gli elementi di matrice per il `CFBundleLocalizations` chiavi in **Info. plist**. L'esempio seguente mostra l'arabo è stato aggiunto nella matrice per il `CFBundleLocalizations` chiave:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Lingue supportate Info. plist](rtl-images/ios-locales.png "Info. plist lingue supportate")

Per altre informazioni, vedere [nozioni fondamentali di localizzazione in iOS](https://docs.microsoft.com/en-gb/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Right-to-left localizzazione può essere testata, modificare la lingua e area geografica in un dispositivo o simulatore da destra a sinistra delle impostazioni locali specificato nel **Info. plist**.

> [!WARNING]
> Si noti che quando si modifica la lingua e area geografica a delle impostazioni locali di right-to-left su iOS, qualsiasi [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) visualizzazioni verranno generata un'eccezione se non si include le risorse necessarie per le impostazioni locali. Ad esempio, durante il test di un'app in arabo con un `DatePicker`, verificare che **mideast** sia selezionato nel **internazionalizzazione** sezione del **compilazione iOS** riquadro.

### <a name="android"></a>Android

L'app **androidmanifest. XML** file deve essere aggiornati in modo che il `<uses-sdk>` i set di nodi il `android:minSdkVersion` 17, dell'attributo e il `<application>` i set di nodi il `android:supportsRtl` dell'attributo `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

Right-to-left localizzazione può essere testata modificando il dispositivo/emulatore per utilizzare la lingua da destra a sinistra oppure abilitando **direzione layout da destra a sinistra forzato** nelle **Impostazioni > opzioni per gli sviluppatori**.

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Le risorse della lingua necessari devono essere specificate nel `<Resources>` nodo del **package. appxmanifest** file. L'esempio seguente mostra l'arabo è stato aggiunto per il `<Resources>` nodo:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Inoltre, piattaforma UWP richiede che le impostazioni cultura predefinite dell'app sono definite esplicitamente nella libreria .NET Standard. Questa operazione può essere eseguita impostando il `NeutralResourcesLanguage` attributo `AssemblyInfo.cs`, o in un'altra classe, per le impostazioni cultura predefinite:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

Right-to-left localizzazione può essere testata, modificare la lingua e area geografica del dispositivo alle impostazioni locali appropriate da destra a sinistra.

## <a name="limitations"></a>Limitazioni

Localizzazione da destra a sinistra di xamarin. Forms presenta attualmente alcune limitazioni:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) posizione pulsante, percorso di elemento della barra degli strumenti e animazione di transizione è controllato da impostazioni locali del dispositivo anziché il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) direzione di scorrimento rapido non invertite.
- [`Image`](xref:Xamarin.Forms.Image) contenuto visivo non invertite.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) e [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) orientamento è controllato da impostazioni locali del dispositivo, anziché [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`WebView`](xref:Xamarin.Forms.WebView) contenuto non rispetta il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- Oggetto `TextDirection` proprietà deve essere aggiunto, per controllare l'allineamento del testo.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) l'orientamento è controllato da impostazioni locali del dispositivo anziché il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) allineamento del testo è controllato da impostazioni locali del dispositivo anziché il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) i movimenti e l'allineamento non vengono invertiti.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) l'orientamento è controllato da impostazioni locali del dispositivo anziché il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) il posizionamento è controllato da impostazioni locali del dispositivo anziché il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) allineamento del testo è controllato da impostazioni locali del dispositivo anziché il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà non viene ereditata dai [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) elementi figlio.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) allineamento del testo è controllato da impostazioni locali del dispositivo anziché il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Da destra a sinistra language supporto con Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Supporto di xamarin. Forms 3.0 Right-to-Left, da [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio TodoLocalizedRTL](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
