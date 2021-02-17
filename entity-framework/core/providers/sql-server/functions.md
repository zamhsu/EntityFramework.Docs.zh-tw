---
title: 函數對應-Microsoft SQL Server 資料庫提供者-EF Core
description: Microsoft SQL Server 資料庫提供者的函式對應
author: bricelam
ms.date: 1/26/2021
uid: core/providers/sql-server/functions
ms.openlocfilehash: c39e81fca52854e7fa7d5b2e81b80511e8184b90
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543220"
---
# <a name="function-mappings-of-the-microsoft-sql-server-provider"></a>Microsoft SQL Server 提供者的函式對應

此頁面會顯示使用 SQL Server 提供者時，哪些 .NET 成員會轉譯成哪些 SQL 函數。

## <a name="binary-functions"></a>二進位函式

.NET                         | SQL                           | 加入
---------------------------- | ----------------------------- | --------
位元組。包含 (值)         | CHARINDEX (@value ， @bytes) > 0 | EF Core 5.0
位元組。第一個 ()                 | 子字串 (@bytes ，1，1)        | EF Core 6.0
位元組。長度                 | DATALENGTH (@bytes)             | EF Core 5.0
位元組。Enumerable.sequenceequal (第二個)   | @bytes = @second              | EF Core 5.0
位元組 [i]                     | 子字串 (@bytes 、 @i + 1、1)   | EF Core 6.0
英 孚。DataLength (arg)  | DATALENGTH (@arg)               | EF Core 5.0

## <a name="conversion-functions"></a>轉換函數

.NET                      | SQL                                    | 加入
------------------------- | -------------------------------------- | --------
位元組。ToString ()           | 轉換 (Varchar (100) ， @bytes) 
byteValue ToString ()       | 轉換 (Varchar (3) ， @byteValue) 
charValue ToString ()       | 轉換 (Varchar (1) ， @charValue) 
轉換. ToBoolean (值)   | 轉換 (位， @value)                    | EF Core 5.0
轉換. ToByte (值)      | 轉換 (Tinyint， @value) 
轉換. ToDecimal (值)   | 將 (decimal (18，2) ， @value) 
轉換. ToDouble (值)    | 轉換 (float， @value) 
轉換. ToInt16 (值)     | 轉換 (Smallint， @value) 
轉換. ToInt32 (值)     | 轉換 (int， @value) 
轉換. ToInt64 (值)     | 轉換 (Bigint， @value) 
將 ToString (值轉換)    | 將 (Nvarchar (max) ， @value) 
dateTime. ToString ()        | 轉換 (Varchar (100) ， @dateTime) 
dateTimeOffset ()  | 轉換 (Varchar (100) ， @dateTimeOffset) 
decimalValue ToString ()    | 轉換 (Varchar (100) ， @decimalValue) 
doubleValue ToString ()     | 轉換 (Varchar (100) ， @doubleValue) 
floatValue ToString ()      | 轉換 (Varchar (100) ， @floatValue) 
Guid。ToString ()            | 轉換 (Varchar (36) ， @guid) 
intValue ToString ()        | 轉換 (Varchar (11) ， @intValue) 
longValue ToString ()       | 轉換 (Varchar (20) ， @longValue) 
sbyteValue ToString ()      | 轉換 (Varchar (4) ， @sbyteValue) 
shortValue ToString ()      | 轉換 (Varchar (6) ， @shortValue) 
timeSpan. ToString ()        | 轉換 (Varchar (100) ， @timeSpan) 
uintValue ToString ()       | 轉換 (Varchar (10) ， @uintValue) 
ulongValue ToString ()      | 轉換 (Varchar (19) ， @ulongValue) 
ushortValue ToString ()     | 轉換 (Varchar (5) ， @ushortValue) 

## <a name="date-and-time-functions"></a>日期和時間函式

