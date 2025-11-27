# dirtax

A Stata command for converting between net and gross incomes using progressive tax schedules.

## Overview

`dirtax` is a Stata ado command that facilitates conversions between net (after-tax) and gross (before-tax) incomes based on user-specified tax rates and thresholds. This tool is particularly useful for tax policy analysis, income modeling, and welfare calculations.

## Installation

```stata
* Install from GitHub
github install pcorralrodas/dirtax

* Or manually copy the .ado and .sthlp files to your personal ado directory
```

## Syntax

```stata
dirtax varname [if] [in], Rates(numlist) GENerate(newvarname) 
    [THolds(numlist) NETinput GROSSinput taxfree(varname)]
```

## Description

`dirtax` takes a vector of incomes along with tax rates and thresholds, then outputs either:
- **Gross incomes** (when input is net) - grossing up after-tax incomes
- **Net incomes** (when input is gross) - calculating after-tax incomes

The command applies progressive taxation, where different rates apply to income within specified brackets.

## Required Options

| Option | Description |
|--------|-------------|
| `rates(numlist)` | Tax rates (as percentages) for each bracket. Must be ≥0. List is automatically sorted. |
| `generate(newvarname)` | Name for the new variable containing output incomes |

## Optional Options

| Option | Description |
|--------|-------------|
| `tholds(numlist)` | Income thresholds where each tax bracket begins. Must be ≥0. List is automatically sorted. If omitted, uses single bracket starting at 0. |
| `netinput` | Indicates input variable contains NET (after-tax) incomes. Command will gross up. |
| `grossinput` | Indicates input variable contains GROSS (before-tax) incomes. Command will net down. |
| `taxfree(varname)` | Variable specifying tax-free allowance/exemption to subtract before applying rates |

**Note:** You must specify either `netinput` or `grossinput` (but not both).

## Examples

### Basic Usage: Net to Gross

```stata
sysuse auto, clear
rename price income

// Convert net income to gross using progressive tax schedule
dirtax income, netinput ///
    rates(5 8 14 20) ///
    tholds(0 4200 7000 13000) ///
    gen(gross_income)
    
sum income gross_income
```

### Gross to Net Conversion

```stata
// Convert gross income back to net
dirtax gross_income, grossinput ///
    rates(5 8 14 20) ///
    tholds(0 4200 7000 13000) ///
    gen(net_income)
    
sum income gross_income net_income
```

### With Tax-Free Allowance

```stata
// Apply 1000 unit tax-free exemption
gen tfree = 1000

dirtax income, netinput ///
    taxfree(tfree) ///
    rates(5 8 14 20) ///
    tholds(0 4200 7000 13000) ///
    gen(gross_income2)
    
sum income gross_income2

// Convert back to net
dirtax gross_income2, grossinput ///
    taxfree(tfree) ///
    rates(5 8 14 20) ///
    tholds(0 4200 7000 13000) ///
    gen(net_income2)
    
sum income gross_income2 net_income2
```

## How It Works

The command applies a progressive tax structure:

1. **Tax brackets** are defined by `tholds()` thresholds
2. **Tax rates** from `rates()` apply to income within each bracket
3. **Tax-free allowance** (if specified) is subtracted before calculations
4. Income is taxed progressively through brackets

### Example Tax Schedule

With `rates(5 8 14 20)` and `tholds(0 4200 7000 13000)`:

| Income Range | Tax Rate |
|--------------|----------|
| 0 - 4,200 | 5% |
| 4,200 - 7,000 | 8% |
| 7,000 - 13,000 | 14% |
| 13,000+ | 20% |

## Use Cases

- **Tax policy analysis**: Model effects of tax rate changes
- **Income imputation**: Convert between gross and net wages in surveys
- **Welfare calculations**: Adjust incomes for tax burden analysis
- **Microsimulation**: Build tax-benefit models
- **Data harmonization**: Standardize income measures across datasets

## Technical Notes

- Both `rates()` and `tholds()` numlists are automatically sorted in ascending order
- The number of rates should match the number of thresholds
- Tax-free allowances are subtracted from income before tax calculations
- All calculations preserve missing values appropriately

## Author

**Paul Corral**  
The World Bank - Poverty and Equity Global Practice  
Washington, DC  
📧 pcorralrodas@worldbank.org

*Any error or omission is the author's responsibility alone.*

## Citation

If you use this command in your research, please cite:

```
Corral, P. (2019). dirtax: Stata command for net and gross income conversions.
The World Bank, Washington, DC.
```

## License

[Specify your license here, e.g., MIT, GPL-3, etc.]

## Contributing

Contributions, bug reports, and feature requests are welcome! Please:
1. Open an issue to discuss proposed changes
2. Submit pull requests with clear descriptions
3. Include examples demonstrating new features


