# Calculated Columns

## Calculated Brand Type

```DAX
Calculated Brand Type = 
IF('Dimension Table'[brandName] = "Generic", "Non Branded", "Branded")
```

## Calculated Conversion Type

```DAX
Calculated Conversion Type = 
IF(
    CONTAINS(
        'Buyer PO',
        'Buyer PO'[buyerReqItemId], 'Dimension Table'[buyerReqItemId]
    ),
    "Converted",
    "Pending"
)
```

## Calculated Date Only

```DAX
Calculated Date Only = 
DATE(
    YEAR('Dimension Table'[buyerReqDate]),
    MONTH('Dimension Table'[buyerReqDate]),
    DAY('Dimension Table'[buyerReqDate])
)
```

## Calculated Month Year

```DAX
Calculated Month Year = 
FORMAT('Dimension Table'[buyerReqDate],"MMM YY")
```

## Calculated MonthYearSort

```DAX
Calculated MonthYearSort = 
YEAR('Dimension Table'[buyerReqDate]) * 100 +
MONTH('Dimension Table'[buyerReqDate])
```

## Calculated Seller Company

```DAX
Calculated Seller Company = 
IF(
    NOT ISBLANK('Seller Quote'[Calculated Seller Unit Price]),
    'Seller Quote'[sellerCompany],
    BLANK()
)
```

## Calculated Seller Quote Quantity

```DAX
Calculated Seller Quote Quantity = 
IF(
    NOT ISBLANK('Seller Quote'[Calculated Seller Unit Price]),
    'Seller Quote'[sellerQuoteQty],
    BLANK()
)
```

## Calculated Seller Unit Price

```DAX
Calculated Seller Unit Price = 

VAR CurrentSellerReqItemId = 'Seller Quote'[sellerReqItemId]

-- Step 1: Find the buyerQuoteItemId that references this sellerReqItemId
--         AND has a non-blank Selected Unit Price
VAR MatchingBuyerQuoteRow = 
    FILTER(
        'Buyer Quote',
        'Buyer Quote'[sellerReqItemId] = CurrentSellerReqItemId
            && NOT ISBLANK('Buyer Quote'[Calculated Buyer Unit Price])
    )

-- Step 2: Check if such a row exists
VAR IsSelected = NOT ISEMPTY(MatchingBuyerQuoteRow)

-- Step 3: Return this row's sellerQuoteUnitPrice if matched, else BLANK
RETURN
    IF(
        IsSelected,
        'Seller Quote'[sellerQuoteUnitPrice],
        BLANK()
    )
```

## Calculated Buyer Discount Rate

```DAX
Calculated Buyer Discount Rate = 
IF(
    NOT ISBLANK('Buyer Quote'[Calculated Buyer Unit Price]),
    'Buyer Quote'[quotDiscountRate],
    BLANK()
)
```

## Calculated Buyer Quote Item Amount Before Tax

```DAX
Calculated Buyer Quote Item Amount Before Tax = 

VAR CurReqItemId =
    'Buyer Quote'[buyerReqItemId]

VAR CurQuoteItemId =
    'Buyer Quote'[buyerQuoteItemId]

VAR POExists =
    CONTAINS(
        'Buyer PO',
        'Buyer PO'[buyerReqItemId], CurReqItemId
    )

VAR CurRowHasPO =
    CONTAINS(
        'Buyer PO',
        'Buyer PO'[buyerQuoteItemId], CurQuoteItemId
    )

VAR TotalQuoteQty =
    CALCULATE(
        SUM('Buyer Quote'[quoteQty]),
        ALLEXCEPT(
            'Buyer Quote',
            'Buyer Quote'[buyerReqItemId]
        )
    )

VAR ReqQty =
    LOOKUPVALUE(
        'Dimension Table'[reqQty],
        'Dimension Table'[buyerReqItemId], CurReqItemId
    )

VAR CurRowQuoteAmt =
    'Buyer Quote'[bQuoteItemAmountBeforeTax]

VAR CurRowUnitPrice =
    'Buyer Quote'[Calculated Buyer Unit Price]

VAR CurRowQuoteQty =
    'Buyer Quote'[quoteQty]

RETURN
IF(
    POExists,

    -- PO exists
    IF(
        CurRowHasPO,
        CurRowQuoteAmt,
        BLANK()
    ),

    -- No PO exists
    IF(
        TotalQuoteQty = ReqQty,

        IF(
            CurRowQuoteQty > 0,
            CurRowQuoteAmt,
            BLANK()
        ),

        -- Quantities do not match
        IF(
            NOT ISBLANK(CurRowUnitPrice),
            CurRowUnitPrice * CurRowQuoteQty,
            BLANK()
        )
    )
)
```

