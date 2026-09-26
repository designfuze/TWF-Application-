# Tabasheer Welfare Foundation — Talent Hunt Examination Portal

## GitHub + Vercel Deployment Guide

This project contains the **Tabasheer Welfare Foundation Talent Hunt Examination** frontend and Google Apps Script backend.

> **Important:** `Index.html` can be hosted on GitHub/Vercel, but `Code.gs` cannot run directly on Vercel. `Code.gs` must remain deployed as a Google Apps Script Web App unless the backend is rewritten for Vercel/Node.js.

---

## 1. Current Assets

### TWF Logo / Favicon

```text
1YckO15_oT9dspFSpIgIgt-otTlLXUa-E
```

### TWF Official Signature

```text
1d2hbH0xPa35iqU5Ckp3eDXiy1Y_v07Q5
```

The same TWF logo is used for the portal logo and favicon.

The TWF signature is used on:

- Admit Card — bottom-right
- Result — bottom-right

---

## 2. Project Structure

For a Vercel frontend, use a structure similar to:

```text
Tabasheer-Talent-Hunt/
│
├── index.html
├── README.md
└── assets/
    └── (optional local assets)
```

`Code.gs` is kept separately in the Google Apps Script project as the backend.

---

## 3. Google Apps Script Backend

The existing `Code.gs` remains responsible for:

- Google Sheets data
- Application submission
- Application ID generation
- Student photo handling
- Admit Card Management
- Result Management
- Exam Settings
- Examination Date
- TWF signature loading
- TWF logo loading
- Confirmation emails
- Admin emails

Deploy `Code.gs` as a Google Apps Script Web App.

Use the deployed `/exec` URL as the frontend backend/API URL.

Example:

```text
https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec
```

Do **not** use the `/dev` URL for production.

---

## 4. Vercel Frontend

The `Index.html` file is the frontend.

Upload the frontend project to GitHub and import that repository into Vercel.

### GitHub

1. Create a new GitHub repository.
2. Upload the current `Index.html`.
3. Rename it to:

```text
index.html
```

4. Commit the changes.

### Vercel

1. Open Vercel.
2. Choose **Add New → Project**.
3. Import the GitHub repository.
4. For a plain HTML project, no framework is required.
5. Deploy.

Vercel will provide a URL such as:

```text
https://your-project.vercel.app
```

---

## 5. Connect Vercel Frontend to Google Apps Script

The frontend must know the deployed Apps Script Web App URL.

In `index.html`, use the production Apps Script `/exec` URL wherever the frontend calls Google Apps Script.

Example:

```javascript
const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
```

Replace `YOUR_DEPLOYMENT_ID` with the actual deployment ID.

> Do not put the Apps Script `/dev` URL in production.

---

## 6. Important CORS / Request Note

A Vercel-hosted frontend and Google Apps Script backend are different origins.

If the current frontend uses `google.script.run`, it will **not work when the HTML is hosted on Vercel**, because `google.script.run` is available only inside an Apps Script HTML-service page.

For Vercel hosting, the frontend must communicate with the Apps Script backend through HTTP requests to the deployed `/exec` endpoint.

That means functions such as:

```javascript
google.script.run.submitApplication(...)
```

must be changed to an HTTP/API request pattern compatible with the Apps Script backend.

Do not deploy the current Apps Script HTML unchanged to Vercel if it still depends on `google.script.run`.

---

## 7. Recommended Production Architecture

```text
                         Student
                            │
                            ▼
                https://tabasheer.in/...
                            │
                            ▼
                  Vercel Frontend
                     index.html
                            │
                            │ HTTPS requests
                            ▼
              Google Apps Script Web App
                         Code.gs
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
        Google Sheets    Google Drive    Email
             │              │
             │              ├── TWF Logo
             │              └── TWF Signature
             │
             ▼
       Applications / Management
```

---

## 8. Existing Google Sheets Structure

### Applications

```text
Timestamp
Application ID
Student Name
Gender
Date of Birth
Phone
Email
Present School Name
10th Board
Other Board Name
District
Exam Centre
Exam Paper Medium
Full Address
Photo File ID
Photo File URL
Admit Card Available Date
Declaration
```

### Admit Card Management

```text
Application ID
Student Name
Gender
Date of Birth
Present School Name
10th Board
District
Exam Centre
Exam Paper Medium
Full Address
Application Date
Photo File ID
Admit Card Live?
```

### Result Management

```text
Application ID
Student Name
DOB / Date of Birth
10th Board
Exam Medium / Exam Paper Medium
Result Status
Marks
Rank
Remarks
Result Live?
```

### Exam Settings

```text
A1 = Setting
B1 = Value
A2 = Examination Date
B2 = Examination Date Value
```

Only **B2** needs to be changed for a new examination date.

---

## 9. Production Rules

### Admit Card

The Admit Card is controlled by:

```text
Admit Card Management → Admit Card Live?
```

There is no old 10-day waiting rule.

### Result

The Result is controlled by:

```text
Result Management → Result Live?
```

### Application ID

Current format:

```text
TWF-TH-00110-DF
TWF-TH-00111-DF
TWF-TH-00112-DF
...
```

### Examination Date

The public portal reads the examination date from:

```text
Exam Settings → B2
```

---

## 10. TWF Branding

### Portal

- Tabasheer Welfare Foundation
- Talent Hunt Examination
- Current year registration
- TWF logo
- TWF favicon

### Admit Card

- TWF logo
- Student information
- Examination information
- Student photo
- Examination date
- TWF signature at bottom-right
- Authorized Signatory
- Tabasheer Welfare Foundation

### Result

- TWF logo
- Student result information
- Marks
- Rank
- Result status
- Remarks
- TWF signature at bottom-right
- Authorized Signatory
- Tabasheer Welfare Foundation

---

## 11. Custom Domain

After the Vercel project is working, the Vercel project can be connected to the desired `tabasheer.in` domain/subdomain from Vercel's **Domains** settings.

For example:

```text
https://exam.tabasheer.in
```

or another preferred subdomain.

If the existing WordPress URL must remain:

```text
https://tabasheer.in/scholarship-application-2/
```

then the WordPress page can link to the Vercel application, or the application can be integrated into the WordPress page separately.

---

## 12. Important Before Production

Before publishing the Vercel version, verify:

- [ ] Apps Script backend is deployed as `/exec`
- [ ] Frontend uses `/exec`, not `/dev`
- [ ] All `google.script.run` calls are replaced if the frontend is hosted outside Apps Script
- [ ] Application submission works
- [ ] Application ID is generated correctly
- [ ] Student photo upload works
- [ ] Admit Card Live/Not Live works
- [ ] Admit Card shows TWF logo
- [ ] Admit Card shows TWF signature at bottom-right
- [ ] Examination Date is displayed
- [ ] Result Live/Not Live works
- [ ] Result shows TWF logo
- [ ] Result shows TWF signature at bottom-right
- [ ] Result marks/rank/remarks are displayed correctly
- [ ] Mobile layout works
- [ ] Favicon appears in the browser tab
- [ ] Google Drive permissions allow the Apps Script backend to read the logo/signature files

---

## 13. Important Deployment Note

Do not delete or replace the Google Apps Script backend just because the frontend is moved to Vercel.

Vercel hosts the frontend.

Google Apps Script continues to provide the backend until the backend is intentionally migrated to another server/API platform.

```text
Vercel = Frontend
Google Apps Script = Backend
Google Sheets = Database
Google Drive = File Storage
```

This separation is the safest way to move the current portal to GitHub + Vercel without losing the existing Google Sheets and Drive workflow.
