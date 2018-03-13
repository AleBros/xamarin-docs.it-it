---
title: Collegamento in Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 3528E195-AA74-90AF-B5F3-3B65FB4F0BB8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: bfbd95d33e442d31e94bd8c6ed888741f88d1188
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="linking-on-android"></a>Collegamento in Android

Le applicazioni Xamarin.Android usano un *linker* per ridurre le dimensioni dell'applicazione. Il linker esegue un'analisi statica dell'applicazione per determinare gli assembly, i tipi e i membri effettivamente usati. Il linker si comporta quindi come *Garbage Collector*, cercando continuamente gli assembly, i tipi e i membri referenziati, fino a trovare l'intera closure di assembly, tipi e membri referenziati. Tutto quello che è esterno a questa closure viene quindi *rimosso*.

Considerando l'esempio [Hello, Android](https://developer.xamarin.com/samples/HelloM4A/):

<table border="0" cellpadding="1" cellspacing="1">
  <tbody>
    <tr>
      <td>
        <strong>Configurazione</strong>
      </td>
      <td>
        <strong>Dimensioni 1.2.0</strong>
      </td>
      <td>
        <strong>Dimensioni 4.0.1</strong>
      </td>
    </tr>
    <tr>
      <td>
Rilascio senza collegamento: </td>
      <td>
14,0 MB </td>
      <td>
16,0 MB </td>
    </tr>
    <tr>
      <td>
Rilascio con collegamento: </td>
      <td>
4,2 MB </td>
      <td>
2,9 MB </td>
    </tr>
  </tbody>
</table>

Il collegamento restituisce un pacchetto con dimensioni inferiori del 30% rispetto al pacchetto (non collegato) originale nella versione 1.2.0 e con dimensioni inferiori del 18% rispetto al pacchetto non collegato nella versione 4.0.1.



## <a name="control"></a>Control

Il collegamento è basato sull'*analisi statica*. Di conseguenza, tutto quello che dipende dall'ambiente di runtime non viene rilevato:

```csharp
// To play along at home, Example must be in a different assembly from MyActivity.
public class Example {
    // Compiler provides default constructor...
}

[Activity (Label="Linker Example", MainLauncher=true)]
public class MyActivity {
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Will this work?
        var o = Activator.CreateInstance (typeof (ExampleLibrary.Example));
    }
}
```


### <a name="linker-behavior"></a>Comportamento del linker

Il meccanismo principale per controllare il linker è l'elenco a discesa **Comportamento del linker** (*Collegamento* in Visual Studio) all'interno della finestra di dialogo **Opzioni progetto**. Sono disponibili tre opzioni:

1.  **Non collegare** (*Nessuno* in Visual Studio)
1.  **Collega solo assembly SDK** (*Solo assembly SDK*)
1.  **Collega tutti gli assembly** (*Assembly SDK e utente*)


L'opzione **Non collegare** disattiva il linker. L'esempio delle dimensioni dell'applicazione con configurazione "Rilascio senza collegamento" mostrato sopra usa questo comportamento. Si tratta di un'opzione utile per la risoluzione di problemi causati da errori di runtime, per determinare se è responsabile il linker. Questa impostazione è in genere sconsigliata per le compilazioni di produzione.

