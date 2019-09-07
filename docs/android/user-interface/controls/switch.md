---
title: Switch Novell. Android
description: Come usare il widget switch in un'applicazione Novell. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: 82271711864363bbaf593e8c44e31632048399dc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764883"
---
# <a name="xamarinandroid-switch"></a>Switch Novell. Android

Il `Switch` widget (mostrato di seguito) consente a un utente di passare da uno stato all'altro, ad esempio on o off. Il `Switch` valore predefinito è off. Il widget è riportato di seguito negli Stati ON e OFF:

[![Screenshot di un widget switch negli Stati OFF e ON](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Creazione di un'opzione

Per creare un'opzione, è sufficiente dichiarare `Switch` un elemento in XML, come indicato di seguito:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Viene creato un commute di base, come illustrato di seguito:

[![Screenshot dell'app demo che visualizza un interruttore nello stato disattivato](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Modifica dei valori predefiniti

Sia il testo visualizzato dal controllo per gli stati ON che OFF e il valore predefinito sono configurabili. Ad esempio, per impostare il parametro su on e leggere No/Yes anziché off/on, è possibile impostare gli `checked`attributi, `textOn`e `textOff` nel codice XML seguente.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>Fornire un titolo

Il `Switch` widget supporta anche l'inclusione di un'etichetta di `text` testo impostando l'attributo nel modo seguente:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Questo markup produce lo screenshot seguente in fase di esecuzione:

[![Screenshot dell'app demo con testo che precede orizzontalmente il widget switch](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Quando un `Switch`valore di viene modificato, viene generato `CheckedChange` un evento.
Nel codice seguente, ad esempio, si acquisisce questo evento e si `Toast` presenta un widget con un messaggio basato `isChecked` sul valore `Switch`di, che viene passato `CompoundButton.CheckedChangeEventArg` al gestore eventi come parte dell'argomento.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>Collegamenti correlati

- [SwitchDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/switchdemo)
- [Esercitazione sul layout a schede](~/android/user-interface/layouts/tab-layout/index.md)