.NET                                                        | SQL                                                  | 加入
----------------------------------------------------------- | ---------------------------------------------------- | --------
DateTime.Now                                                | GETDATE () 
DateTime. Today                                              | 轉換 (日期，GETDATE () ) 
DateTime.UtcNow                                             | GETUTCDATE () 
dateTime. AddDays (值)                                      | DATEADD (day， @value ， @dateTime) 
dateTime. Time.addhours (值)                                     | DATEADD (小時， @value ， @dateTime) 
dateTime. AddMilliseconds (值)                              | DATEADD (毫秒、 @value ， @dateTime) 
dateTime. AddMinutes (值)                                   | DATEADD (分鐘， @value ， @dateTime) 
dateTime. AddMonths (months)                                   | DATEADD (month、 @months ， @dateTime) 
dateTime. AddSeconds (值)                                   | DATEADD (秒、 @value 、 @dateTime) 
dateTime. AddYears (值)                                     | DATEADD (year、 @value ， @dateTime) 
日期時間                                               | 轉換 (日期， @dateTime) 
dateTime. Day                                                | DATEPART (day， @dateTime) 
dateTime. DayOfYear                                          | DATEPART (dayofyear， @dateTime) 
dateTime。小時                                               | DATEPART (小時， @dateTime) 
dateTime。毫秒                                        | DATEPART (毫秒， @dateTime) 
dateTime。分鐘                                             | DATEPART (分鐘， @dateTime) 
dateTime. Month                                              | DATEPART (month， @dateTime) 
日期時間。秒                                             | DATEPART (第二個， @dateTime) 
dateTime. TimeOfDay                                          | 轉換 (時間， @dateTime)                               | EF Core 2.2
dateTime. Year                                               | DATEPART (year， @dateTime) 
DateTimeOffset.Now                                          | SYSDATETIMEOFFSET () 
DateTimeOffset. UtcNow                                       | SYSUTCDATETIME () 
AddDays (天)                                 | DATEADD (day， @days ， @dateTimeOffset) 
dateTimeOffset Time.addhours (小時)                               | DATEADD (小時， @hours ， @dateTimeOffset) 
dateTimeOffset AddMilliseconds (毫秒)                 | DATEADD (毫秒、 @milliseconds ， @dateTimeOffset) 
dateTimeOffset AddMinutes (分鐘)                           | DATEADD (分鐘， @minutes ， @dateTimeOffset) 
AddMonths (月份)                             | DATEADD (month、 @months ， @dateTimeOffset) 
dateTimeOffset AddSeconds (秒)                           | DATEADD (秒、 @seconds 、 @dateTimeOffset) 
AddYears (年)                               | DATEADD (year、 @years ， @dateTimeOffset) 
dateTimeOffset。 Date                                         | 轉換 (日期， @dateTimeOffset) 
dateTimeOffset. Day                                          | DATEPART (day， @dateTimeOffset) 
dateTimeOffset. DayOfYear                                    | DATEPART (dayofyear， @dateTimeOffset) 
dateTimeOffset. 小時                                         | DATEPART (小時， @dateTimeOffset) 
dateTimeOffset。毫秒                                  | DATEPART (毫秒， @dateTimeOffset) 
dateTimeOffset。分鐘                                       | DATEPART (分鐘， @dateTimeOffset) 
dateTimeOffset                                        | DATEPART (month， @dateTimeOffset) 
dateTimeOffset。其次                                       | DATEPART (第二個， @dateTimeOffset) 
dateTimeOffset                                    | 轉換 (時間， @dateTimeOffset)                         | EF Core 2.2
dateTimeOffset。 Year                                         | DATEPART (year， @dateTimeOffset) 
英 孚。DateDiffDay (start，end)                         | DATEDIFF (day、 @start ， @end) 
英 孚。DateDiffHour (start，end)                        | DATEDIFF (小時、 @start @end) 
英 孚。DateDiffMicrosecond (start，end)                 | DATEDIFF (微秒、 @start 、 @end) 
英 孚。DateDiffMillisecond (start，end)                 | DATEDIFF (毫秒、 @start 、 @end) 
英 孚。DateDiffMinute (start，end)                      | DATEDIFF (minute、 @start ， @d2) 
英 孚。DateDiffMonth (start，end)                       | DATEDIFF (month、 @start @end) 
英 孚。DateDiffNanosecond (start，end)                  | DATEDIFF (毫微秒、 @start ， @end) 
英 孚。DateDiffSecond (start，end)                      | DATEDIFF (second、 @start 、 @end) 
英 孚。DateDiffWeek (start，end)                        | DATEDIFF (week、 @start @end)                          | EF Core 5.0
英 孚。DateDiffYear (start，end)                        | DATEDIFF (year、 @start @end) 
英 孚。DateFromParts (year、month、day)                 | DATEFROMPARTS (@year 、 @month @day)                    | EF Core 5.0
英 孚。DateTime2FromParts (year，month，day，... )       | DATETIME2FROMPARTS (@year ， @month ，， @day ... )          | EF Core 5.0
英 孚。DateTimeFromParts (year，month，day，... )        | DATETIMEFROMPARTS (@year ， @month ，， @day ... )           | EF Core 5.0
英 孚。DateTimeOffsetFromParts (year，month，day，... )  | DATETIMEOFFSETFROMPARTS (@year ， @month ，， @day ... )     | EF Core 5.0
英 孚。IsDate (運算式)                              | ISDATE (@expression)                                   | EF Core 3.0
英 孚。SmallDateTimeFromParts (year，month，day，... )   | SMALLDATETIMEFROMPARTS (@year ， @month ，， @day ... )      | EF Core 5.0
英 孚。函數。 TimeFromParts (小時，分鐘，秒，... )        | TIMEFROMPARTS (@hour ， @minute ，， @second ... )           | EF Core 5.0
timeSpan. 小時                                              | DATEPART (小時， @timeSpan)                             | EF Core 5.0
timeSpan 毫秒                                       | DATEPART (毫秒， @timeSpan)                      | EF Core 5.0
timeSpan. 分鐘                                            | DATEPART (分鐘， @timeSpan)                           | EF Core 5.0
timeSpan. 秒                                            | DATEPART (第二個， @timeSpan)                           | EF Core 5.0

