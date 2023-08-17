---
title: Tax document data extraction – Document Intelligence (formerly Form Recognizer)
titleSuffix: Azure AI services
description: Automate tax document data extraction with Document Intelligence's tax document models
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/18/2023
ms.author: lajanuar
monikerRange: 'doc-intel-3.1.0'
---

<!-- markdownlint-disable MD033 -->

# Document Intelligence tax document model

[!INCLUDE [applies to v3.1](includes/applies-to-v3-1.md)]

The Document Intelligence contract model uses powerful Optical Character Recognition (OCR) capabilities to analyze and extract key fields and line items from a select group of tax documents. Tax documents can be of various formats and quality including phone-captured images, scanned documents, and digital PDFs. The API analyzes document text; extracts key information such as customer name, billing address, due date, and amount due; and returns a structured JSON data representation. The model currently supports certain English tax document formats.

**Supported document types:**

* W-2
* 1098
* 1098-E
* 1098-T

## Automated tax document processing

Automated tax document processing is the process of extracting key fields from tax documents. Historically, tax documents have been done manually this model allows for the easy automation of tax scenarios

## Development options

Document Intelligence v3.0 supports the following tools:

| Feature | Resources | Model ID |
|----------|-------------|-----------|
|**Tax model** |&#9679; [**Document Intelligence Studio**](https://formrecognizer.appliedai.azure.com)</br> &#9679; [**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2022-08-31/operations/AnalyzeDocument)</br> &#9679; [**C# SDK**](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</br> &#9679; [**Python SDK**](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</br> &#9679; [**Java SDK**](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</br> &#9679; [**JavaScript SDK**](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)|**prebuilt-tax.us.W-2**</br>**prebuilt-tax.us.1098**</br>**prebuilt-tax.us.1098E**</br>**prebuilt-tax.us.1098T**|

## Input requirements

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## Try tax document data extraction

See how data, including customer information, vendor details, and line items, is extracted from invoices. You need the following resources:

