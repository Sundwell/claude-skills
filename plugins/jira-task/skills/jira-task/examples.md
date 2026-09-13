# Task description examples
Thirteen real TECH tickets written in this schema, October 2025 onward. This is the style reference - look at structure, phrasing, length, how endpoints and AC are written.

---

## PROJ-1473 | Task | 2026-07-29

**Add "Comment" info to transaction details**

Thread in slack - <slack thread link>

It would be useful for team support to have information about transaction if payout(s) was blocked by something or other problems which causes tx to be in “processing” state

**Backend:**

1. Add field `comment` to endpoint `GET /transactions/:id`

**Acceptance Criteria:**

1. “Comment” should be displayed on Transaction Details page when its status is “Processing”

---

## PROJ-1462 | Task | 2026-07-02

**Killswitches improvements**

Thread in slack - <slack thread link>

This task is aimed to improve user experience with batch processing action and moving killswitches prettified names (labels) to backend, so we can change them at any time without depending on FE

Also we’ve adding a status for “Force Batch Processing” action and displaying and updating it in real time

**Backend**

1. Update `GET /killswitches` - add to each `feature` its `label` field. Now we have `name: alpha-transit` and we’ll have next to it `label: Alpha Transit`

   1. Response example

      ```
        {
          "features": [
              {
                  "id": 136,
                  "name": "beta-pay",
                  "label": "Beta Pay", // new
                  "active": true
              },
              {
                  "id": 135,
                  "name": "gamma-bank",
                  "label": "Gamma Bank" // new
                  "active": true
              },
          ],
          "processingBatchSize": 200,
          "queuedTransactions": 341
      }
      ```
2. New endpoint `GET /killswitches/is-batch-process` - used to get info about current batch processing job

   1. Response example

      ```
      {
        true // or false
      }
      ```
