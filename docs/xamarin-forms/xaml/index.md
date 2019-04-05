---
title: eXtensible Application Markup Language (XAML)
description: XAML è un linguaggio di markup dichiarativo che possa essere usato per definire le interfacce utente. L'interfaccia utente è definito in un file XML usando la sintassi XAML, mentre il comportamento di runtime viene definito in un file code-behind distinto.
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/03/2019
ms.openlocfilehash: 1f6bdfb1aed87f8a2dabdc0efcea3a1b8bf2ecf2
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019347"
---
# <a name="extensible-application-markup-language-xaml"></a>eXtensible Application Markup Language (XAML)

_XAML è un linguaggio di markup dichiarativo che possa essere usato per definire le interfacce utente. L'interfaccia utente è definito in un file XML usando la sintassi XAML, mentre il comportamento di runtime viene definito in un file code-behind distinto._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evoluzione 2016: Diventando un Master di XAML**

> [!NOTE]
> Provare il [anteprima Standard XAML](standard/index.md)

## [<a name="xaml-controls"></a>Controlli XAML](xaml-controls.md)

Tutte le viste definite in xamarin. Forms possono farvi riferimento dai file XAML.

<a name="xaml" />

## [<a name="xaml-basics"></a>Nozioni di base su XAML](xaml-basics/index.md)

XAML consente agli sviluppatori di definire le interfacce utente nelle applicazioni xamarin. Forms tramite markup anziché codice. XAML non è mai obbligatorio in un programma di xamarin. Forms, ma è ricco di strumenti e spesso è più coerente visivamente e più concisa rispetto al codice equivalente. XAML è particolarmente adatto per l'uso con l'architettura dell'applicazione Model-View-ViewModel (MVVM) più diffusi: XAML definisce la vista collegata al ViewModel codice tramite le associazioni di dati basati su XAML.

## [<a name="xaml-compilation"></a>Compilazione XAML](xamlc.md)

Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC). Questo articolo descrive come usare XAMLC e i relativi vantaggi.

## [<a name="xaml-previewer"></a>Visualizzatore anteprima XAML](xaml-previewer/index.md)

Il [Visualizzatore anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) esegue il rendering di un'anteprima in tempo reale di una pagina side-by-side con il markup XAML, consentendo di visualizzare l'interfaccia utente sottoposta a rendering durante la digitazione.

## [<a name="xaml-namespaces"></a>Spazi dei nomi XAML](namespaces.md)

XAML Usa le `xmlns` attributo XML per le dichiarazioni dello spazio dei nomi. Questo articolo presenta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso.

## [<a name="xaml-custom-namespace-schemas"></a>Schemi spazio dei nomi personalizzati XAML](custom-namespace-schemas.md)

Uno schema personalizzato dello spazio dei nomi XAML può essere definito con la `XmlnsDefinitionAttribute` (classe), che specifica un mapping tra un URL personalizzato e uno o più spazi dei nomi CLR. Schema spazio dei nomi personalizzato è quindi utilizzabile nelle dichiarazioni dello spazio dei nomi XAML.

## [<a name="xaml-namespace-recommended-prefixes"></a>Prefissi consigliati per lo spazio dei nomi XAML](custom-prefix.md)

Il `XmlnsPrefixAttribute` classe può essere utilizzata dagli autori di controlli per specificare un prefisso consigliato da associare a uno spazio dei nomi XAML, per l'utilizzo XAML.

## [<a name="xaml-markup-extensions"></a>Estensioni di markup XAML](markup-extensions/index.md)

XAML include le estensioni di markup per impostare gli attributi su oggetti oltre quelle che possono essere espresse con semplici stringhe o valori. Ad esempio che fanno riferimento a costanti, le proprietà statiche e i campi, i dizionari risorse e le associazioni dati.

## [<a name="field-modifiers"></a>Modificatori di campo](field-modifiers.md)

Il `x:FieldModifier` namespace (attributo) specifica il livello di accesso per i campi generati per gli elementi denominati XAML.

## [<a name="passing-arguments"></a>Passaggio di argomenti](passing-arguments.md)

XAML può essere usato per passare argomenti per i costruttori non predefiniti o ai metodi factory. Questo articolo viene illustrato come utilizzare gli attributi XAML che possono essere usati per passare gli argomenti ai costruttori, per chiamare i metodi factory e per specificare il tipo di argomento generico.

## [<a name="bindable-properties"></a>Proprietà associabili](bindable-properties.md)

In xamarin. Forms, la funzionalità delle proprietà di common language runtime (CLR) viene estesa dalla proprietà associabili. Una proprietà associabile è un tipo speciale di proprietà, in cui il valore della proprietà viene rilevato dal sistema di proprietà di xamarin. Forms. Questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e il loro uso.

## [<a name="attached-properties"></a>Proprietà associate](attached-properties.md)

Una proprietà associata è un tipo speciale di proprietà associabile, definito in una classe ma collegato ad altri oggetti e riconoscibile in XAML come attributo che contiene una classe e un nome di proprietà separati da un punto. Questo articolo fornisce un'introduzione alle proprietà associate e viene illustrato come creare e il loro uso.

## [<a name="resource-dictionaries"></a>Dizionari di risorse](resource-dictionaries.md)

Risorse XAML sono definizioni di oggetti che possono essere utilizzati più di una volta. Oggetto [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) consente alle risorse di essere definito in un'unica posizione e usata nuovamente in un'applicazione xamarin. Forms. Questo articolo illustra come creare e utilizzare un `ResourceDictionary`e su come unire un `ResourceDictionary` in un altro.

## [<a name="loading-xaml-at-runtime"></a>Caricamento di XAML in fase di esecuzione](runtime-load.md)

XAML può essere caricato e analizzato in fase di esecuzione con il [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) i metodi di estensione.