## <a name="numeric-functions"></a>數值函數

.NET                    | SQL                  | 加入
----------------------- | -------------------- | --------
英 孚。函數。隨機 ()    | RAND ()                | EF Core 6.0
數學 Abs (值)          | ABS (@value) 
Acos (d)             | ACOS (@d) 
Asin (d)             | ASIN (@d) 
Atan (d)             | ATAN (@d) 
Atan2 (y，x)         | ATN2 (@y ， @x) 
數學. 上限 (d)          | 上限 (@d) 
 (d) 的 Math. Cos             | COS (@d) 
數學 Exp (d)              | EXP (@d) 
數學. Floor (d)            | 樓層 (@d) 
 (d) 的 Math .Log             | 記錄 (@d) 
NewBase) 的 Math. Log (    | 記錄 (@a ， @newBase) 
Log10 (d)            | LOG10 (@d) 
Pow (x，y)           | POWER (@x ， @y) 
數學. 四捨五入 (d)            | ROUND (@d 、0) 
數學. 四捨五入 (d，小數)  | 四捨五入 (@d ， @decimals) 
數學. Sign (值)         | 簽署 (@value) 
 () 的 Math Sin             | SIN (@a) 
 (d) 的數學 Sqrt            | SQRT (@d) 
數學 () 的 Tan             | TAN (@a) 
數學. 截斷 (d)         | ROUND (@d 、0、1) 

## <a name="string-functions"></a>字串函數

.NET                                                                    | SQL                                                                    | 加入
----------------------------------------------------------------------- | ---------------------------------------------------------------------- | --------
英 孚。函數. Collate (運算元，定序)                                 | @operand 整理 @collation                                            | EF Core 5.0
英 孚。函數。包含 (propertyReference、searchCondition)                | 包含 (@propertyReference 、 @searchCondition)                          | EF Core 2.2
英 孚。函數。包含 (propertyReference、searchCondition、languageTerm)  | 包含 (@propertyReference 、 @searchCondition 、LANGUAGE @languageTerm)  | EF Core 2.2
英 孚。函數 FreeText (propertyReference，freeText)                       | FREETEXT (@propertyReference ， @freeText) 
英 孚。函數 FreeText (propertyReference、freeText、languageTerm)         | FREETEXT (@propertyReference 、 @freeText 、LANGUAGE @languageTerm) 
英 孚。IsNumeric (運算式)                                       | ISNUMERIC (@expression)                                                  | EF Core 6.0
英 孚。函數。例如 (matchExpression、pattern)                              | @matchExpression 喜歡 @pattern
英 孚。函數。例如 (matchExpression、pattern、escapeCharacter)             | @matchExpression LIKE @pattern ESCAPE @escapeCharacter
字串。比較 (strA、strB)                                               | @strA= @strB THEN 0 時的案例 .。。結束
字串。Concat (str0、str1)                                                | @str0 + @str1
字串。IsNullOrEmpty (值)                                              | @value 為 Null 或 @value 類似 N ' '
字串。IsNullOrWhiteSpace (值)                                         | @value 為 Null 或 LTRIM (RTRIM (@value) ) = N ' '
stringValue. CompareTo (strB)                                              | @stringValue= @strB THEN 0 時的案例 .。。結束
stringValue 包含 (值)                                              | @stringValue 例如 N '% ' + @value + N '% '
stringValue. EndsWith (值)                                              | @stringValue 例如 N '% ' + @value
stringValue. FirstOrDefault ()                                             | 子字串 (@stringValue ，1，1)                                           | EF Core 5.0
stringValue. IndexOf (值)                                               | CHARINDEX (@value ， @stringValue) -1
stringValue. LastOrDefault ()                                              | SUBSTRING (@stringValue 、LEN (@stringValue) 、1)                           | EF Core 5.0
stringValue。長度                                                      | LEN (@stringValue) 
stringValue，取代 (@oldValue ， @newValue)                                | 取代 (@stringValue 、 @oldValue @newValue) 
stringValue. StartsWith (值)                                            | @stringValue LIKE @value + N '% '
stringValue (startIndex，長度)                                | 子字串 (@stringValue 、 @startIndex + 1、 @length) 
stringValue. ToLower ()                                                    | 較低的 (@stringValue) 
stringValue. ToUpper ()                                                    | 上層 (@stringValue) 
stringValue Trim ()                                                       | LTRIM (RTRIM (@stringValue) ) 
stringValue. TrimEnd ()                                                    | RTRIM (@stringValue) 
stringValue. TrimStart ()                                                  | LTRIM (@stringValue) 

## <a name="miscellaneous-functions"></a>其他函式

.NET                                     | SQL                                | 加入
---------------------------------------- | ---------------------------------- | --------
收集。包含 (專案)                 | @item 在 @collection               | EF Core 3.0
Enumvalue 類型. HasFlag (旗標)                   | @enumValue & @flag = @flag
Guid.NewGuid()                           | NEWID()
空。CoNtext.variables.getvalueordefault<t ()              | 聯合 (@nullable 、0) 
空。CoNtext.variables.getvalueordefault<t (defaultValue)  | 聯合 (@nullable 、 @defaultValue) 

> [!NOTE]
> 有些 SQL 已經過簡化，以供說明之用。 實際的 SQL 比較複雜，可處理更大範圍的值。

## <a name="see-also"></a>另請參閱

* [空間函數對應](xref:core/providers/sql-server/spatial#spatial-function-mappings)