## Calculated Buyer Unit Price

```DAX
Calculated Buyer Unit Price = 

VAR CurrentReqItemId = 'Buyer Quote'[buyerReqItemId]
VAR CurrentQuoteItemId = 'Buyer Quote'[buyerQuoteItemId]

-- Step 1: Get all BuyerPO rows under this buyerReqItemId
-- by finding all buyerQuoteItemIds under this req first
VAR AllQuoteItemsInReq = 
    SELECTCOLUMNS(
        FILTER('Buyer Quote', 'Buyer Quote'[buyerReqItemId] = CurrentReqItemId),
        "qid", 'Buyer Quote'[buyerQuoteItemId]
    )

VAR AllPORowsInReq = 
    FILTER(
        'Buyer PO',
        'Buyer PO'[buyerQuoteItemId] IN AllQuoteItemsInReq
    )

-- Step 2: Check if ANY PO exists under this buyerReqItemId
VAR AnyPOExistsForReq = NOT ISEMPTY(AllPORowsInReq)

-- Step 3: Find the single winning buyerPOItemId
-- = the one with minimum bPOUnitPrice under this buyerReqItemId
VAR MinPOUnitPrice = 
    MINX(AllPORowsInReq, 'Buyer PO'[bPOUnitPrice])

VAR WinningPOQuoteItemId = 
    MINX(
        FILTER(AllPORowsInReq, 'Buyer PO'[bPOUnitPrice] = MinPOUnitPrice),
        'Buyer PO'[buyerQuoteItemId]
        -- MINX on buyerQuoteItemId acts as tiebreaker
        -- if two POs have same bPOUnitPrice, picks one deterministically
    )

-- Step 4: When no PO → find the single buyerQuoteItemId with minimum bQuotUnitPrice
VAR MinQuoteUnitPrice = 
    MINX(
        FILTER('Buyer Quote', 'Buyer Quote'[buyerReqItemId] = CurrentReqItemId),
        'Buyer Quote'[bQuotUnitPrice]
    )

VAR WinningQuoteItemId = 
    MINX(
        FILTER(
            'Buyer Quote',
            'Buyer Quote'[buyerReqItemId] = CurrentReqItemId
                && 'Buyer Quote'[bQuotUnitPrice] = MinQuoteUnitPrice
        ),
        'Buyer Quote'[buyerQuoteItemId]
        -- tiebreaker if two rows have same minimum price
    )

-- Step 5: Apply logic
RETURN
    IF(
        AnyPOExistsForReq,
        -- PO path: only the row whose buyerQuoteItemId = WinningPOQuoteItemId gets price
        IF(
            CurrentQuoteItemId = WinningPOQuoteItemId,
            'Buyer Quote'[bQuotUnitPrice],
            BLANK()
        ),
        -- No PO path: only the row whose buyerQuoteItemId = WinningQuoteItemId gets price
        IF(
            CurrentQuoteItemId = WinningQuoteItemId,
            'Buyer Quote'[bQuotUnitPrice],
            BLANK()
        )
    )
```

