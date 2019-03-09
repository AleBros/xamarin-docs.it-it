---
title: Tipo Registrar per xamarin. IOS
description: Questo documento descrive le registrar di tipo xamarin. IOS, che rende C# le classi disponibili per il runtime di Objective-C.
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/29/2018
ms.openlocfilehash: 83340ce2d5db145c29166d90d3a5180b1767d7ca
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672495"
---
# <a name="type-registrar-for-xamarinios"></a>Programma di registrazione di tipo per xamarin. IOS

Questo documento descrive il sistema di registrazione tipo usato da xamarin. IOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registrazione dei metodi e le classi gestite

Durante l'avvio, registrerà xamarin. ios:

- Le classi con una [[registrare]](xref:Foundation.RegisterAttribute) attributo come classi di Objective-C.
- Le classi con una [[Category]](xref:ObjCRuntime.CategoryAttribute) attributo come categorie di Objective-C.
- Interfaccia con un [[Protocol]](xref:Foundation.ProtocolAttribute) attributo come protocolli di Objective-C.
- I membri con un' [[esportare]](xref:Foundation.ExportAttribute), rendendo possibile per Objective-C per accedervi.

Ad esempio, prendere in considerazione il gestito `Main` metodo comune nelle applicazioni xamarin. ios:

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

Questo codice indica al runtime di Objective-C di usare il tipo denominato `AppDelegate` come classe delegata dell'applicazione. Il runtime di Objective-C deve essere in grado di creare un'istanza di C# `AppDelegate` classe, che deve essere registrata classe.

Xamarin. IOS registrazione vengono eseguite automaticamente, in fase di esecuzione (registrazione dinamica) o in fase di compilazione (registrazione statico).

Registrazione dinamica Usa la reflection all'avvio per trovare tutte le classi e i metodi di registrazione, passarli al runtime di Objective-C. La registrazione dinamica viene utilizzata per impostazione predefinita per le compilazioni del simulatore.

Registrazione statico controlla se, in fase di compilazione, gli assembly usati dall'applicazione. Determina le classi e i metodi di registrazione con Objective-C e genera una mappa, che viene incorporata in un file binario.
Quindi, all'avvio, registra la mappa con il runtime di Objective-C. La registrazione statica viene utilizzata per compilazioni del dispositivo.

### <a name="categories"></a>Categories

A partire da xamarin. IOS 8.10, è possibile creare categorie di Objective-C tramite C# sintassi.

Per creare una categoria, usare il `[Category]` attributo e specificare il tipo da estendere. Ad esempio, il codice seguente estende `NSString`:

```csharp
[Category (typeof (NSString))]
```

Ognuno dei metodi di una categoria ha un `[Export]` attributo, rendendolo disponibile per il runtime di Objective-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Tutti i metodi di estensione gestita devono essere statici, ma è possibile creare metodi di istanza di Objective-C usando lo standard C# sintassi per i metodi di estensione:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

Il primo argomento al metodo di estensione è l'istanza in cui è stato richiamato il metodo:

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

Questo esempio si aggiungerà nativo `toUpper` al metodo di istanza di `NSString` classe. Questo metodo può essere chiamato da Objective-c:

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

A partire da xamarin. IOS 8.10, si interfaccia con il `[Protocol]` attributi verranno esportati in Objective-C come protocolli:

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

Questo codice consente di esportare `IMyProtocol` Objective-c come un protocollo chiamato `MyProtocol` e una classe denominata `MyClass` che implementa il protocollo.

## <a name="new-registration-system"></a>Nuovo sistema di registrazione

A partire dal 6.2.6 stabile versione e la versione beta 6.3.4, è stato aggiunto il nuovo programma di registrazione statico. Nel 7.2.1 versione, abbiamo le registrar di nuovo il valore predefinito.

Questo nuovo sistema di registrazione offre le nuove funzionalità seguenti:

- Rilevamento in fase di compilazione di errori di programmazione:
    - Due classi da registrare con lo stesso nome.
    - Più di un metodo esportato per rispondere allo stesso selettore
- Rimozione del codice nativo inutilizzata:
    - Il nuovo sistema di registrazione aggiungerà i riferimenti forti al codice usato in librerie statiche, che consente al linker nativo di eliminare le codice nativo non utilizzato dal file binario risulta. Nelle associazioni di esempio di Xamarin, la maggior parte delle applicazioni diventano più piccoli almeno 300 KB.

- Supporto per sottoclassi generiche di `NSObject`; vedere [Generics NSObject](~/ios/internals/api-design/nsobject-generics.md) per altre informazioni. Il nuovo sistema di registrazione verrà inoltre rilevato costrutti generici non supportati che avrebbe causato precedentemente comportamento casuale in fase di esecuzione.

### <a name="errors-caught-by-the-new-registrar"></a>Colti di nuovo registro errori

Di seguito sono riportati alcuni esempi dei messaggi di errore rilevati dal programma di registrazione nuova.

- Esportazione di più volte lo stesso selettore nella stessa classe:

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

- Esportazione di più di una classe gestita con lo stesso nome di Objective-C:

    ```csharp
    [Register ("Class")]
    class MyClass : NSObject {}

    [Register ("Class")]
    class YourClass : NSObject {}
    ```

