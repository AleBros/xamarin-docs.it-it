---
title: Registrar di tipo per Xamarin.iOS
description: Questo documento descrive il registrar di tipo Xamarin.iOS, C# che rende le classi disponibili per il runtime di Objective-C.
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: f38c49ce9334a5659f0a8b5dd03e3bae8863cf5a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022271"
---
# <a name="type-registrar-for-xamarinios"></a>Registrar di tipo per Xamarin.iOS

Questo documento descrive il sistema di registrazione dei tipi usato da Xamarin.iOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registrazione di classi gestite e metodi

Durante l'avvio, Xamarin.iOS registrerà:

- Classi con un attributo [[Register]](xref:Foundation.RegisterAttribute) come classi Objective-C.
- Classi con attributo [[Category]](xref:ObjCRuntime.CategoryAttribute) come categorie Objective-C.
- Interfacce con un attributo [[Protocol]](xref:Foundation.ProtocolAttribute) come protocolli Objective-C.
- Membri con [[Export]](xref:Foundation.ExportAttribute), rendendo possibile l'accesso a Objective-C.

Si consideri ad esempio il metodo di `Main` gestito comune nelle applicazioni Xamarin.iOS:

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

Questo codice indica al runtime di Objective-C di usare il tipo denominato `AppDelegate` come classe delegata dell'applicazione. Affinché il runtime di Objective-C sia in grado di creare un'istanza della C# classe`AppDelegate`, tale classe deve essere registrata.

Xamarin.iOS esegue automaticamente la registrazione, in fase di esecuzione (registrazione dinamica) o in fase di compilazione (registrazione statica).

La registrazione dinamica usa la reflection all'avvio per trovare tutte le classi e i metodi da registrare, passandoli al runtime di Objective-C. Per impostazione predefinita, la registrazione dinamica viene usata per le build del simulatore.

La registrazione statica controlla, in fase di compilazione, gli assembly usati dall'applicazione. Determina le classi e i metodi da registrare con Objective-C e genera una mappa, incorporata nel file binario.
Quindi, all'avvio, registra la mappa con il runtime di Objective-C. La registrazione statica viene usata per le compilazioni di dispositivi.

### <a name="categories"></a>Categories

A partire da Xamarin.iOS 8,10, è possibile creare categorie Objective-C usando C# la sintassi.

Per creare una categoria, usare l'attributo `[Category]` e specificare il tipo da estendere. Il codice seguente, ad esempio, estende `NSString`:

```csharp
[Category (typeof (NSString))]
```

Ognuno dei metodi di una categoria dispone di un attributo `[Export]`, rendendolo disponibile al runtime di Objective-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Tutti i metodi di estensione gestiti devono essere statici, ma è possibile creare metodi di istanza Objective-C usando la C# sintassi standard per i metodi di estensione:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

Il primo argomento del metodo di estensione è l'istanza sulla quale è stato richiamato il metodo:

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
 }
 ```

In questo esempio viene aggiunto un metodo di istanza di `toUpper` nativo alla classe `NSString`. Questo metodo può essere chiamato da Objective-C:

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

### <a name="protocols"></a>Protocolli

A partire da Xamarin.iOS 8,10, le interfacce con l'attributo `[Protocol]` verranno esportate in Objective-C come protocolli:

```csharp
[Protocol ("MyProtocol")]
interface IMyProtocol
{
    [Export ("method")]
    void Method ();
}

class MyClass : IMyProtocol
{
    void Method ()
    {
    }
}
```

Questo codice Esporta `IMyProtocol` in Objective-C come protocollo denominato `MyProtocol` e una classe denominata `MyClass` che implementa il protocollo.

## <a name="new-registration-system"></a>Nuovo sistema di registrazione

A partire dalla versione stabile di 6.2.6 e dalla versione beta di 6.3.4, è stato aggiunto un nuovo registrar statico. Nella versione 7.2.1, il nuovo registrar è stato impostato come predefinito.

Questo nuovo sistema di registrazione offre le seguenti nuove funzionalità:

- Rilevamento in fase di compilazione degli errori del programmatore:
  - Due classi registrate con lo stesso nome.
  - Più di un metodo esportato per rispondere allo stesso selettore
- Rimozione del codice nativo non usato:
  - Il nuovo sistema di registrazione aggiungerà riferimenti sicuri al codice usato nelle librerie statiche, consentendo al linker nativo di rimuovere il codice nativo inutilizzato dal file binario risultante. Nelle associazioni di esempio di Novell, la maggior parte delle applicazioni diventa almeno 300K più piccola.

- Supporto per sottoclassi generiche di `NSObject`; Per ulteriori informazioni, vedere [generics NSObject](~/ios/internals/api-design/nsobject-generics.md) . Inoltre, il nuovo sistema di registrazione rileverà costrutti generici non supportati che in precedenza avrebbero causato un comportamento casuale in fase di esecuzione.

### <a name="errors-caught-by-the-new-registrar"></a>Errori rilevati dal nuovo registrar

Di seguito sono riportati alcuni esempi degli errori rilevati dal nuovo registrar.

- Esportazione dello stesso selettore più di una volta nella stessa classe:

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo:")]
      void Foo (NSString str);
      [Export ("foo:")]
      void Foo (string str)
  }
  ```

