---
title: Modificatori di campo XAML in xamarin. Forms
description: L'attributo namespace X:FieldModifier specifica il livello di accesso per i campi generati per elementi XAML denominati.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209506"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificatori di campo XAML in xamarin. Forms

_Il `x:FieldModifier` attributo namespace specifica il livello di accesso per i campi generati per elementi XAML denominati._

## <a name="overview"></a>Panoramica

I valori validi dell'attributo sono:

- `Public` : Specifica che il campo generato per l'elemento XAML `public`.
- `NotPublic` : Specifica che il campo generato per l'elemento XAML `internal` all'assembly.

Se il valore dell'attributo non è impostato, il campo generato per l'elemento sarà `private`.

Le condizioni seguenti devono essere soddisfatti per un `x:FieldModifier` attributo da elaborare:

- Elemento di primo livello XAML deve essere un valore valido `x:Class`.
- L'elemento XAML corrente ha un `x:Name` specificato.

Il codice XAML seguente vengono illustrati esempi di impostazione dell'attributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> Il `x:FieldModifier` attributo non può essere utilizzato per specificare il livello di accesso di una classe XAML.
