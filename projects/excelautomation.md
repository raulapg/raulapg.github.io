---
title: Excel Automation
description: Partially automating process of searching for business expenses for brother's small business
permalink: /projects/excelautomation
---
## Background

## Process
```python
import re, openpyxl

wb = openpyxl.load_workbook('checking_statement.xlsx')
sheet = wb['checking_statement']

bankRegex = re.compile(r'')

for i in range(1, sheet.max_row + 1):
    cell = sheet.cell(row=i, column=2).value
    access = bankRegex.search(str(cell))

    if access == None:
        continue
    else:
        sheet.delete_rows(i)

wb.save('checking_statement.xlsx')