- Esportando i metodi generici:

    ```csharp
    [Register]
    class MyDemo : NSObject
    {
        [Export ("foo")]
        void Foo<T> () {}
    }
    ```

### <a name="limitations-of-the-new-registrar"></a>Limitazioni della funzione di registrazione nuovo

Alcuni aspetti da tenere in mente riguardo il nuovo programma di registrazione:

- Alcune librerie di terze parti devono essere aggiornate per funzionare con il nuovo sistema di registrazione. Visualizzare [necessarie modifiche](#required-modifications) sotto per altri dettagli.

- È anche un risvolto a breve termine che Clang deve essere utilizzata se viene usato il framework di account (infatti Apple **accounts.h** intestazione può essere compilata solo da Clang). Aggiungere `--compiler:clang` agli argomenti aggiuntivi di mtouch usare Clang se si usa Xcode 4.6 o versioni precedenti (xamarin. IOS selezionerà automaticamente Clang in Xcode 5.0 o versioni successive.)

- Se Xcode 4.6 (o versioni precedenti) viene utilizzato, GCC / G + + è necessario selezionare se tipo esportato i nomi contengono caratteri non ASCII (questo è perché la versione di Clang fornito con Xcode 4.6 non supporta caratteri non ASCII all'interno degli identificatori nel codice Objective-C). Aggiungere `--compiler:gcc` agli argomenti aggiuntivi di mtouch usare GCC.

## <a name="selecting-a-registrar"></a>Selezionando un registrar

È possibile selezionare un altro registrar mediante l'aggiunta di una delle opzioni seguenti agli argomenti di progetto aggiuntivi di mtouch **compilazione iOS** impostazioni:

- `--registrar:static` – default per le compilazioni di dispositivo
- `--registrar:dynamic` : l'impostazione predefinita per le compilazioni del simulatore

> [!NOTE]
> API classica di Xamarin supportate altre opzioni, ad esempio `--registrar:legacystatic` e `--registrar:legacydynamic`. Tuttavia, queste opzioni non sono supportate dall'API unificata.

## <a name="shortcomings-in-the-old-registration-system"></a>Limitazioni del vecchio sistema di registrazione

Il sistema di registrazione precedente presenta i seguenti svantaggi:

- Si è verificato alcun riferimento statico (nativo) alle classi di Objective-C e i metodi nelle librerie native di terze parti, che significava che è stato possibile chiedere al linker nativo di rimuovere il codice nativo terze parti che non è stato effettivamente usato (perché tutto ciò che potrebbe venire rimosso). Questo è il motivo per il `-force_load libNative.a` che ogni binding di terze parti di iniziare subito a (o equivalente `ForceLoad=true` nel `[LinkWith]` attributo).
- È possibile esportare due tipi gestiti con lo stesso nome di Objective-C senza alcun avviso. È stata uno scenario raro ritrovarsi con due `AppDelegate` classi negli spazi dei nomi diversi. In fase di esecuzione sarebbe completamente casuale quello che è stato selezionato (in effetti, che consentono di variare tra le esecuzioni di un'app che non è stato ricompilato anche - effettuato per un'esperienza di debug molto può sembrare sconcertante e frustrante).
- È possibile esportare due metodi con la stessa firma di Objective-C. Anche in questo caso quello che viene chiamato da Objective-C è casuale, ma questo problema non è stato operazione tanto comune quanto quello precedente, principalmente perché l'unico modo in cui si verifica effettivamente questo bug è stato l'override del metodo gestito sfortunato.
- Il set di metodi che è stato esportato è leggermente diverso tra compilazioni statiche e dinamiche.
- Non funziona correttamente quando si esportano le classi generiche (quale implementazione generica esatta eseguita in fase di esecuzione sarebbe random, producendo un comportamento non determinato).

<a name="required-modifications" />

## <a name="new-registrar-required-changes-to-bindings"></a>Nuovo programma di registrazione: ha richiesto modifiche alle associazioni

In questa sezione vengono descritte le modifiche di associazioni che devono essere apportate per funzionare con il nuovo programma di registrazione.

### <a name="protocols-must-have-the-protocol-attribute"></a>Protocolli devono avere l'attributo [Protocol]

Protocolli ora è necessario disporre di `[Protocol]` attributo. Se non eseguire questa operazione, sarà possibile un errore del linker nativo, ad esempio:

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>I selettori devono avere un numero di parametri valido

Tutti i selettori devono indicare in modo corretto numero di parametri. In precedenza, questi errori sono stati ignorati e potrebbe causare problemi di runtime.

In breve, il numero di punti e deve corrispondere al numero di parametri:

- Senza parametri: `foo`
- Un parametro: `foo:`
- Due parametri: `foo:parameterName2:`

Di seguito sono gli usi:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Utilizzare il parametro IsVariadic nell'esportazione

Funzioni Variadic devono usare la `IsVariadic` argomento per il `[Export]` attributo:

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Devono essere collegate ai simboli esistenti

Non è possibile associare le classi che non esistono nella libreria nativa.
Se una classe è stato rimossa dal o rinominata nella libreria nativa, assicurarsi di aggiornare le associazioni in modo che corrispondano.
