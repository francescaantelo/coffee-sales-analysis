## KPI Measures

```DAX
% Share = 
DIVIDE (
    [Revenue],
    CALCULATE ( [Revenue], ALLSELECTED ( DimProduct[coffee_name] ) )
)

Average Peak Hour Revenue = 
VAR h = [Peak Hour]
RETURN
CALCULATE (
    AVERAGEX ( VALUES ( DimDate[Date] ), [Revenue] ),
    TREATAS ( { h }, DimTime[hour_of_day] )
)

Avg Ticket = DIVIDE([Revenue], [Transactions])

Best Coffee = 
VAR t =
    TOPN(
        1,
        SUMMARIZE(
            ALL(DimProduct),                 -- o ALL(DimCoffee)
            DimProduct[coffee_name],         -- o DimCoffee[CoffeeName]
            "Total", [Revenue]
        ),
        [Total], DESC,
        DimProduct[coffee_name], ASC
    )
RETURN
MAXX(t, DimProduct[coffee_name])


Best Coffee Revenue = 
VAR t =
    TOPN(
        1,
        SUMMARIZE(
            ALL(DimProduct),
            DimProduct[coffee_name],
            "Total", [Revenue]
        ),
        [Total], DESC
    )
RETURN
MAXX(t, [Total])

Best Selling Month = 
VAR t =
    TOPN(
        1,
        SUMMARIZE(
            ALL(DimDate),                      -- ignora el contexto para comparar global
            DimDate[MontName_EN],
            DimDate[Mes],
            "Total", [Revenue]
        ),
        [Total], DESC,
        DimDate[Mes], ASC            -- desempate orden natural
    )
RETURN
MAXX(t, DimDate[MontName_EN])

Best Selling Month Revenue = 
VAR t =
    TOPN(
        1,
        SUMMARIZE(
            ALL(DimDate),
            DimDate[Nombre del mes],
            "Total", [Revenue]
        ),
        [Total], DESC
    )
RETURN
MAXX(t, [Total])

Evening % = 
1 - [Morning %]

Last updated = NOW()

Lowest Weekday = 
VAR t =
    TOPN (
        1,
        SUMMARIZE (
            ALLSELECTED ( DimDate[Weekday_EN], DimDate[Weekday Sort] ),
            DimDate[Weekday_EN],
            DimDate[Weekday Sort],
            "Total", [Revenue]
        ),
        [Total], ASC,                    -- mínimo
        DimDate[Weekday Sort], ASC
    )
RETURN
MAXX ( t, DimDate[Weekday_EN] )

MoM Growth % = 
VAR Cur  = [Revenue (Current Month)]
VAR Prev = [Revenue (Prev Month)]
RETURN
IF (
    NOT ISBLANK ( Prev ),
    DIVIDE ( Cur - Prev, Prev ),
    BLANK()          -- si prefieres 0% pon 0 en lugar de BLANK()
)

Morning % = 
DIVIDE ( [Revenue Morning], [Revenue Morning] + [Revenue Evening] )

Peak Hour = 
VAR t =
    TOPN(
        1,
        SUMMARIZE(
            ALL(DimTime),
            DimTime[hour_of_day],
            "Total", [Revenue]
        ),
        [Total], DESC,
        DimTime[hour_of_day], ASC
    )
RETURN
MAXX(t, DimTime[hour_of_day])

Peak Hour by Revenue = 
CALCULATE (
    MAX ( DimTime[hour_of_day] ),
    TOPN ( 1, SUMMARIZE ( DimTime, DimTime[hour_of_day], "Rev", [Revenue] ), [Revenue], DESC )
)

Peak Weekday = 
VAR t =
    TOPN(
        1,
        SUMMARIZE(
            ALLSELECTED ( DimDate[Weekday_EN], DimDate[Weekday Sort] ),
            DimDate[Weekday_EN],
            DimDate[Weekday Sort],
            "Total", [Revenue]
        ),
        [Total], DESC,
        DimDate[Weekday Sort], ASC
    )
RETURN
MAXX(t, DimDate[Weekday_EN])

Peak Weekday by Revenue = 
CALCULATE (
    MAX ( DimDate[Día de la semana] ),
    TOPN ( 1, SUMMARIZE ( DimDate, DimDate[Día de la semana], "Rev", [Revenue] ), [Revenue], DESC )
)

Period Growth % = 
VAR StartDate =
    CALCULATE(MIN(DimDate[Date]), ALL(DimDate))
VAR EndDate =
    CALCULATE(MAX(DimDate[Date]), ALL(DimDate))
VAR StartRevenue =
    CALCULATE([Revenue], DimDate[Date] = StartDate)
VAR EndRevenue =
    CALCULATE([Revenue], DimDate[Date] = EndDate)
RETURN
IF(
    ISBLANK(StartRevenue) || ISBLANK(EndRevenue),
    BLANK(),
    DIVIDE(EndRevenue - StartRevenue, StartRevenue)
)

Revenue = SUM(FactSales[money])

Revenue (Current Month) = 
VAR ThisYear  = MAX ( DimDate[Año] )
VAR ThisMonth = MAX ( DimDate[Month Sort] )
RETURN
CALCULATE (
    [Revenue],
    FILTER ( ALL ( DimDate ),
        DimDate[Año] = ThisYear
            && DimDate[Month Sort] = ThisMonth
    )
)

Revenue (Prev Month) = 
VAR ThisYear  = MAX ( DimDate[Año] )
VAR ThisMonth = MAX ( DimDate[Month Sort] )
VAR PrevMonth = IF ( ThisMonth = 1, 12, ThisMonth - 1 )
VAR PrevYear  = IF ( ThisMonth = 1, ThisYear - 1, ThisYear )
RETURN
CALCULATE (
    [Revenue],
    FILTER ( ALL ( DimDate ),
        DimDate[Año] = PrevYear
            && DimDate[Month Sort] = PrevMonth
    )
)

Revenue Evening = CALCULATE([Revenue], KEEPFILTERS(DimTime[hour_of_day] >=17 && DimTime[hour_of_day] <=22))

Revenue Last Month = 
CALCULATE([Revenue], DATEADD(DimDate[date], -1, MONTH))

Revenue Morning = CALCULATE([Revenue], KEEPFILTERS(DimTime[hour_of_day] >=6 && DimTime[hour_of_day] <=11))

Rolling Avg (7D) = 
AVERAGEX(
    DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -7, DAY),
    [Revenue]
)

Transactions = COUNTROWS(FactSales)
```