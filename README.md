

# Bincheck.io – Google Sheet 

>  This code is for Google Apps Script only.
> Do NOT deploy or run this from GitHub.

## Usage
- Open Google Sheet
- Extensions → Apps Script
- Paste the code below
- Add your BINCheck API key
- Run function: BIN_LOOKUP

---

## Script

```javascript


const BINCHECK_API = "YOUR_API_KEY_HERE";

function BIN_LOOKUP() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const lastRow = sheet.getLastRow();

  for (let row = 2; row <= lastRow; row++) {
    let value = sheet.getRange(row, 1).getValue();
    if (!value) continue;

    let card = value.toString().replace(/\s+/g, "");
    if (!/^\d{6,}$/.test(card)) continue;

    let bin = card.substring(0, 6);
    let url = `https://bincheck.io/api/v2/bin/${bin}`;

    let options = {
      method: "get",
      headers: {
        "Authorization": "Bearer " + BINCHECK_API
      },
      muteHttpExceptions: true
    };

    let response = UrlFetchApp.fetch(url, options);
    let data = JSON.parse(response.getContentText());
    if (!data.success) continue;

    let b = data.BIN;

    sheet.getRange(row, 2).setValue(b.scheme || "");
    sheet.getRange(row, 3).setValue(b.issuer?.name || "");
    sheet.getRange(row, 4).setValue(b.country?.name || "");
    sheet.getRange(row, 5).setValue(b.type || "");
    sheet.getRange(row, 6).setValue(b.level || "");
  }
}


/************************************
| Column | Purpose               |
| ------ | --------------------- |
| A      | Card number / BIN     |
| B      | Scheme (VISA / MC)    |
| C      | Bank                  |
| D      | Country               |
| E      | Type (Debit / Credit) |
| F      | Level                 |



#Example input:
A2 = 457562

#OUTPUT
A2: 457562
B2: VISA
C2: HDFC Bank
D2: India
E2: Debit
F2: Classic
************************************/




