---
title: eXtensible Application Markup Language (XAML)
description: XAML è un linguaggio di markup dichiarativo che può essere usato per definire le interfacce utente. L'interfaccia utente è definito in un file XML utilizzando la sintassi XAML, mentre il comportamento di runtime viene definito in un file code-behind separato.
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/18/2018
ms.openlocfilehash: c040c12829708418d0a705b8e9f930989900c678
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209427"
---
# <a name="extensible-application-markup-language-xaml"></a>eXtensible Application Markup Language (XAML)

_XAML è un linguaggio di markup dichiarativo che può essere usato per definire le interfacce utente. L'interfaccia utente è definito in un file XML utilizzando la sintassi XAML, mentre il comportamento di runtime viene definito in un file code-behind separato._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evoluzione 2016: Diventando uno schema XAML**

> [!NOTE]
> Provare il [anteprima Standard XAML](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[Nozioni di base su XAML](xaml-basics/index.md)

XAML consente agli sviluppatori di definire le interfacce utente nelle applicazioni di xamarin. Forms usando markup anziché il codice. XAML non è mai necessario in un programma xamarin. Forms ma ricco ed è spesso più coerente visivamente e più concisa rispetto al codice equivalente. XAML è particolarmente adatto per l'utilizzo con l'architettura dell'applicazione Model-View-ViewModel (MVVM) più diffusi: XAML definisce la vista che è collegata al codice ViewModel le associazioni di dati basate su XAML.

## <a name="xaml-compilationxamlcmd"></a>[Compilazione XAML](xamlc.md)

Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC). In questo articolo viene descritto come utilizzare XAMLC e i vantaggi offerti.

## <a name="xaml-previewerxaml-previewermd"></a>[Visualizzatore anteprima XAML](xaml-previewer.md)

Il [anteprima XAML](~/xamarin-forms/xaml/xaml-previewer.md) annunciati in Xamarin evolvere 2016 è disponibile per il test del canale alfa.

## <a name="xaml-namespacesnamespacesmd"></a>[Spazi dei nomi XAML](namespaces.md)

XAML Usa il `xmlns` attributo XML per le dichiarazioni dello spazio dei nomi. In questo articolo presenta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso.

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[Estensioni di markup XAML](markup-extensions/index.md)

XAML include le estensioni di markup per impostare gli attributi su oggetti oltre a ciò che può essere espresso con semplici stringhe o valori. Questi includono costanti, le proprietà statiche e campi, dizionari risorse e le associazioni di dati di riferimento.

## <a name="field-modifiersfield-modifiersmd"></a>[Modificatori di campo](field-modifiers.md)

Il `x:FieldModifier` attributo namespace specifica il livello di accesso per i campi generati per elementi XAML denominati.

## <a name="passing-argumentspassing-argumentsmd"></a>[Passaggio di argomenti](passing-arguments.md)

XAML può essere usato per passare argomenti a costruttori non predefiniti o ai metodi factory. In questo articolo viene illustrato l'utilizzo di attributi XAML che possono essere utilizzati per passare argomenti a costruttori, per chiamare i metodi factory e per specificare il tipo di argomento generico.

## <a name="bindable-propertiesbindable-propertiesmd"></a>[Proprietà associabili](bindable-properties.md)

In xamarin. Forms, viene estesa la funzionalità delle proprietà di common language runtime (CLR) dalle proprietà associabile. Una proprietà associabile è un tipo speciale di proprietà, in cui il valore della proprietà viene rilevato dal sistema di proprietà di xamarin. Forms. In questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e utilizzarli.

## <a name="attached-propertiesattached-propertiesmd"></a>[Proprietà associate](attached-properties.md)

Una proprietà associata è un tipo speciale di proprietà associabile, definito in una classe ma collegato ad altri oggetti e riconoscibile in XAML come un attributo che contiene una classe e un nome di proprietà separati da un punto. In questo articolo viene fornita un'introduzione alle proprietà associata e viene illustrato come creare e utilizzarli.

## <a name="resource-dictionariesresource-dictionariesmd"></a>[Dizionari di risorse](resource-dictionaries.md)

Risorse XAML sono definizioni di oggetti che possono essere utilizzati più volte. Oggetto [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) risorse siano definiti in un'unica posizione e riutilizzate in un'applicazione di xamarin. Forms. In questo articolo viene illustrato come creare e utilizzare un `ResourceDictionary`e come unire un `ResourceDictionary` in un altro.