* An Azure subscription—you can [create one for free](https://azure.microsoft.com/free/cognitive-services/)

* A [Form Recognizer instance (Document Intelligence forthcoming)](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) in the Azure portal. You can use the free pricing tier (`F0`) to try the service. After your resource deploys, select **Go to resource** to get your key and endpoint.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot of keys and endpoint location in the Azure portal.":::

## Document Intelligence Studio

1. On the Document Intelligence Studio home page, select **Tax Documents**

1. You can analyze the sample tax documents or select the **+ Add** button to upload your own sample.

1. Select the **Analyze** button:

    :::image type="content" source="media/studio/invoice-analyze.png" alt-text="Screenshot of the analyze invoice menu.":::

> [!div class="nextstepaction"]
> [Try Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

## Supported languages and locales

>[!NOTE]
> Document Intelligence auto-detects language and locale data.

| Supported languages | Details |
|:----------------------|:---------|
| English (en) | United States (us)|

## Field extraction W-2

The following are the fields extracted from a W-2 tax form in the JSON output response.

|Name| Type | Description | Example output |
|:-----|:----|:----|:---:|
| `W-2FormVariant`| String | IR W-2 Form variant. This field can have the one of the following values: `W-2`, `W-2AS`, `W-2CM`, `W-2GU`, or `W-2VI`| W-2 |
| `TaxYear` | Number | Form tax year| 2021 |
| `W2Copy` | String | W-2 tax copy version along with printed instruction related to this copy| Copy A—For Social Security Administration |
| `Employee`| object | Object that contains social security number, name, and address| |
| `ControlNumber` | string | W-2 control number. IRS W-2 field d| 0AB12 D345 7890 |
| `Employer` | Object | Object that contains employer identification number, name and address|  |
| `WagesTipsAndOtherCompensation` | Number | Wages, tips, and other compensation amount in USD. IRS W-2 field 1| 1234567.89 |
| `FederalIncomeTaxWithheld` | Number | Federal income tax withheld amount in USD. IRS W-2 field 2| 1234567.89 |
| `SocialSecurityWages` | Number | Social security wages amount in USD. IRS W-2 field 3| 1234567.89 |
| `SocialSecurityTaxWithheld` | Number | Social security tax withheld amount in USD. IRS W-2 field 4| 1234567.89 |
| `MedicareWagesAndTips` | Number | Medicare wages and tips amount in USD. IRS W-2 field 5| 1234567.89 |
| `MedicareTaxWithheld` | Number | Medicare tax withheld amount in USD. IRS W-2 field 6| 1234567.89 |
| `SocialSecurityTips` | Number | Social security tips amount in USD. IRS W-2 field 7| 1234567.89 |
| `AllocatedTips` | Number | Allocated tips in USD. IRS W-2 field 8| 1234567.89 |
| `VerificationCode` | Number |W-2 verification code. IRS W-2 field 9| 1234567.89 |
| `DependentCareBenefits` | Number | Dependent care benefits amount in USD. IRS W-2 field 10| 1234567.89 |
| `NonQualifiedPlans` | Number | Non qualified plans amount in USD. IRS W-2 field 11| 1234567.89 |
| `IsStatutoryEmployee` |String| Part of IRS W-2 field 13. Can be 'true' or 'false'| true |
| `IsRetirementPlan` |String| Part of IRS W-2 field 13. Can be 'true' or 'false'| true |
| `IsThirdPartySickPay` |String| Part of IRS W-2 field 13. Can be 'true' or 'false'| true |
| `Other` | String | Content of IRS W-2 field 14| SICK LV WAGES SBJT TO $511/DAY LIMIT 1356 |
| `StateTaxInfos` | Array | State tax-related information. content of IRS W-2 field 15 to 17| |
| `LocaleTaxInfos` | Array |Local tax-related information. Content of IRS W-2 field 18 to 20| |

## Field extraction 1098

The following are the fields extracted from a1098 tax form in the JSON output response.

|Name| Type | Description | Example output |
|:-----|:----|:----|:---:|
| TaxYear | Number | Form tax year| 2021 |
| Borrower | Object | An object that contains the borrower's TIN, Name, Address, and AccountNumber | |
| Lender | Object | An object that contains the lender's TIN, Name, Address, and Telephone| |
| MortgageInterest |Number| Mortgage Interest amount received from  payer(s)/borrower(s) (box 1)| 1,234,567.89
|OutstandingMortgagePrincipal |Number| Outstanding mortgage principal (box 2) |1,234,567.89|
| MortgageOriginationDate |Date| Origination date of the mortgage (box 3) |2022-01-01|
| OverpaidInterestRefund |Number| Refund amount of overpaid interest (box 4)| 1,234,567.89
| MortgageInsurancePremium |Number| Mortgage insurance premium amount (box 5) | 1,234,567.89
| PointsPaid |Number| Points paid on purchase of principal residence (Box 6)| 1,234,567.89
| IsPropertyAddressSameAsBorrower |String| Is the address of the property securing the mortgage the same as the payer's/borrower's mailing address (box 7)| true|
| PropertyAddress |String| Address or description of the property securing the mortgage (box 8) | 123 Main St., Redmond WA 98052 |
| MortgagedPropertiesCount |Number| Number of mortgaged properties (box 9)| 1|
| Other |String| Additional information to report to payer (box 10)| |
| RealEstateTax |Number|Real estate tax (box 1)| 1,234,567.89|
| AdditionalAssessment |String| Added assessments made on the property  (box 10)| 1,234,567.89|
| MortgageAcquisitionDate |date | Mortgage acquisition date (box 11)| 2022-01-01|

### Field extraction 1098-T

The following are the fields extracted from a1098-E tax form in the JSON output response.

|Name| Type | Description | Example output |
|:-----|:----|:----|:---:|
| Student | Object | An object that contains the borrower's TIN, Name, Address, and AccountNumber | |
| Filer | Object | An object that contains the lender's TIN, Name, Address, and Telephone| |
| PaymentReceived | Number | Payment received for qualified tuition and related expenses (box 1)| 1234567.89 |
| Scholarships | Number |Scholarships or grants (box 5)| 1234567.89 |
| ScholarshipsAdjustments | Number | Adjustments of scholarships or grants for a prior year (box 6) 1234567.89 |
| AdjustmentsForPriorYear | Number | Adjustments of payments for a prior year (box 4)| 1234567.89 |
| IncludesAmountForNextPeriod |String| Does payment received relate to an academic period beginning in the next tax year (box 7)| true |
| IsAtLeastHalfTimeStudent |String| Was the student at least a half-time student during any academic period in this tax year (box 8)| true |
| IsGraduateStudent |String| Was the student a graduate student (box 9)| true |
| InsuranceContractReimbursements | Number | Total number and amounts of reimbursements or refunds of qualified tuition and related expanses (box 10)| 1234567.89 |

## Field extraction 1098-E

The following are the fields extracted from a1098-T tax form in the JSON output response.

|Name| Type | Description | Example output |
|:-----|:----|:----|:---:|
| TaxYear | Number | Form tax year| 2021 |
| Borrower | Object | An object that contains the borrower's TIN, Name, Address, and AccountNumber | |
| Lender | Object | An object that contains the lender's TIN, Name, Address, and Telephone| |
| StudentLoanInterest |number| Student loan interest received by lender (box 1)| 1234567.89 |
| ExcludesFeesOrInterest |string| Does box 1 exclude loan origination fees and/or capitalized interest (box 2)| true |

The tax documents key-value pairs and line items extracted are in the `documentResults` section of the JSON output.

## Next steps

* Try processing your own forms and documents with the [Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio)

* Complete a [Document Intelligence quickstart](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) and get started creating a document processing app in the development language of your choice.
