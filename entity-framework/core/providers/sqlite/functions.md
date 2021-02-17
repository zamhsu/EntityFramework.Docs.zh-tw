---
title: 函數對應-SQLite 資料庫提供者-EF Core
description: SQLite EF Core 資料庫提供者的函式對應
author: bricelam
ms.date: 1/26/2021
uid: core/providers/sqlite/functions
ms.openlocfilehash: 66fb24219465b8c34407d279450b7d501e7a572d
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543207"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>SQLite EF Core 提供者的函式對應

此頁面會顯示在使用 SQLite 提供者時，哪些 .NET 成員會轉譯成哪些 SQL 函數。

## <a name="binary-functions"></a>二進位函式

.NET                                           | SQL                                  | 加入
---------------------------------------------- | ------------------------------------ | --------
位元組。包含 (值)                           | instr (@bytes 、char (@value) ) > 0      | EF Core 5.0
位元組。長度                                   | 長度 (@bytes)                        | EF Core 5.0
位元組。Enumerable.sequenceequal (第二個)                     | @bytes = @second                     | EF Core 5.0
英 孚。函數。 Hex (位元組)                         | hex (@bytes)                           | EF Core 6.0
英 孚。函數。 Substr (bytes，startIndex)          | substr (@bytes ， @startIndex)           | EF Core 6.0
英 孚。函數。 Substr (位元組、startIndex、長度)  | substr (@bytes 、 @startIndex @length)  | EF Core 6.0

## <a name="conversion-functions"></a>轉換函數

.NET                      | SQL                           | 加入
------------------------- | ----------------------------- | --------
boolValue ToString ()       | 將 (轉換 @boolValue 為文字)       | EF Core 6.0
byteValue ToString ()       | 將 (轉換 @byteValue 為文字)       | EF Core 6.0
位元組。ToString ()           | 將 (轉換 @bytes 為文字)           | EF Core 6.0
charValue ToString ()       | 將 (轉換 @charValue 為文字)       | EF Core 6.0
dateTime. ToString ()        | 將 (轉換 @dateTime 為文字)        | EF Core 6.0
dateTimeOffset ()  | 將 (轉換 @dateTimeOffset 為文字)  | EF Core 6.0
decimalValue ToString ()    | 將 (轉換 @decimalValue 為文字)    | EF Core 6.0
doubleValue ToString ()     | 將 (轉換 @doubleValue 為文字)     | EF Core 6.0
floatValue ToString ()      | 將 (轉換 @floatValue 為文字)      | EF Core 6.0
Guid。ToString ()            | 將 (轉換 @guid 為文字)            | EF Core 6.0
intValue ToString ()        | 將 (轉換 @intValue 為文字)        | EF Core 6.0
longValue ToString ()       | 將 (轉換 @longValue 為文字)       | EF Core 6.0
sbyteValue ToString ()      | 將 (轉換 @sbyteValue 為文字)      | EF Core 6.0
shortValue ToString ()      | 將 (轉換 @shortValue 為文字)      | EF Core 6.0
timeSpan. ToString ()        | 將 (轉換 @timeSpan 為文字)        | EF Core 6.0
uintValue ToString ()       | 將 (轉換 @uintValue 為文字)       | EF Core 6.0
ushortValue ToString ()     | 將 (轉換 @ushortValue 為文字)     | EF Core 6.0

## <a name="date-and-time-functions"></a>日期和時間函式

.NET                            | SQL                                                                      | 加入
------------------------------- | ------------------------------------------------------------------------ | --------
DateTime.Now                    | datetime ( ' now '，' localtime ' ) 
DateTime. Today                  | datetime ( ' now '、' localtime '、' start of day ' ) 
DateTime.UtcNow                 | datetime ( ' now ' ) 
dateTime. AddDays (值)          | datetime (@dateTime ， @value \| \| ' days ' ) 
dateTime. Time.addhours (值)         | datetime (@dateTime ， @d \| \| ' hours ' ) 
dateTime. AddMilliseconds (值)  | datetime (@dateTime ， (@value /1000.0) \| \| ' seconds ' )                    | EF Core 2.2
dateTime. AddMinutes (值)       | datetime (@dateTime ， @value \| \| ' 分鐘 ' ) 
dateTime. AddMonths (months)       | datetime (@dateTime ， @months \| \| ' months ' ) 
dateTime. AddSeconds (值)       | datetime (@dateTime ， @value \| \| ' seconds ' ) 
dateTime. AddTicks (值)         | datetime (@dateTime ， (@value /10000000.0) \| \| ' seconds ' )                | EF Core 2.2
dateTime. AddYears (值)         | datetime (@dateTime ， @value \| \| ' 年 ' ) 
日期時間                   | datetime (@dateTime ，' start of day ' ) 
dateTime. Day                    | strftime ( '% d '， @dateTime) 
dateTime. DayOfWeek              | strftime ( '% w '， @dateTime)                                                 | EF Core 2.2
dateTime. DayOfYear              | strftime ( '% j '， @dateTime) 
dateTime。小時                   | strftime ( '% H '， @dateTime) 
dateTime。毫秒            |  (strftime ( '% f '， @dateTime) * 1000) % 1000
dateTime。分鐘                 | strftime ( '% M '， @dateTime) 
dateTime. Month                  | strftime ( '% m '， @dateTime) 
日期時間。秒                 | strftime ( '% S '， @dateTime) 
dateTime。滴答                  |  (julianday (@dateTime) -julianday ( ' 0001-01-01 00:00:00 ' ) ) * 864000000000 | EF Core 2.2
dateTime. TimeOfDay              | 時間 (@dateTime)                                                           | EF Core 3.0
dateTime. Year                   | strftime ( '% Y '， @dateTime) 

