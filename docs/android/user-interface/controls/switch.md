---
title: Switch Xamarin.Android
description: Come usare il widget switch in un'applicazione Xamarin.Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 73becb5e4424854c9be6cdc3554f6cf93507b9a9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029130"
---
# <a name="xamarinandroid-switch"></a>Switch Xamarin.Android

Il widget `Switch` (illustrato di seguito) consente a un utente di passare da uno stato all'altro, ad esempio ON o OFF. Il valore predefinito `Switch` è disattivato. Il widget è riportato di seguito negli Stati ON e OFF:

[![screenshot di un widget switch negli Stati OFF e ON](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Creazione di un'opzione

Per creare un'opzione, è sufficiente dichiarare un elemento `Switch` in XML, come indicato di seguito:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Viene creato un commute di base, come illustrato di seguito:

[![screenshot dell'app demo che visualizza un interruttore nello stato disattivato](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Modifica dei valori predefiniti

Sia il testo visualizzato dal controllo per gli stati ON che OFF e il valore predefinito sono configurabili. Ad esempio, per impostare il parametro su ON e leggere NO/YES anziché OFF/ON, è possibile impostare gli attributi `checked`, `textOn`e `textOff` nel codice XML seguente.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>Fornire un titolo

Il widget `Switch` supporta anche l'inclusione di un'etichetta di testo impostando l'attributo `text` come segue:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Questo markup produce lo screenshot seguente in fase di esecuzione:

[![screenshot dell'app demo con testo che precede orizzontalmente il widget switch](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Quando viene modificato il valore di un `Switch`, viene generato un evento di `CheckedChange`.
Nel codice seguente, ad esempio, si acquisisce questo evento e si presenta un `Toast` widget con un messaggio basato sul valore `isChecked` di `Switch`, che viene passato al gestore eventi come parte dell'argomento `CompoundButton.CheckedChangeEventArg`.

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
