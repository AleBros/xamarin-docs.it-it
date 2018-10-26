---
title: eXtensible Application Markup Language (XAML)
description: XAML è un linguaggio di markup dichiarativo che possa essere usato per definire le interfacce utente. L'interfaccia utente è definito in un file XML usando la sintassi XAML, mentre il comportamento di runtime viene definito in un file code-behind distinto.
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/18/2018
ms.openlocfilehash: 4045f3a1d31b1c0c8b69e840d3943b6ce258b894
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113586"
---
# <a name="extensible-application-markup-language-xaml"></a>eXtensible Application Markup Language (XAML)

_XAML è un linguaggio di markup dichiarativo che possa essere usato per definire le interfacce utente. L'interfaccia utente è definito in un file XML usando la sintassi XAML, mentre il comportamento di runtime viene definito in un file code-behind distinto._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evoluzione 2016: Sta diventando uno schema XAML**

> [!NOTE]
> Provare il [anteprima Standard XAML](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[Nozioni di base su XAML](xaml-basics/index.md)

XAML consente agli sviluppatori di definire le interfacce utente nelle applicazioni xamarin. Forms tramite markup anziché codice. XAML non è mai obbligatorio in un programma di xamarin. Forms, ma è ricco di strumenti e spesso è più coerente visivamente e più concisa rispetto al codice equivalente. XAML è particolarmente adatto per l'uso con l'architettura dell'applicazione Model-View-ViewModel (MVVM) più diffusi: XAML definisce la vista collegata al ViewModel codice tramite le associazioni di dati basati su XAML.

## <a name="xaml-compilationxamlcmd"></a>[Compilazione XAML](xamlc.md)

Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC). Questo articolo descrive come usare XAMLC e i relativi vantaggi.

## <a name="xaml-previewerxaml-previewermd"></a>[Visualizzatore anteprima XAML](xaml-previewer.md)

Il [Visualizzatore anteprima XAML](~/xamarin-forms/xaml/xaml-previewer.md) esegue il rendering di un'anteprima in tempo reale di una pagina side-by-side con il markup XAML, consentendo di visualizzare l'interfaccia utente sottoposta a rendering durante la digitazione.

## <a name="xaml-namespacesnamespacesmd"></a>[Spazi dei nomi XAML](namespaces.md)

XAML Usa le `xmlns` attributo XML per le dichiarazioni dello spazio dei nomi. Questo articolo presenta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso.

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[Estensioni di markup XAML](markup-extensions/index.md)

XAML include le estensioni di markup per impostare gli attributi su oggetti oltre quelle che possono essere espresse con semplici stringhe o valori. Ad esempio che fanno riferimento a costanti, le proprietà statiche e i campi, i dizionari risorse e le associazioni dati.

## <a name="field-modifiersfield-modifiersmd"></a>[Modificatori di campo](field-modifiers.md)

Il `x:FieldModifier` namespace (attributo) specifica il livello di accesso per i campi generati per gli elementi denominati XAML.

## <a name="passing-argumentspassing-argumentsmd"></a>[Passaggio di argomenti](passing-arguments.md)

XAML può essere usato per passare argomenti per i costruttori non predefiniti o ai metodi factory. Questo articolo viene illustrato come utilizzare gli attributi XAML che possono essere usati per passare gli argomenti ai costruttori, per chiamare i metodi factory e per specificare il tipo di argomento generico.

## <a name="bindable-propertiesbindable-propertiesmd"></a>[Proprietà associabili](bindable-properties.md)

In xamarin. Forms, la funzionalità delle proprietà di common language runtime (CLR) viene estesa dalla proprietà associabili. Una proprietà associabile è un tipo speciale di proprietà, in cui il valore della proprietà viene rilevato dal sistema di proprietà di xamarin. Forms. Questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e il loro uso.

## <a name="attached-propertiesattached-propertiesmd"></a>[Proprietà associate](attached-properties.md)

Una proprietà associata è un tipo speciale di proprietà associabile, definito in una classe ma collegato ad altri oggetti e riconoscibile in XAML come attributo che contiene una classe e un nome di proprietà separati da un punto. Questo articolo fornisce un'introduzione alle proprietà associate e viene illustrato come creare e il loro uso.

## <a name="resource-dictionariesresource-dictionariesmd"></a>[Dizionari di risorse](resource-dictionaries.md)

Risorse XAML sono definizioni di oggetti che possono essere utilizzati più di una volta. Oggetto [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) consente alle risorse di essere definito in un'unica posizione e usata nuovamente in un'applicazione xamarin. Forms. Questo articolo illustra come creare e utilizzare un `ResourceDictionary`e su come unire un `ResourceDictionary` in un altro.
