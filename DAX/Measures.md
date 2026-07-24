# Measures

## Amount Conversion %

```DAX
Amount Conversion % = 
DIVIDE(
    SUM('Buyer PO'[bPOItemAmountBeforeTax]),
    SUM('Buyer Quote'[Calculated Buyer Quote Item Amount Before Tax]),
    0
)
```

## Avg Days Buyer Req to Quote

```DAX
Avg Days Buyer Req to Quote = 

AVERAGEX(
    FILTER(
        'Buyer Quote',
        NOT ISBLANK('Buyer Quote'[Calculated Buyer Unit Price])
    ),
    DATEDIFF(
        RELATED('Dimension Table'[buyerReqDate]),
        'Buyer Quote'[buyerQuoteDate],
        DAY
    )
)
```

## Buyer Quote Margin Rate

```DAX
Buyer Quote Margin Rate = 
CALCULATE(
    SUM('Buyer Quote'[bQuotMarginRate]),
    NOT ISBLANK('Buyer Quote'[Calculated Buyer Unit Price])
)
```

## Gross Margin %

```DAX
Gross Margin % = 

VAR SelectedBuyerRows = 
    FILTER(
        'Buyer Quote',
        NOT ISBLANK('Buyer Quote'[Calculated Buyer Unit Price])
    )

VAR TotalRevenue = 
    SUMX(SelectedBuyerRows, 'Buyer Quote'[Calculated Buyer Unit Price])

VAR TotalCost = 
    SUMX(
        SelectedBuyerRows,
        VAR sid = 'Buyer Quote'[sellerReqItemId]
        RETURN
            MAXX(
                FILTER('Seller Quote', 'Seller Quote'[sellerReqItemId] = sid),
                'Seller Quote'[Calculated Seller Unit Price]
            )
    )

RETURN
    DIVIDE(TotalRevenue - TotalCost, TotalRevenue, BLANK())
```

## Pending Amount

```DAX
Pending Amount = 
VAR QuoteAmt = 
    CALCULATE(
        SUM('Buyer Quote'[Calculated Buyer Quote Item Amount Before Tax]),
        NOT ISBLANK('Buyer Quote'[Calculated Buyer Quote Item Amount Before Tax])
    )

VAR BPOAmt = 
    SUM('Buyer PO'[bPOItemAmountBeforeTax])

RETURN
    QuoteAmt - BPOAmt
```

## RFQ Conversion %

```DAX
RFQ Conversion % = DIVIDE([Total PO], [Total Quote], 0)
```

## Seller Conversion %

```DAX
Seller Conversion % = DIVIDE(DISTINCTCOUNT('Buyer PO'[buyerReqItemId]), DISTINCTCOUNT('Seller Quote'[buyerReqItemId]), 0)
```

## SPO Amount

```DAX
SPO Amount = 
SUMX(
    SUMMARIZE(
        ZDashboard,
        ZDashboard[buyerReqItemId],
        "MinAmount",
        MINX(
            FILTER(
                ZDashboard,
                ZDashboard[buyerReqItemId] = EARLIER(ZDashboard[buyerReqItemId])
            ),
            ZDashboard[spoItemAmountBeforeTax]
        )
    ),
    [MinAmount]
)
```

## Seller Quote Company

```DAX
Seller Quote Company = 
CALCULATE(
    SELECTEDVALUE('Seller Quote'[sellerCompany]),
    NOT ISBLANK('Seller Quote'[Calculated Seller Unit Price])
)
```

## Seller Quote Discount Rate

```DAX
Seller Quote Discount Rate = 
CALCULATE(
    SUM('Seller Quote'[sellerQuoteDiscountRate]),
    NOT ISBLANK('Seller Quote'[Calculated Seller Unit Price])
)
```

## Seller Quote List Price

```DAX
Seller Quote List Price = 
CALCULATE(
    SUM('Seller Quote'[sellerQuoteListPrice]),
    NOT ISBLANK('Seller Quote'[Calculated Seller Unit Price])
)
```

## TAT RFQ to PO Hours

```DAX
TAT RFQ to PO Hours = 
AVERAGEX(
    VALUES(ZDashboard[buyerReqItemId]),

    VAR CurrentId = ZDashboard[buyerReqItemId]

    VAR AllRows = 
        FILTER(ZDashboard, ZDashboard[buyerReqItemId] = CurrentId)

    VAR RowsWithPO = 
        FILTER(AllRows, NOT ISBLANK(ZDashboard[bPOItemAmountBeforeTax]))

    VAR MinPOAmount = 
        MINX(RowsWithPO, ZDashboard[bPOItemAmountBeforeTax])

    -- Pick the anchor row (same logic as bQuote measure)
    VAR AnchorRow = 
        FILTER(
            RowsWithPO,
            ZDashboard[bPOItemAmountBeforeTax] = MinPOAmount
        )

    -- Get dates from that anchor row
    VAR ReqDate  = MINX(AnchorRow, ZDashboard[buyerReqDate])
    VAR PODate   = MINX(AnchorRow, ZDashboard[buyerPODate])

    VAR TATHours = 
        IF(
            NOT ISBLANK(PODate) && NOT ISBLANK(ReqDate) && PODate >= ReqDate,
            DATEDIFF(ReqDate, PODate, HOUR),
            BLANK()
        )

    RETURN TATHours
)
```

## Total PO - Previous Year

```DAX
Total PO - Previous Year = 
CALCULATE(
    [Total PO],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Total Quote - Previous Year

```DAX
Total Quote - Previous Year = 
CALCULATE(
    [Total Quote],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Total RFQ - Previous Year

```DAX
Total RFQ - Previous Year = 
CALCULATE(
    [Total RFQ],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Total Pending

```DAX
Total Pending = [Total Quote] - [Total PO]
```


## Total PO

```DAX
Total PO = DISTINCTCOUNT('Buyer PO'[buyerReqItemId])
```

## Unquoted Request

```DAX
Unquoted Request = 
DISTINCTCOUNT('Dimension Table'[buyerReqItemId]) - DISTINCTCOUNT('Buyer Quote'[buyerReqItemId])
```
