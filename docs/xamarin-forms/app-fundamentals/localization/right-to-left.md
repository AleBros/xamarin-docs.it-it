---
title: Localizzazione da destra a sinistra
description: Da destra a sinistra localizzazione aggiunge il supporto per la direzione di flusso da destra a sinistra per le applicazioni di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 94cf937a810e84ead0bdc1f69933968b42090a6f
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846432"
---
# <a name="right-to-left-localization"></a>Localizzazione da destra a sinistra

_Da destra a sinistra localizzazione aggiunge il supporto per la direzione di flusso da destra a sinistra per le applicazioni di xamarin. Forms._

> [!NOTE]
> Da destra a sinistra localizzazione richiede l'utilizzo di API 17 o superiore in Android e iOS 9 o versione successiva.

Direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio. Alcuni linguaggi, come l'arabo ed ebraico, richiedono che gli elementi dell'interfaccia utente sono posizionati in una direzione di flusso da destra a sinistra. Questo risultato può essere ottenuto impostando il [ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà. Questa proprietà ottiene o imposta la direzione nella quale flusso di elementi dell'interfaccia utente all'interno di qualsiasi elemento padre che controlla il layout e deve essere impostato su uno del [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valori di enumerazione:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Impostazione di [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft) su un elemento in genere imposta l'allineamento a destra, l'ordine di lettura da destra a sinistra e il layout del controllo da da destra a sinistra:

[![TodoItemPage in arabo con una direzione di flusso da destra a sinistra](rtl-images/TodoItemPage-Arabic.png "TodoItemPage in arabo con una direzione di flusso da destra a sinistra")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage in arabo con una direzione di flusso da destra a sinistra")

> [!TIP]
> È necessario impostare solo la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà nel layout iniziale. Se si modifica questo valore in fase di esecuzione, un processo di layout costoso che influirà sulle prestazioni.

Il valore predefinito [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) è il valore della proprietà per un elemento senza un padre [ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight), mentre il valore predefinito `FlowDirection` per un elemento con un elemento padre è [ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Pertanto, un elemento eredita la `FlowDirection` valore della proprietà dal relativo elemento padre nell'albero visuale e qualsiasi altro elemento possibile sostituire il valore viene ottenuta dal relativo elemento padre.

> [!TIP]
> Durante la localizzazione di un'app per lingue da destra a sinistra, impostare il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà in un layout di pagina o radice. In questo modo tutti gli elementi contenuti all'interno della pagina o layout radice, rispondere in modo appropriato per la direzione del flusso.

## <a name="respecting-device-flow-direction"></a>Direzione del flusso dispositivo rispettando

Rispetto della direzione del flusso del dispositivo in base alla lingua selezionata e regione è una scelta esplicita developer e non viene eseguita automaticamente. Può essere ottenuto impostando il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà in una pagina o layout radice per il `static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valore:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Tutti gli elementi figlio della pagina o il layout di primo livello, per impostazione predefinita quindi erediteranno le [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valore.

## <a name="platform-setup"></a>Installazione della piattaforma

Programma di installazione specifico della piattaforma è necessario per abilitare le impostazioni locali da destra a sinistra.

### <a name="ios"></a>iOS

Le impostazioni locali da destra a sinistra necessari devono essere aggiunti come una lingua supportata per gli elementi di matrice per la `CFBundleLocalizations` chiave nel **Info. plist**. Nell'esempio seguente viene illustrato arabo è stato aggiunto nella matrice per la `CFBundleLocalizations` chiave:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Lingue supportate Info. plist](rtl-images/ios-locales.png "Info. plist le lingue supportate")

Per altre informazioni, vedere [nozioni di localizzazione in iOS](https://docs.microsoft.com/en-gb/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Può essere testata localizzazione da destra a sinistra modificando la lingua e paese nel dispositivo o simulatore per le impostazioni locali da destra a sinistra che è stata specificata in **Info. plist**.

> [!WARNING]
> Si noti che quando si modifica la lingua e paese per le impostazioni locali da destra a sinistra in iOS, qualsiasi [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) viste verranno generata un'eccezione se non si include le risorse necessarie per le impostazioni locali. Ad esempio, durante il test di un'app in arabo con un `DatePicker`, verificare che **mideast** sia selezionato nel **internazionalizzazione** sezione del **compilazione iOS** riquadro.

### <a name="android"></a>Android

L'app **androidmanifest. XML** deve essere aggiornato in modo che il `<uses-sdk>` i set di nodi il `android:minSdkVersion` attributo 17 e il `<application>` i set di nodi il `android:supportsRtl` attributo `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

Da destra a sinistra localizzazione può essere testata modificando l'emulatore per utilizzare la lingua da destra a sinistra o abilitando **direzione del layout da destra a sinistra Force** in **Impostazioni > opzioni per gli sviluppatori**.

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Le risorse di lingua richiesta devono essere specificate nel `<Resources>` nodo il **package. appxmanifest** file. L'esempio seguente mostra arabo è stato aggiunto per il `<Resources>` nodo:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Inoltre, UWP richiede che le impostazioni cultura predefinite dell'app sono definita in modo esplicito nella libreria Standard di .NET. Ciò può essere soddisfatta impostando il `NeutralResourcesLanguage` attributo `AssemblyInfo.cs`, o in un'altra classe, per le impostazioni cultura predefinite:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

Può essere testata localizzazione da destra a sinistra modificando la lingua e paese del dispositivo alle impostazioni locali appropriate da destra a sinistra.

## <a name="limitations"></a>Limitazioni

Localizzazione da destra a sinistra di xamarin. Forms attualmente presenta diverse limitazioni:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) posizione pulsante, percorso di elemento della barra degli strumenti e animazione di transizione viene controllata dalle impostazioni internazionali di dispositivo invece che al [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) direzione di spostamento non invertite.
- [`Image`](xref:Xamarin.Forms.Image) contenuto visivo non invertite.
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) e [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) orientamento dipende dalle impostazioni locali del dispositivo, anziché la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`WebView`](xref:Xamarin.Forms.WebView) contenuto non rispetta il [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- Oggetto `TextDirection` proprietà deve essere aggiunto, per controllare l'allineamento del testo.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) l'orientamento è controllata dalle impostazioni internazionali di dispositivo invece che al [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) l'allineamento del testo è controllata dalle impostazioni internazionali di dispositivo invece che al [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) i movimenti e allineamento non vengono annullate.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) l'orientamento è controllata dalle impostazioni internazionali di dispositivo invece che al [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) la selezione è controllata dalle impostazioni internazionali di dispositivo invece che al [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) l'allineamento del testo è controllata dalle impostazioni internazionali di dispositivo invece che al [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà non viene ereditata dai [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) gli elementi figlio.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) l'allineamento del testo è controllata dalle impostazioni internazionali di dispositivo invece che al [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Da destra a sinistra language supporta con Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Il supporto xamarin. Forms 3.0 destra a sinistra, da [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio TodoLocalizedRTL](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