3. Update `POST /killswitches/process-transactions`

   1. Add label for each key: value pair, so it returns `alpha: { label: "Alpha Bank", count: 0 }` instead `alphaCount: 0`, so we can dynamically add/remove pairs on backend and not depend on FE for labels

      ```
      {
          "alpha": { label: "Alpha Bank", count: 0 },
          "alphaInstant": { label: "Alpha Instant", count: 0 },
          "beta-pay": { label: "Beta Pay", count: 0 },
          "gammaBank": { label: "Gamma Bank", count: 0 }
    

... (cut)

---

## PROJ-1450 | Bug | 2026-06-03

**Fix timeout problem while generating merchant report**

When pressing “Generate Merchant Reports” button on Merchant Finance Report Tool page both on dev and staging it frequently throws an error with “connection refused” on reporting-svc.backend:8080

Here is thread with more context <jira comment link>

**Backend**

1. Think how we can fix timeout on `POST /reports/merchant-finance/trigger/` on dev and staging, this timeout appears time-to-time

**Acceptance Criteria**

1. Generate Merchant Reports button works every time it clicked

---

## PROJ-1444 | Story | 2026-05-11

**Add <iframe> tool to test transactions in-place**

Thread in slack <slack thread link>
We need to add small tool to use our onboarding right in the backoffice to simulate some cases, when merchants don’t redirect clients directly to our [checkout.acme.example](http://checkout.acme.example) but instead place withdrawal directly in their product/site/etc. using , so we can test if everything works fine via simulating such <iframe> usage case from backoffice
We will add this tool at the very bottom of the testing tool page
\*\*Frontend\*\*
1. Add a tool “\*\*Test the transaction in iframe\*\*” at the very bottom of the testing tool page, so user can enter transaction URL and proceed with withdrawal in-place
\*\*Acceptance Criteria\*\*
1. The tool “\*\*Test the transaction in iframe\*\*” is presented and the onboarding flow can be completed via video verification as well as via the other options

---

## PROJ-1443 | Story | 2026-05-11

**Show all killswitches on Killswitches page**

For now we display only 4 bank killswitches:
1. Alpha CT Traffic
2. Alpha SEPA Inst Traffic
3. Delta Pay
4. Beta Pay
This list is hardcoded on FE now and is unaffected by backend response
We need to change this logic, so everything that comes from backend should be displayed, for example - Gamma Bank is not displayed on FE, but returned from backend, after this change Gamma Bank should be displayed on Killswitches page as well
Also due to removing hardcoded list we should add generic warning message for all banks instead of dynamic ones, i.e. if bank switch is disabled, warning message will look like ` is disabled. Traffic will be blocked from being sent out to this bank.` , for example, before we had a bit different message for Delta Pay - `Delta Pay is disabled. Transactions will not be added to the CSV.`
Also if we don’t have prettified label for particular bank, it will be displayed as it comes from backend, i.e. `delta-pay` or `gamma-bank` or `sepa-alpha` instead of `Alpha SEPA Inst Traffic`
\*\*Frontend\*\*
1. Display all `features` that come from backend response in the Banks Management section and therefore remove hardcoded list
2. Make generic warning message for all banks, format is ` is disabled. Traffic will be blocked from being sent out to this bank.`
\*\*Acceptance Criteria\*\*
1. `Gamma Bank` killswitch is presented on Killswitches page
2. All disabled killswitches have similar warning format - ` is disabled. Traffic will be blocked from being sent out to this bank.`

---

## PROJ-1442 | Story | 2026-05-08

**Editing "Cleared At" and "Returned At" for Transfer Orders**

Thread in slack with all information - <slack thread link> 

We have “Cleared At” and “Returned At” dates which can be updated throughout the month and as for now it can be done only via database

So it would be great to add possibility to edit those dates right in the backoffice to make support team life easier

**Frontend**

1. Add possibility to edit Cleared At and Returned At for Transfer Orders

    1. Editing is possible only for pre-existing dates, “adding” a date is not possible
    2. No confirmation modal needed
    

**Backend**

1. New endpoint `PATCH /transfer-orders/:orderId` - used to update `clearedAt` and `returnedAt` fields

    1. These fields can only be **edited**, not added. I.e. if `clearedAt` was empty - it can’t be saved via this endpoint
    2. Request example
    
        ```json
        {
          "clearedAt": "yyyy-MM-dd",
          "returnedAt": "yyyy-MM-dd"
        }
        ```
    3. Updated transfer order should be returned in response (note only transfer order ID and updated field(s) will be included in response)
    
2. Update endpoint `GET /transfer-orders/transaction/:transactionId` so `clearedAt` and `returnedAt` dates are returned in year-month-date format without time

**Acceptance Criteria**

1. Transfer Orders’ dates Cleared At and Returned At can be edited and saved one-by-one

    1. Note: Editing is possible only for pre-existing dates, it’s not possible to “add” a date, only edit existing one
    2. Transaction to test on dev - <dev transaction link>

---

## PROJ-1441 | Task | 2026-05-04

**Add fee note to Merchant Rates**

We need to add fee note to all places where Merchant Rates are presented, except Merchant Create page (because we don’t have a merchant entity at that time)
\*\*Frontend\*\*
1. Add fee note
1. Merchant Details page - info card and edit rates modal
2. Message is “Merchant Fee is capped at <5> EUR. Transactions with the Merchant Fee exceeding <5> EUR will be reduced to <5> EUR.”
‌
\*\*Backend\*\*
1. Updated existing endpoint `GET /merchants/` - to return additional field only for merchants who have special fee cap applied to them
1. Response example for merchant with fee cap
```json
{
"merchant": {...},
"merchantConfig": {...},
"merchantRate": {...},
"merchantAccount": {...},
"feeCap": 5.0,
}
```
2. Response example for merchant without fee cap
```json
{
"merchant": {...},
"merchantConfig": {...},
"merchantRate": {...},
"merchantAccount": {...}
}
```
\*\*Acceptance Criteria\*\*
1. Fixed fee message is visible in all places where Merchant Rates
1. Merchant Details page - info card and edit rates modal
2. Message is “Merchant Fee is capped at <5> EUR. Transactions with the Merchant Fee exceeding <5> EUR will be reduced to <5> EUR.”

---

## PROJ-1440 | Task | 2026-04-27

**Permanent recipients list for Merchant Finance Report Tool**

Thread in slack - <slack thread link> 

The main point is to move recipients list for Merchant Finance Report Tool to global, shared across all users, backoffice settings

Basically, we will have a table “Recipients List” on the Merchant Finance Report Tool page similar to Reports => Daily Performance and Reports => Finance Report

**Frontend**

1. Split “Recipients List” into two logic parts

    1. Reading
    
        1. Display emails as a chips
        2. Optimize layout to have good UX and readability after updated structure and 1 more action button
        3. Show first 3 emails, if there are more - display “Show more” button
        
    2. Editing list
    
        1. Add edit modal window with reused Recipients List table from report pages
        
    

**Backend**

1. New endpoint `GET /reports/merchant-finance/recipients` - returns saved recipients

    1. Response example (from `GET /reports/finance/recipients`)
    
        ```json
        {
            "recipients": [
                {
                    "ID": 59,
                    "CreatedAt": "2025-05-07T15:39:05.817696Z",
                    "UpdatedAt": "2025-05-07T15:39:05.817696Z",
                    "DeletedAt": null,
                    "email": "user@example.com",
                    "name": "user@example.com",
                    "active": true,
                    "type": "performance"
                }
            ]
        }
        ```
    
2. New endpoint `POST /reports/merchant-finance/recipients/add`

    1. Request example
    
        ```json
        {
            "recipient": "new.recipient@example.com"
        }
        ```
    2. Should return updated recipients list i

... (cut)

---

## PROJ-1437 | Task | 2026-04-10

**Remove "Chief Executive" from Employment options**

Thread in slack - <slack thread link>

**Frontend**

1. Remove “Chief Executive” option from “Account Opening” step

   1. Make it both in Onboarding ([checkout.acme.example](http://checkout.acme.example)) and Portal ([portal.acme.example](http://portal.acme.example))

**Acceptance Criteria:**

1. “Chief Executive” option from “Account Opening” step is removed from “Employment” field options

   1. This option is removed both in Onboarding and Portal application

---

## PROJ-1421 | Story | 2026-02-23

**Filter unused killswitches**

**Frontend**

Nothing here

---

**Backend**

1. Endpoint `GET /killswitches` should return in response only currently used `features` (return 4 instead of 10)

   1. Currently used killswitches

      1. ```
         [
         
             {
                 "id": 8,
                 "name": "beta-pay",
                 "entityName": "bank",
                 "active": true
             },
             {
                 "id": 7,
                 "name": "delta-pay",
                 "entityName": "bank",
                 "active": true
             },
             {
                 "id": 1,
                 "name": "alpha",
                 "entityName": "bank",
                 "active": true
             },
             {
                 "id": 5,
                 "name": "sepa-alpha",
                 "entityName": "bank",
                 "active": true
             }
         ]
         ```

---

**Acceptance Criteria:**

1. On the Killswitches page endpoint `GET /killswitches` should return 4 features instead of 10, below is example how to check (on the video there are 10 features indexed from 0 to 9)

---

## PROJ-1415 | Task | 2026-01-23

**Killswitches - Add "Change Reason"**

Support team usually [writes the reason](<slack thread link>) before turning something on/off, so it would be useful for everyone to store this reason and automatically write it in the slack before other “KILL SWITCH UPDATE” [helper messages](<slack thread link>)

**Frontend**

1. When saving killswitches in the “Confirm Changes” modal user **must** enter mandatory “Change Reason” before saving
2. “Change Reason” is properly displayed in the Audit Trail logs

---

**Backend**

1. Update `PATCH /killswitches` endpoint

   1. Add `changeReason` field to request body. This field should be mandatory string
2. Add “Change Reason” to the Audit Trail logs

   1. Format should be “Change Reason: [value]” as usually for “common” updates
   2. Example

      1. `Feature Toggle ID: 1, Name: alpha updated to active: true|||Change Reason: example reason text`

---

**Acceptance Criteria**

1. “Change Reason” mandatory text input is presented in “Confirm Changes” modal which opens upon “Save” button press
2. “Change Reason” is presented in audit trail logs for all new killswitches updates

---

## PROJ-1404 | Sub-task | 2025-10-27

**[BE] Unified Onboarding - Merchant Creation Process API | Backoffice**

As a Acme business manager, I would like to create Merchant credentials faster, without duplicate manual work involved, so that we can onboard merchants faster.

**Overall description**

1. When user creates merchant in PRODUCTION and (possibly) creates his first merchant dashboard user - the same merchant with his first (optional) dashboard user should be created on STAGING as well. It is only for creation process i.e. no live synchronization, just one operation

   1. *Whenever the Merchant Creation process is started from the Production Backoffice, credentials will be created for both (Staging and Production), using the data that was input in Production.*
   2. *Once credentials are created, and Create a new User for the Merchant Dashboard prompt appears, the same user / password combination is then applied for both Staging & Production.*
2. **[New on frontend]** As the next step, a prompt appears asking to fill an email address field if we want to send the onboarding information. The field accepts an email address or multiple addresses, separated by colon, or semi-colon
3. A welcome email is sent to those addresses using the following email template, nicely branded with Acme logo:

   ***Subject****: Welcome to Acme - credentials*

   *Hello,*

   *Here are your Acme credentials:*

   *ExampleMerchant Staging : <uuid>*

   <staging dashboard link>

   *ops@merchant.example*

   *<password>*

   *ExampleMerchant Production: <uuid>*

   <production dashboard link>

   *ops@merchant.example*

   *<password>*

   *Bank Account Top-up details:*

   *Account Holder: <account holder>*

   *<IBAN>*
4. All of the above are PLACEHOLDERS, so they will be filled with the actual data

... (cut)

---

## PROJ-1396 | Task | 2025-10-07

**[BE] Account Statements API | Backoffice**

Account Statements has quite similar functionality (at least from FE part) as Merchant Finance Report Tool

Thread in slack - <slack thread link>

**Logic description**

labelling is automatically done on backend side via keywords in the statement description ([check this doc](<spreadsheet link>) for examples)

1. Examples

   1. John Doe | <IBAN> | SEPA Payments Bitcoin Sale <uuid> - **"Purchase Crypto"** type, cuz it has uuid / bitcoin sale keyword
   2. | <BIC> | SEPA Payments Returned Transaction MS03 - **"Refund crypto purchase"** type due to "Returned" keyword (it can be "Return", "RETURN", RTN, rtn and kinda these keywords which we will populate time to time as I understood)
   3. PWE - Payments Charges | Charges - **"Bank fees"** type
2. All of them generated automatically by some banking system and always (at least for now) have same structure

**Acceptance Criteria:**

1. Accounting Report Tool page

   1. New endpoint - `GET /reports/accounting/get`. Used to get previous account statements reports

      1. Pagination is presented
      2. Response example

         1. ```
            {
              "reports": [
                {
                  "id": 123,
                  "createdAt": "2025-10-07T12:08:49.062178Z",
                  "month": 9,
                  "year": 2025,
                  "file": "name.xlsx"
                },
                {...}
              ],
              "total": 17
            }
            ```
   2. New endpoint - `POST /reports/accounting/trigger`. Used to manually trigger report generation

      1. Should be unavai

... (cut)
