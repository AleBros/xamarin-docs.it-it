---
title: Tipi nativi
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 4d11d053cf4471a98cbba0f7c97be3bef39276fb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="native-types"></a>Tipi nativi

Alla base della differenza, API iOS e Mac utilizzare un tipo di dati specifici dell'architettura che è sempre a 32 bit su piattaforme a 32 bit e a 64 bit su piattaforme a 64 bit.

Ad esempio, Objective-C esegue il mapping di `NSInteger` tipo di dati `int32_t` nei sistemi a 32 bit e a `int64_t` nei sistemi a 64 bit.

In base a questo comportamento, nell'API unificata, viene sostituito utilizzi precedenti del `int` (che in .NET è definito come costante `System.Int32`) in un nuovo tipo di dati: `System.nint`.  È possibile considerare "n" come significato "native", pertanto di tipo integer nativo della piattaforma.

Con questi nuovi tipi di dati, lo stesso codice sorgente viene compilato per 32 bit, a 32 bit e 64 bit o 64 bit, a seconda del flag di compilazione.

## <a name="new-data-types"></a>Nuovi tipi di dati

Nella tabella seguente mostra le modifiche nei nostri tipi di dati per la corrispondenza di questo nuovo ambiente a 32/64 bit:

|Tipo nativo|tipo di supporto a 32 bit|tipo di supporto a 64 bit|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Si è scelto di tali nomi per consentire al codice c# per la ricerca più o meno la medesima aspetto oggi.

### <a name="implicit-and-explicit-conversions"></a>Conversioni implicite ed esplicite

I nuovi tipi di dati è progettata per consentire a un singolo file sorgente c# naturalmente utilizzo dell'archiviazione a bit 32 o 64 a seconda di piattaforma host e le impostazioni di compilazione.

Questa operazione necessaria per progettare un set di conversioni implicite ed esplicite da e verso i tipi di dati specifici della piattaforma per i tipi di dati .NET integrali e a virgola mobile.

Operatori di conversioni implicite vengono forniti quando non vi è alcuna possibilità di perdita dei dati (a 32 bit i valori archiviati in uno spazio a 64 bit).

Operatori di conversioni esplicite vengono forniti quando è presente un potenziale perdita di dati (valore a 64 bit viene archiviato in un percorso di archiviazione 32 o potenzialmente 32).

 `int`, `uint` e `float` sono tutti in modo implicito nel `nint`, `nuint` e `nfloat` come 32 bit si adatteranno 32 o 64 bit.

 `nint`, `nuint` e `nfloat` sono tutti in modo implicito nel `long`, `ulong` e `double` come valori a 32 o 64 bit si adatteranno nel servizio di archiviazione a 64 bit.

È necessario utilizzare le conversioni esplicite da `nint`, `nuint` e `nfloat` in `int`, `uint` e `float` poiché i tipi nativi potrebbero contenere 64 bit di spazio di archiviazione.

È necessario utilizzare le conversioni esplicite da `long`, `ulong` e `double` in `nint`, `nuint` e `nfloat` poiché i tipi nativi solo potrebbero essere in grado di contenere 32 bit di archiviazione.

## <a name="coregraphics-types"></a>Tipi di CoreGraphics

I tipi di dati di punto, le dimensioni e rettangolo utilizzati con CoreGraphics utilizzano 32 o 64 bit, a seconda del dispositivo in che sono in esecuzione.  Quando è associato in origine la iOS e Mac API utilizzassimo strutture di dati esistenti che si sono verificati in modo che corrisponda le dimensioni della piattaforma host (i tipi di dati `System.Drawing`).

Quando si passa a **unificato**, sarà necessario sostituire le istanze di `System.Drawing` con i relativi `CoreGraphics` controparti come illustrato nella tabella seguente:

|Tipo precedente in System. Drawing|Nuovo CoreGraphics di tipo di dati|Descrizione|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Contiene informazioni sul rettangolo punto mobile.|
|`SizeF`|`CGSize`|Punto di mobile contiene informazioni sulle dimensioni (larghezza, altezza)|
|`PointF`|`CGPoint`|Contiene una virgola mobile, punto (X, Y), informazioni|

I valori float utilizzati tipi di dati precedente per memorizzare gli elementi delle strutture di dati, mentre il nuovo uno Usa `System.nfloat`.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
