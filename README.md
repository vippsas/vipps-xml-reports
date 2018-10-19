# Vipps XML reports

The contents of this repository has moved to
[Settlements](https://github.com/vippsas/vipps-developers/tree/master/settlements)

The old content is kept here, for reference.

## Old contents, for reference

This repository contains schemas and example files for Vipps XML settlement reports.

The new settlement report schema v3.0 can be found in the [schemas](schemas) directory together with the old v2.0 version for comparison.

Example files following schema v3.0 are included in the [examples](examples) directory.

### Changelog

#### Changes to Vipps settlement report XML schema v2.0 to v3.0

NB! New settlements will contain a mix of captures and refunds.
To make the numbers unambiguous we have introduced new fields
for capture and refund but kept gross and net fields as before.

- Schema changes from v2.0 to v3.0:
    - Old schema URL for v2.0 was http://vipps.dnb.no/XMLSchema/SettlementReport.xsd
    - New schema URL is http://vipps.no/xmlschema/SettlementReport-3.0.xsd
    - New schema validates all amount fields with new types "money", "positiveMoney", and "negativeMoney"
    - Other changed organized by parent element below

- Changes to PaymentsInfo:
    - ReportDateFrom and ReportDateTo fields:
        - Drop time part, keep only date (in yyyy-mm-dd format)
        - Change schema type from xs:string to xs:date
    - Remove control sums (TotalSettledGrossAmount, TotalSettledNetAmount, TotalSettledFeeAmount, and TotalSettledRefundAmount)
    - Move NumOfSettlements after SettlementInfo blocks to facilitate future streaming optimizations for large files

- Changes to TransactionInfo:
    - Rename TransactionDate to TransactionTime and:
        - Change type from xs:string to xs:dateTime
        - Fix timezone bug from previous report system where time UTC formatting was applied to Oslo time.
        - Now always Oslo timezone, consistent with dates
    - Change type of TransactionID from xs:string to xs:long
    - Add field TransactionCaptureAmount (always positive)
    - Add field TransactionRefundAmount (always negative)
    - Note that TransactionGrossAmount = TransactionCaptureAmount + TransactionRefundAmount

- Changes to SettlementInfo:
    - Rename SettlementBatchDate to SettlementDate and:
        - Drop time part and change type from xs:string to xs:date
        - For new settlements, this date is within the inclusive range [ReportDateFrom, ReportDateTo] and is equal to or later than the date of the last transaction within the settlement
        - Note that the bank transfer will typically occur at a later date
    - Change type of SettlementID from xs:string to xs:long
    - Move NumOfTransactions and all amounts to below TransactionInfo fields, to facilitate future streaming optimizations for large files
    - Add field SettlementType ("Net" or "Gross")
    - Add field SettledAmount, which is the amount paid out or invoiced (net or gross depending on settlement type)
    - Add field CaptureSettlementAmount, sum of TransactionCaptureAmount fields
    - Add field RefundSettlementAmount, sum of TransactionRefundAmount fields
    - Note that GrossSettlementAmount is still the sum of TransactionGrossAmount fields
    - Note that GrossSettlementAmount = CaptureSettlementAmount + RefundSettlementAmount

- Changes to FeeInfo:
    - FeeInfo will only be included for old reports with gross settlement type
    - Change type of FeeDate from xs:string to xs:date
    - Change type of FeeAccount from xs:long to xs:string

- Changes to SettlementDetailsInfo:
    - Change type of MainAddressCity from xs:NCName to xs:string

- Changes to VippsInfo:
    - Change type of WebSite from xs:NCName to xs:anyURI
    - Change type of Country from xs:NCName to xs:string
