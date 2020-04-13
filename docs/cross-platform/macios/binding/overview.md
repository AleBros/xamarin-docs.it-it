---
title: Panoramica delle associazioni Objective-C
description: In questo documento viene fornita una panoramica dei diversi modi per creare associazioni C , per il codice Objective-C, incluse le associazioni della riga di comando, i progetti di associazione e Sharpie obiettivo. Viene inoltre illustrato il funzionamento dell'associazione.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: be2f7f555b76d472f7a66d95e661bb2f5884c58f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292903"
---
# <a name="overview-of-objective-c-bindings"></a>Panoramica delle associazioni Objective-C

_Dettagli sul funzionamento del processo di binding_

Associazione di una libreria Objective-C per l'uso con Xamarin richiede tre passaggi:

1. Scrivere una "definizione di API" di C' per descrivere come viene esposta l'API nativa in .NET e come viene eseguito il mapping all'oggetto Objective-C sottostante. Questa operazione viene eseguita utilizzando `interface` i costrutti standard di C, ad esempio e vari **attributi** di associazione (vedere questo [semplice esempio](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Dopo aver scritto la "definizione dell'API" in C, compilarla per produrre un assembly "associazione". Questa operazione può essere eseguita dalla riga di comando o usando un progetto di [**associazione**](#bindingproject) in Visual Studio per Mac o Visual Studio.This can be done on the [**command**](#commandline) line or using a binding project in Visual Studio for Mac or Visual Studio.

3. Tale assembly di "associazione" viene quindi aggiunto al progetto di applicazione Xamarin, in modo da poter accedere alla funzionalità nativa utilizzando l'API definita.
   Il progetto di associazione è completamente separato dai progetti dell'applicazione.

   > [!NOTE]
   > Il passaggio 1 può essere automatizzato con l'assistenza di [**Objective Sharpie**](#objectivesharpie). Esamina l'API Objective-C e genera una "definizione di API" di C. È possibile personalizzare i file creati da Sharpie obiettivo e utilizzarli in un progetto di associazione (o nella riga di comando) per creare l'assembly di associazione. Obiettivo Sharpie non crea associazioni da solo, è semplicemente una parte facoltativa del processo più grande.

Puoi anche leggere ulteriori dettagli tecnici su [come funziona,](#howitworks)che ti aiuteranno a scrivere i tuoi binding.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Associazioni della riga di comando

È possibile `btouch-native` utilizzare il per Xamarin.iOS (o `bmac-native` se si utilizza Xamarin.Mac) per creare associazioni direttamente. Funziona passando le definizioni dell'API di C, che hai creato a mano (o`btouch-native` usando Sharpie obiettivo) allo strumento della riga di comando (per iOS o `bmac-native` Mac).

La sintassi generale per richiamare questi strumenti è:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

Il comando precedente genererà il file `cocos2d.dll` nella directory corrente e conterrà la libreria completamente associata che è possibile utilizzare nel progetto. Questo è lo strumento utilizzato da Visual Studio per Mac per creare le associazioni se si utilizza un progetto di associazione (descritto [di seguito).](#bindingproject)

<a name="bindingproject" />

## <a name="binding-project"></a>Progetto di associazioneBinding Project

Un progetto di associazione può essere creato in Visual Studio per Mac o Visual Studio (Visual Studio supporta solo le associazioni iOS) e semplifica la modifica e la compilazione di definizioni API per l'associazione (rispetto all'utilizzo della riga di comando).

Seguire questa [guida introduttiva](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) per vedere come creare e usare un progetto di associazione per produrre un'associazione.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Obiettivo Sharpie è un altro strumento da riga di comando separato che aiuta con le fasi iniziali della creazione di un'associazione. Non crea un'associazione da sola, ma automatizza il passaggio iniziale della generazione di una definizione API per la libreria nativa di destinazione.

Leggi i [documenti di Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) per imparare ad analizzare le librerie native, i framework nativi e i CocoaPods in definizioni API che possono essere incorporate in associazioni.

<a name="howitworks" />

## <a name="how-binding-works"></a>Funzionamento dell'associazione

È possibile utilizzare l'attributo [[Register],](xref:Foundation.RegisterAttribute) l'attributo [[Export]](xref:Foundation.ExportAttribute) e la chiamata manuale del [selettore Objective-C](~/ios/internals/objective-c-selectors.md) per associare manualmente nuovi tipi Objective-C (precedentemente non associati).

In primo luogo, trovare un tipo che si desidera associare. A scopo di discussione (e semplicità), verrà associato il [NSEnumerator](https://developer.apple.com/documentation/foundation/nsenumerator) tipo (che è già stato associato in [Foundation.NSEnumerator](xref:Foundation.NSEnumerator); l'implementazione riportata di seguito è solo per scopi di esempio).

In secondo luogo, è necessario creare il tipo di C . Probabilmente vorremo inserire questo in uno spazio dei nomi; Poiché Objective-C non supporta gli spazi dei nomi, è necessario utilizzare l'attributo `[Register]` per modificare il nome del tipo che Xamarin.iOS registrerà con il runtime di Objective-C. Anche il tipo di C'è deve ereditare da [Foundation.NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

In terzo luogo, esaminare la documentazione di Objective-C e creare istanze [di ObjCRuntime.Selector](xref:ObjCRuntime.Selector) per ogni selettore che si desidera utilizzare. Collocarli all'interno del corpo della classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

In quarto luogo, il tipo dovrà fornire i costruttori. È *necessario* concatenare la chiamata al costruttore al costruttore della classe base. Gli `[Export]` attributi consentono al codice Objective-C di chiamare i costruttori con il nome del selettore specificato:The attributes permit Objective-C code to call the constructors with the specified selector name:

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

Quinto, fornire metodi per ciascuno dei selettori dichiarati nel passaggio 3. Questi verranno utilizzati `objc_msgSend()` per richiamare il selettore sull'oggetto nativo. Si noti l'uso di [Runtime.GetNSObject()](xref:ObjCRuntime.Runtime.GetNSObject*) per convertire un `IntPtr` tipo in un tipo di (sotto-) tipizzato `NSObject` in modo appropriato. Se si desidera che il metodo sia richiamabile dal codice Objective-C, il membro *deve* essere **virtuale.**

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

Mettere tutto insieme:

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```