> [!NOTE]
> 有些 SQL 已經過簡化，以供說明之用。 實際的 SQL 比較複雜，可處理更大範圍的值。

## <a name="numeric-functions"></a>數值函數

.NET                  | SQL                                   | 加入
--------------------- | ------------------------------------- | --------
-decimalValue         | ef_negate (@decimalValue)               | EF Core 5.0
decimalValue-d      | ef_add (@decimalValue 、ef_negate (@d) )   | EF Core 5.0
decimalValue * d      | ef_multiply (@decimalValue 、 @d)         | EF Core 5.0
decimalValue/d      | ef_divide (@decimalValue 、 @d)           | EF Core 5.0
decimalValue% d      | ef_mod (@decimalValue 、 @d)              | EF Core 5.0
decimalValue + d      | ef_add (@decimalValue 、 @d)              | EF Core 5.0
decimalValue < d      | ef_compare (@decimalValue ， @d) < 0     | EF Core 5.0
decimalValue <= d     | ef_compare (@decimalValue ， @d) <= 0    | EF Core 5.0
decimalValue > d      | ef_compare (@decimalValue ， @d) > 0     | EF Core 5.0
decimalValue >= d     | ef_compare (@decimalValue ， @d) >= 0    | EF Core 5.0
doubleValue% d       | ef_mod (@doubleValue 、 @d)               | EF Core 5.0
英 孚。函數。隨機 ()  | abs (隨機 () /9223372036854780000.0)  | EF Core 6.0
floatValue% d        | ef_mod (@floatValue 、 @d)                | EF Core 5.0
數學 Abs (值)        | abs (@value) 
數學 (val1、val2) 的最大值  | 最大 (@val1 ， @val2) 
Val1，val2) 最小 (  | 最小 (@val1 ， @val2) 
數學. 四捨五入 (d)          | 四捨五入 (@d) 
數學. 四捨五入 (d，位數)  | 四捨五入 (@d ， @digits) 

> [!TIP]
> 前置詞為 *ef* 的 SQL 函數是由 EF Core 所建立。

## <a name="string-functions"></a>字串函數

.NET                                                         | SQL                                                    | 加入
------------------------------------------------------------ | ------------------------------------------------------ | --------
字元。ToLower (c)                                               | 較低的 (@c)                                               | EF Core 6.0
字元。ToUpper (c)                                               | 上層 (@c)                                               | EF Core 6.0
英 孚。函數. Collate (運算元，定序)                      | @operand 整理 @collation                            | EF Core 5.0
英 孚。Glob (matchExpression，pattern)                   | glob (@pattern ， @matchExpression)                        | EF Core 6.0
英 孚。函數。例如 (matchExpression、pattern)                   | @matchExpression 喜歡 @pattern
英 孚。函數。例如 (matchExpression、pattern、escapeCharacter)  | @matchExpression LIKE @pattern ESCAPE @escapeCharacter
IsMatch (輸入，模式)                                 | RegExp (@pattern ， @input)                                | EF Core 6.0
字串。比較 (strA、strB)                                    | @strA= @strB THEN 0 時的案例 .。。結束
字串。Concat (str0、str1)                                     | @str0 \|\| @str1
字串。IsNullOrEmpty (值)                                   | @value 為 Null 或 @value = ' '
字串。IsNullOrWhiteSpace (值)                              | @value 為 Null 或 trim (@value) = ' '
stringValue. CompareTo (strB)                                   | @stringValue= @strB THEN 0 時的案例 .。。結束
stringValue 包含 (值)                                   | instr (@stringValue ， @value) > 0
stringValue. EndsWith (值)                                   | @stringValue例如 '% ' \| \|@value
stringValue. FirstOrDefault ()                                  | substr (@stringValue ，1，1)                              | EF Core 5.0
stringValue. IndexOf (值)                                    | instr (@stringValue ， @value) -1
stringValue. LastOrDefault ()                                   | substr (@stringValue 、length (@stringValue) 、1)           | EF Core 5.0
stringValue。長度                                           | 長度 (@stringValue) 
stringValue 取代 (oldValue，newValue)                       | 取代 (@stringValue 、 @oldValue @newValue) 
stringValue. StartsWith (值)                                 | @stringValue例如 @value \| \| '% '
stringValue (startIndex，長度)                     | substr (@stringValue 、 @startIndex + 1、 @length) 
stringValue. ToLower ()                                         | 較低的 (@stringValue) 
stringValue. ToUpper ()                                         | 上層 (@stringValue) 
stringValue Trim ()                                            | 修剪 (@stringValue) 
stringValue)  (trimChar                                   | trim (@stringValue 、 @trimChar) 
stringValue. TrimEnd ()                                         | rtrim (@stringValue) 
stringValue. TrimEnd (trimChar)                                 | rtrim (@stringValue ， @trimChar) 
stringValue. TrimStart ()                                       | ltrim (@stringValue) 
stringValue. TrimStart (trimChar)                               | ltrim (@stringValue ， @trimChar) 

> [!NOTE]
> 有些 SQL 已經過簡化，以供說明之用。 實際的 SQL 比較複雜，可處理更大範圍的值。

## <a name="miscellaneous-functions"></a>其他函式

.NET                                     | SQL                                | 加入
---------------------------------------- | ---------------------------------- | --------
收集。包含 (專案)                 | @item 在 @collection               | EF Core 3.0
Enumvalue 類型. HasFlag (旗標)                   | @enumValue & @flag = @flag
空。CoNtext.variables.getvalueordefault<t ()              | 聯合 (@nullable 、0) 
空。CoNtext.variables.getvalueordefault<t (defaultValue)  | 聯合 (@nullable 、 @defaultValue) 

## <a name="see-also"></a>另請參閱

* [空間函數對應](xref:core/providers/sqlite/spatial#spatial-function-mappings)