- Esportazione di più di una classe gestita con lo stesso nome Objective-C:

  ```csharp
  [Register ("Class")]
  class MyClass : NSObject {}

  [Register ("Class")]
  class YourClass : NSObject {}
  ```

- Esportazione di metodi generici:

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo")]
      void Foo<T> () {}
  }
  ```

### <a name="limitations-of-the-new-registrar"></a>Limitazioni del nuovo registrar

Alcuni aspetti da tenere presenti sul nuovo Registrar:

- Alcune librerie di terze parti devono essere aggiornate per funzionare con il nuovo sistema di registrazione. Per ulteriori informazioni, vedere le [modifiche richieste](#required-modifications) di seguito.

- Un aspetto negativo a breve termine è anche che è necessario usare Clang se viene usato il Framework degli account (questo è dovuto al fatto che l'intestazione **accounts. h** di Apple può essere compilata solo da Clang). Aggiungere `--compiler:clang` agli argomenti aggiuntivi di mTouch per usare Clang se si usa Xcode 4,6 o versione precedente (Xamarin.iOS selezionerà automaticamente Clang in Xcode 5,0 o versione successiva).

- Se si usa Xcode 4,6 (o versioni precedenti), è necessario selezionare GCC/G + + se i nomi dei tipi esportati contengono caratteri non ASCII, perché la versione di Clang fornita con Xcode 4,6 non supporta caratteri non ASCII all'interno degli identificatori del codice Objective-C. Aggiungere `--compiler:gcc` agli argomenti aggiuntivi di mTouch per usare GCC.

## <a name="selecting-a-registrar"></a>Selezione di un registrar

È possibile selezionare un registrar diverso aggiungendo una delle opzioni seguenti agli argomenti aggiuntivi di mTouch nelle impostazioni di **compilazione iOS** del progetto:

- `--registrar:static`: predefinito per le compilazioni di dispositivi
- `--registrar:dynamic`: predefinito per le compilazioni del simulatore

> [!NOTE]
> Il API classica di Novell supportava altre opzioni, ad esempio `--registrar:legacystatic` e `--registrar:legacydynamic`. Tuttavia, queste opzioni non sono supportate dal API unificata.

## <a name="shortcomings-in-the-old-registration-system"></a>Carenze nel vecchio sistema di registrazione

Il sistema di registrazione precedente presenta i seguenti svantaggi:

- Non esisteva un riferimento statico (nativo) a classi e metodi Objective-C in librerie native di terze parti, il che significava che non potevamo richiedere al linker nativo di rimuovere codice nativo di terze parti che non veniva effettivamente usato (poiché tutti gli elementi verrebbero rimossi). Questo è il motivo del `-force_load libNative.a` che ogni binding di terze parti doveva eseguire (o l'equivalente `ForceLoad=true` nell'attributo `[LinkWith]`).
- È possibile esportare due tipi gestiti con lo stesso nome Objective-C senza alcun avviso. Uno scenario raro consisteva nel finire con due classi `AppDelegate` in spazi dei nomi diversi. In fase di esecuzione sarebbe stato completamente casuale quale è stato selezionato (in effetti, varia tra le esecuzioni di un'app che non è stata ancora ricompilata, che è stata creata per un'esperienza di debug molto sconcertante e frustrante).
- È possibile esportare due metodi con la stessa firma Objective-C. Ancora una volta, che verrebbe chiamato da Objective-C era casuale (ma questo problema non era comune come quello precedente, soprattutto perché l'unico modo per sperimentare effettivamente questo bug era quello di eseguire l'override del metodo gestito sfortunato).
- Il set di metodi che è stato esportato è leggermente diverso tra le compilazioni dinamiche e statiche.
- Non funziona correttamente quando si esportano classi generiche (l'implementazione generica esatta eseguita in fase di esecuzione sarebbe casuale, causando in effetti un comportamento non determinato).

<a name="required-modifications" />

## <a name="new-registrar-required-changes-to-bindings"></a>Nuovo Registrar: modifiche obbligatorie alle associazioni

In questa sezione vengono descritte le modifiche dei binding che devono essere apportate per poter utilizzare il nuovo registrar.

### <a name="protocols-must-have-the-protocol-attribute"></a>I protocolli devono avere l'attributo [Protocol]

I protocolli devono ora avere l'attributo `[Protocol]`. In caso contrario, si otterrà un errore nativo del linker, ad esempio:

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>I selettori devono avere un numero di parametri valido

Tutti i selettori devono indicare correttamente il numero di parametri. In precedenza questi errori venivano ignorati e potevano causare problemi di Runtime.

In breve, il numero di due punti deve corrispondere al numero di parametri:

- Nessun parametro: `foo`
- Un parametro: `foo:`
- Due parametri: `foo:parameterName2:`

Gli utilizzi seguenti non sono corretti:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Usa il parametro IsVariadic nell'esportazione

Le funzioni Variadic devono usare l'argomento `IsVariadic` per l'attributo `[Export]`:

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Deve essere collegato a simboli esistenti

Non è possibile associare classi che non esistono nella libreria nativa.
Se una classe è stata rimossa o rinominata nella libreria nativa, assicurarsi di aggiornare i binding in modo che corrispondano.
