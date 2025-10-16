# Power BI — AdventureWorks sample datasets

This repository contains CSV datasets and a small sample data model you can open with Power BI Desktop.

## Contents

- `AdventureWorks_Calendar.csv` — calendar/date table (recommended as the primary Date table).
- `AdventureWorks_Customers.csv` — customer list and attributes.
- `AdventureWorks_Product_Categories.csv` — product category lookup.
- `AdventureWorks_Product_Subcategories.csv` — product subcategory lookup.
- `AdventureWorks_Products.csv` — product master list.
- `AdventureWorks_Returns.csv` — returned items.
- `AdventureWorks_Territories.csv` — sales territories.
- `Unpivot_Demo.csv` — small demo file showing an unpivot scenario.
- `Sales/AdventureWorks_Sales_2015.csv`, `Sales/AdventureWorks_Sales_2016.csv`, `Sales/AdventureWorks_Sales_2017.csv` — yearly sales transaction CSVs.

## Goal

Provide a small, friendly workspace for learning Power BI: building a star schema, using Power Query to combine files, creating measures in DAX, and exploring reporting techniques.

## Recommended data model

1. Use `AdventureWorks_Calendar.csv` as the Date table (mark as Date table in Power BI).
2. Combine the sales CSVs into a single `Sales` table (Power Query can combine folder files or use the sample PowerShell below to merge locally).
3. Create relationships: Sales[OrderDate] -> Calendar[Date], Sales[ProductKey] -> Products[ProductKey], Products[ProductSubcategoryKey] -> Product_Subcategories, and Subcategories -> Product_Categories. Link Customer and Territory tables as needed.

Keep dimension tables (Customers, Products, Categories, Territories) in their own tables and keep Sales as the fact table.

## Loading steps (Power BI Desktop)

1. Open Power BI Desktop.
2. Get Data -> Text/CSV -> select a CSV (or Get Data -> Folder to combine the Sales files).
3. In Power Query: if you used Folder, click `Combine & Transform` to merge yearly sales files into one table. Check data types and trim whitespace.
4. Apply changes, go to Model view, mark the calendar table as Date table, and create relationships.

### Optional: combine sales CSVs with PowerShell (Windows PowerShell)
If you prefer to create a single CSV file first (simple concatenation assuming identical headers):

```powershell
# Run from repository root (where Sales folder exists)
$out = "Sales\AdventureWorks_Sales_All.csv"
Get-ChildItem -Path .\Sales -Filter *.csv | Sort-Object Name | ForEach-Object -Begin { $first = $true } -Process {
    if ($first) { Get-Content $_.FullName | Out-File -FilePath $out -Encoding utf8; $first = $false } else { Get-Content $_.FullName | Select-Object -Skip 1 | Out-File -FilePath $out -Append -Encoding utf8 }
}
Write-Host "Created $out"
```

Use `Sales\AdventureWorks_Sales_All.csv` as the combined file in Power BI if you run the script.

## Sample DAX measures

- Total Sales =
  SUM(Sales[SalesAmount])

- Total Quantity =
  SUM(Sales[OrderQuantity])

- Sales YTD =
  CALCULATE([Total Sales], DATESYTD('Calendar'[Date]))

Adjust column names if they differ in your CSVs (for example `SalesAmount`, `OrderQuantity`, `OrderDate`).

## Tips and troubleshooting

- Ensure date formats are parsed correctly in Power Query — set the Date column type.
- Trim and clean text columns (e.g., Product numbers) in Power Query to avoid duplicate dimension rows.
- If relationships don't behave as expected, confirm the data types match on both sides of the relationship.

## Next steps / suggestions

- Create a small report with: Total Sales by Year, Sales by Product Category, Top 10 Customers, and a returns analysis (link `AdventureWorks_Returns.csv` to Sales by key).
- Add sample measures for gross margin, average order value, and rolling 12-month sales.

## License

This is a sample dataset repo for demo/learning purposes. Use freely in demos, training, and examples.

---
If you'd like, I can also:

- Create a Power Query M sample to combine the Sales folder inside the Power BI query editor.
- Generate a small example .pbix template (if you provide Power BI Desktop guidance or want a JSON model).

If you'd like any of those, tell me which and I'll add it.
