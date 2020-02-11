---
title: Cenni preliminari sui binding Objective-C
description: In questo documento viene fornita una panoramica dei diversi modi C# per creare binding per il codice Objective-C, incluse le associazioni della riga di comando, i progetti di binding e l'obiettivo Sharpie. Viene inoltre illustrato il funzionamento dell'associazione.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: cad352466e7661183c5277f60c63c283342c50fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015874"
---
# <a name="overview-of-objective-c-bindings"></a>Cenni preliminari sui binding Objective-C

_Informazioni sul funzionamento del processo di associazione_

Il binding di una libreria Objective-C per l'uso con Xamarin richiede tre passaggi:

1. Scrivere una C# "definizione API" per descrivere il modo in cui l'API nativa viene esposta in .NET e il modo in cui viene mappata all'oggetto sottostante Objective-C. Questa operazione viene eseguita usando C# costrutti standard come `interface`e diversi **attributi** di associazione (vedere questo [semplice esempio](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Una volta scritta la "definizione API" in C#, la si compila per produrre un assembly "binding". Questa operazione può essere eseguita dalla [**riga di comando**](#commandline) o tramite un [**progetto di binding**](#bindingproject) in Visual Studio per Mac o Visual Studio.

3. L'assembly "binding" viene quindi aggiunto al progetto dell'applicazione Xamarin, in modo che sia possibile accedere alle funzionalità native usando l'API definita.
   Il progetto di associazione è completamente separato dai progetti dell'applicazione.

   > [!NOTE]
   > Il passaggio 1 può essere automatizzato con l'assistenza dell' [**obiettivo Sharpie**](#objectivesharpie). Esamina l'API Objective-C e genera una proposta C# "definizione API". È possibile personalizzare i file creati da Objective Sharpie e usarli in un progetto di binding (o nella riga di comando) per creare l'assembly di associazione. L'obiettivo Sharpie non crea associazioni da solo, ma è semplicemente una parte facoltativa del processo più grande.

È anche possibile leggere ulteriori dettagli tecnici sul [funzionamento, che](#howitworks)consentiranno di scrivere le associazioni.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Associazioni della riga di comando

È possibile utilizzare il `btouch-native` per Xamarin.iOS (o `bmac-native` se si utilizza Xamarin.Mac) per compilare direttamente le associazioni. Funziona passando le C# definizioni API create manualmente (o usando l'obiettivo Sharpie) allo strumento da riga di comando (`btouch-native`per iOS o`bmac-native`per Mac).

La sintassi generale per richiamare questi strumenti è la seguente:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

Il comando precedente genererà il file `cocos2d.dll` nella directory corrente e conterrà la libreria con binding completo che è possibile usare nel progetto. Questo è lo strumento che Visual Studio per Mac usa per creare i binding se si usa un progetto di associazione (descritto di [seguito](#bindingproject)).

<a name="bindingproject" />

## <a name="binding-project"></a>Progetto di binding

È possibile creare un progetto di binding in Visual Studio per Mac o in Visual Studio (Visual Studio supporta solo le associazioni iOS) e semplifica la modifica e la compilazione delle definizioni API per l'associazione (rispetto all'uso della riga di comando).

Seguire questa [Guida introduttiva](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) per vedere come creare e usare un progetto di associazione per produrre un'associazione.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Objective Sharpie è un altro strumento da riga di comando separato che consente di creare un'associazione con le fasi iniziali. Non crea un'associazione autonomamente, ma consente di automatizzare il passaggio iniziale della generazione di una definizione API per la libreria nativa di destinazione.

Per informazioni su come analizzare le librerie native, i framework nativi e CocoaPods in definizioni API che possono essere incorporati in binding, vedere l'articolo relativo a [Objective Sharpie docs](~/cross-platform/macios/binding/objective-sharpie/index.md) .

<a name="howitworks" />

## <a name="how-binding-works"></a>Funzionamento dell'associazione

È possibile utilizzare l'attributo [[Register]](xref:Foundation.RegisterAttribute) , l'attributo [[Export]](xref:Foundation.ExportAttribute) e la [chiamata manuale del selettore Objective-c](~/ios/internals/objective-c-selectors.md) per associare manualmente i tipi Objective-c nuovi (precedentemente non associati).

Per prima cosa, trovare un tipo che si vuole associare. Ai fini della discussione (e semplicità), verrà associato il tipo [NSEnumerator](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) (che è già stato associato a [Foundation. NSEnumerator](xref:Foundation.NSEnumerator); l'implementazione seguente è solo per scopi di esempio).

In secondo luogo, è necessario creare C# il tipo. È probabile che si desideri inserire questo oggetto in uno spazio dei nomi; Poiché Objective-C non supporta gli spazi dei nomi, è necessario usare l'attributo `[Register]` per modificare il nome del tipo che Xamarin.iOS registrerà con il runtime di Objective-C. Il C# tipo deve anche ereditare da [Foundation. NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

In terzo luogo, esaminare la documentazione di Objective-C e creare istanze di [ObjCRuntime. Selector](xref:ObjCRuntime.Selector) per ogni selettore che si desidera utilizzare. Inserire i seguenti all'interno del corpo della classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quarto, il tipo dovrà fornire costruttori. È *necessario* concatenare la chiamata del costruttore al costruttore della classe base. Gli attributi `[Export]` consentono al codice Objective-C di chiamare i costruttori con il nome del selettore specificato:

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

Quinto, fornire i metodi per ognuno dei selettori dichiarati nel passaggio 3. Questi utilizzeranno `objc_msgSend()` per richiamare il selettore sull'oggetto nativo. Si noti l'uso di [Runtime. GetNSObject ()](xref:ObjCRuntime.Runtime.GetNSObject*) per convertire un `IntPtr` in un tipo di `NSObject` (Sub-) tipizzato in modo appropriato. Se si desidera che il metodo sia chiamabile dal codice Objective-C, il membro *deve* essere **virtuale**.

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

Riunendoli:

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
