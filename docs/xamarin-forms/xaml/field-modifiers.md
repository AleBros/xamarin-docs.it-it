---
title: Modificatori di campo XAML in Novell. Forms
description: L'attributo dello spazio dei nomi x:FieldModifier specifica il livello di accesso per i campi generati per gli elementi XAML denominati.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/02/2019
ms.openlocfilehash: 0f6050de943ca9878cf41b448d44bf222689be56
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739455"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificatori di campo XAML in Novell. Forms

L' `x:FieldModifier` attributo Namespace specifica il livello di accesso per i campi generati per gli elementi XAML denominati. I valori validi dell'attributo sono:

- `private`: specifica che il campo generato per l'elemento XAML è accessibile solo all'interno del corpo della classe in cui è dichiarato.
- `public`: specifica che il campo generato per l'elemento XAML non presenta restrizioni di accesso.
- `protected`: specifica che il campo generato per l'elemento XAML è accessibile all'interno della relativa classe e dalle istanze delle classi derivate.
- `internal`: specifica che il campo generato per l'elemento XAML è accessibile solo all'interno dei tipi nello stesso assembly.
- `notpublic`: specifica che il campo generato per l'elemento XAML è accessibile solo all'interno dei tipi nello stesso assembly.

Per impostazione predefinita, se il valore dell'attributo non è impostato, il campo generato per l'elemento sarà `private`.

> [!NOTE]
> Il valore dell'attributo può utilizzare qualsiasi combinazione di maiuscole e minuscole, in quanto verrà convertito in minuscolo da Novell. Forms.

Per elaborare un `x:FieldModifier` attributo, è necessario che siano soddisfatte le condizioni seguenti:

- L'elemento XAML di primo livello deve essere un oggetto `x:Class`valido.
- L'elemento XAML corrente ha un `x:Name` oggetto specificato.

Il codice XAML seguente mostra esempi di impostazione dell'attributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> L' `x:FieldModifier` attributo non può essere usato per specificare il livello di accesso di una classe XAML.