L'opzione **Collega solo assembly SDK** collega solo gli [assembly forniti con Xamarin.Android](~/cross-platform/internals/available-assemblies.md).
Tutti gli altri assembly (ad esempio il codice dell'utente) non vengono collegati.

L'opzione **Collega tutti gli assembly** collega tutti gli assembly e questo significa che può essere rimosso anche il codice dell'utente se non sono presenti riferimenti statici.

L'esempio precedente funziona con le opzioni *Non collegare* e *Collega solo assembly SDK*, ma non con il comportamento *Collega tutti gli assembly*, con cui genera l'errore seguente:

```shell
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): UNHANDLED EXCEPTION: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type,bool) <0x00180>
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type) <0x00017>
I/MonoDroid(17755): at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle) <0x00027>
I/MonoDroid(17755): at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (intptr,intptr,intptr) <0x00057>
I/MonoDroid(17755): at (wrapper dynamic-method) object.95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr) <0x00033>
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):
E/mono    (17755): Unhandled Exception: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type, Boolean nonPublic) [0x00000] in <filename unknown>:0
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type) [0x00000] in <filename unknown>:0
E/mono    (17755):   at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle bundle) [0x00000] in <filename unknown>:0
E/mono    (17755):   at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (IntPtr jnienv, IntPtr native__this, IntPtr native_savedInstanceState) [0x00000] in <filename unknown>:0
E/mono    (17755):   at (wrapper dynamic-method) object:95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr)
```


### <a name="preserving-code"></a>Mantenimento del codice

Il linker rimuove talvolta parti di codice che si potrebbe voler mantenere. Ad esempio:

-   Potrebbe essere presente codice che viene chiamato in modo dinamico tramite `System.Reflection.MemberInfo.Invoke`.

-   Se si creano istanze dei tipi in modo dinamico, potrebbe essere necessario mantenere il costruttore predefinito dei tipi.

-   Se si usa la serializzazione XML, potrebbe essere necessario mantenere le proprietà dei tipi.

In questi casi, è possibile usare l'attributo [Android.Runtime.Preserve](https://developer.xamarin.com/api/type/Android.Runtime.PreserveAttribute/). Poiché ogni membro che non è collegato in modo statico dall'applicazione è soggetto alla rimozione, è possibile usare questo attributo per contrassegnare i membri non referenziati in modo statico, ma che sono comunque necessari per l'applicazione. È possibile applicare questo attributo a ogni membro di un tipo oppure al tipo stesso.

Nell'esempio seguente questo attributo viene usato per mantenere il costruttore della classe `Example`:

```csharp
public class Example
{
    [Android.Runtime.Preserve]
    public Example ()
    {
    }
}
```

Se si vuole mantenere l'intero tipo, è possibile usare la sintassi di attributo seguente:

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
```

Ad esempio, nel frammento di codice seguente viene mantenuta l'intera classe `Example` per la serializzazione XML:

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
class Example
{
    // Compiler provides default constructor...
}
```

A volte è necessario mantenere determinati membri, ma solo se è stato mantenuto il tipo che li contiene. In questi casi, usare la sintassi di attributo seguente:

```csharp
[Android.Runtime.Preserve (Conditional = true)]
```

Se non si vuole definire una dipendenze dalle librerie Xamarin, ad esempio quando si compila una libreria di classi portabile (PCL) multipiattaforma, è comunque possibile usare l'attributo `Android.Runtime.Preserve`. A questo scopo, dichiarare una classe `PreserveAttribute` all'interno dello spazio dei nomi `Android.Runtime` in questo modo:

```csharp
namespace Android.Runtime
{
    public sealed class PreserveAttribute : System.Attribute
    {
        public bool AllMembers;
        public bool Conditional;
    }
}
```



### <a name="falseflag"></a>falseflag

Se l'attributo [Preserve] non può essere usato, è spesso utile fornire un blocco di codice in modo che il linker rilevi che il tipo viene usato, impedendo l'esecuzione del blocco di codice al runtime. Per usare questa tecnica, è possibile procedere in questo modo:

```csharp
[Activity (Label="Linker Example", MainLauncher=true)]
class MyActivity {

#pragma warning disable 0219, 0649
    static bool falseflag = false;
    static MyActivity ()
    {
        if (falseflag) {
            var ignore = new Example ();
        }
    }
#pragma warning restore 0219, 0649

    // ...
}
```



### <a name="linkskip"></a>linkskip

È possibile specificare che un set di assembly forniti dall'utente non venga collegato del tutto, permettendo di ignorare altri assembly utente con il comportamento *Collega solo assembly SDK* usando la [proprietà di MSBuild AndroidLinkSkip](~/android/deploy-test/building-apps/build-process.md):

```xml
<PropertyGroup>
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
</PropertyGroup>
```


### <a name="linkdescription"></a>LinkDescription

L'**azione di compilazione** [`@(LinkDescription)`](~/android/deploy-test/building-apps/build-process.md)
 può essere usata nei file che possono contenere un [file di configurazione personalizzato del linker](~/cross-platform/deploy-test/linker.md).
edmx. I file di configurazione personalizzati del linker possono essere necessari per mantenere membri `internal` o `private` che devono essere conservati.



### <a name="custom-attributes"></a>Attributi personalizzati

Quando un assembly viene collegato, vengono rimossi i tipi di attributo personalizzati seguenti da tutti i membri:

-  System.ObsoleteAttribute
-  System.MonoDocumentationNoteAttribute
-  System.MonoExtensionAttribute
-  System.MonoInternalNoteAttribute
-  System.MonoLimitationAttribute
-  System.MonoNotSupportedAttribute
-  System.MonoTODOAttribute
-  System.Xml.MonoFIXAttribute


Quando un assembly viene collegato, vengono rimossi i tipi di attributo personalizzati seguenti da tutti i membri nelle compilazioni di rilascio:

-  System.Diagnostics.DebuggableAttribute
-  System.Diagnostics.DebuggerBrowsableAttribute
-  System.Diagnostics.DebuggerDisplayAttribute
-  System.Diagnostics.DebuggerHiddenAttribute
-  System.Diagnostics.DebuggerNonUserCodeAttribute
-  System.Diagnostics.DebuggerStepperBoundaryAttribute
-  System.Diagnostics.DebuggerStepThroughAttribute
-  System.Diagnostics.DebuggerTypeProxyAttribute
-  System.Diagnostics.DebuggerVisualizerAttribute


## <a name="related-links"></a>Collegamenti correlati

- [Configurazione personalizzata del linker](~/cross-platform/deploy-test/linker.md)
- [Collegamento in iOS](~/ios/deploy-test/linker.md)
