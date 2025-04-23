# Power Query M Code - Persian Date Table Generator
This code generates a comprehensive date table with both Gregorian and Persian calendar conversions.

```powerquery

let
    StartDate =  #date(2023,1,1),
    EndDate = #date(2027,1,1),
    Source = List.Dates(StartDate, Duration.Days(EndDate - StartDate), #duration(1,0,0,0)),
    Convert_To_Table = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    Rename = Table.RenameColumns(Convert_To_Table,{{"Column1", "DateString"}}),
    DateFormat = Table.TransformColumnTypes(Rename,{{"DateString", type date}}),
    Date_ID = Table.AddColumn(DateFormat, "Date_ID", each Date.ToText([DateString], "yyyyMMdd")),
    Date_ID1 = Table.TransformColumnTypes(Date_ID,{{"Date_ID", Int64.Type}}),
    PersianDateString1 = Table.AddColumn(Date_ID1, "PersianDateString1", each Date.ToText([DateString], "yyyy-MM-dd", "fa-IR")),
    PersianDateString = Table.AddColumn(PersianDateString1, "PersianDateString", each Date.ToText([DateString], "yyyy/MM/dd", "fa-IR")),
    PersianDate = Table.AddColumn(PersianDateString,"PersianDate", each Text.Remove([PersianDateString1], {"-"})),
    PersianDate1 = Table.TransformColumnTypes(PersianDate,{{"PersianDate", Int64.Type}}),
    PersianYear = Table.AddColumn(PersianDate1, "PersianYear", each Number.FromText(Text.Start([PersianDateString1],4)), Int64.Type),
    PersianMonthNumber = Table.AddColumn(PersianYear, "PersianMonthNumber", each Number.FromText(Text.End(Text.Start([PersianDateString1],7),2)),Int64.Type),
    PersianDayNumberofMonth = Table.AddColumn(PersianMonthNumber, "PersianDayNumberofMonth", each Text.End([PersianDateString1],2)),
    PersianDayNumberofMonth1 = Table.TransformColumnTypes(PersianDayNumberofMonth,{{"PersianDayNumberofMonth", Int64.Type}}),
    PersianMonthTitle = Table.AddColumn(PersianDayNumberofMonth1, "PersianMonthTitle", each Date.ToText([DateString],"MMMM","fa-IR"), type text),
    PersianMonthNameEn = Table.AddColumn(PersianMonthTitle, "PersianMonthNameEn", each if [PersianMonthNumber] = 1 then "Farvardin" else if [PersianMonthNumber] = 2 then "Ordibehesht" else if [PersianMonthNumber] = 3 then "Khordad" else if [PersianMonthNumber] = 4 then "Tir" else if [PersianMonthNumber] = 5 then "Mordad" else if [PersianMonthNumber] = 6 then "Shahrivar" else if [PersianMonthNumber] = 7 then "Mehr" else if [PersianMonthNumber] = 8 then "Aban" else if [PersianMonthNumber] = 9 then "Azar" else if [PersianMonthNumber] = 10 then "Dey" else if [PersianMonthNumber] = 11 then "Bahman" else if [PersianMonthNumber] = 12 then "Esfand" else null),
    PersianDayNameOfWeek = Table.AddColumn(PersianMonthNameEn, "PersianDayNameOfWeek", each Date.ToText([DateString],"dddd","fa-IR"), type text),
    EnglishYear = Table.AddColumn(PersianDayNameOfWeek, "EnglishYear", each Date.Year([DateString]), Int64.Type),
    EnglishMonthNumber = Table.AddColumn(EnglishYear, "EnglishMonthNumber", each Date.Month([DateString]), Int64.Type),
    EnglishMonthTitle = Table.AddColumn(EnglishMonthNumber, "EnglishMonthTitle", each Date.ToText([DateString],"MMMM"), type text),
    EnglishDayNumberofWeek = Table.AddColumn(EnglishMonthTitle, "EnglishDayNumberofWeek", each Date.DayOfWeek([DateString]), Int64.Type),
    EnglishDayNameofWeek = Table.AddColumn(EnglishDayNumberofWeek, "EnglishDayNameofWeek", each Date.ToText([DateString],"dddd"), type text),
    PersianDayNumberofWeek = Table.AddColumn(EnglishDayNameofWeek, "PersianDayNumberofWeek", each if [PersianDayNameOfWeek] = "يكشنبه" then 2 else if [PersianDayNameOfWeek] = "دوشنبه" then 3 else if [PersianDayNameOfWeek] = "سه شنبه" then 4 else if [PersianDayNameOfWeek] = "چهارشنبه" then 5 else if [PersianDayNameOfWeek] = "پنجشنبه" then 6 else if [PersianDayNameOfWeek] = "جمعه" then 7 else if [PersianDayNameOfWeek] = "شنبه" then 1 else null, type number)
in
    PersianDayNumberofWeek
```
