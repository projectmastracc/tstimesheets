# Trades Timesheet & Admin Verification System

A **zero-cost, GitHub-only** timesheet tracking system. Engineers submit weekly hours via a simple form (no tech knowledge required), admins verify with their token, and managers approve—all data stored securely as GitHub Issues.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│  Engineer (index.html) - Simple Form - NO Token Needed              │
│  ✓ Enter name and weekly hours via form                             │
│  ✓ Click submit → Creates GitHub Issue automatically               │
│  ✓ No GitHub account or tech knowledge required                    │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
              GitHub Issues (Private Repository)
              Auto-labeled: timesheet, pending-review
                           │
        ┌──────────────────┴──────────────────┐
        ▼                                      ▼
┌──────────────────────┐              ┌──────────────────────┐
│  Admin (admin.html)  │              │  Manager (admin.html)│
│  ✓ Paste admin token │              │  ✓ Paste admin token │
│  ✓ View all issues   │              │  ✓ Approve & process │
│  ✓ Verify accuracy   │              │  ✓ Export for payroll│
│  ✓ Export CSV        │              │  ✓ Add BACS labels   │
└──────────────────────┘              └──────────────────────┘
```

## Quick Start

### Step 1: Create Two GitHub Tokens

#### Token A: Submission Token (Limited - Embedded in Engineer Form)
This token is intentionally public but has **minimal permissions** (write-only for issues in this repo):

1. Go to **GitHub Settings → Developer settings → Personal access tokens → Fine-grained tokens**
2. Click **Generate new token**
3. Name: `tstimesheets-submission`
4. Expiration: 90 days
5. **Repository access**: `tstimesheets` only
6. **Permissions**: 
   - Issues: **Write-only** ✓
   - All other permissions: **None**
7. Click **Generate token** and **copy it**
8. Paste this token into [index.html](index.html) line 14:
   ```javascript
   const SUBMISSION_TOKEN = 'github_pat_YOUR_TOKEN_HERE';
   ```
9. Commit and push this change (it's safe—token is write-only)

#### Token B: Admin Token (Full Access - For Admin Only)
This token is used by admins/managers to view, verify, and approve timesheets:

1. Go to **GitHub Settings → Developer settings → Personal access tokens → Fine-grained tokens**
2. Click **Generate new token**
3. Name: `tstimesheets-admin`
4. Expiration: 90 days
5. **Repository access**: `tstimesheets` only
6. **Permissions**:
   - Issues: **Read & Write** ✓
   - All other permissions: **None**
7. Click **Generate token** and **copy it**
8. **DO NOT commit this token to the repo** — keep it private
9. Use this token when accessing `admin.html`

### Step 2: Use the System

#### **For Engineers** (`index.html`)

1. **Open the Engineer Form**: Go to your site's `index.html`
2. **That's it—no token needed!** You should see:
   - A simple form asking for your name
   - Fields for Monday through Friday
   - A big Submit button
3. **Fill in your timesheet**:
   - Select your name from dropdown
   - Choose the week commencing date (Monday)
   - Enter hours, job type, and short description for each day
4. **Click Submit**:
   - Your timesheet is instantly posted as a GitHub Issue
   - You'll see a success message
   - The form clears and you're done

#### **For Admin/Manager** (`admin.html`)

1. **Open the Admin Dashboard**: Go to your site's `admin.html`
2. **Paste your admin token**:
   - In the "Admin Token Required" section
   - Paste your `tstimesheets-admin` token (the one you created above, NOT the submission token)
   - Click **🔐 Unlock Admin Panel**
3. **Review Submissions**:
   - The dashboard loads all timesheet issues
   - Filter by status: Pending Review, Admin Verified, Manager Approved
4. **Verify (Admin Step)**:
   - Review the hours and descriptions
   - Check for accuracy/completeness
   - Click **✔️ Verify** to mark as admin-verified
5. **Approve (Manager Step)**:
   - After admin verification, click **💰 Approve** to mark as manager-approved
   - This signals the entry is ready for payroll processing
6. **Export**:
   - Click **📥 Export to CSV** to download for payroll, accounting, or records

## File Structure

```
tstimesheets/
├── index.html          # Engineer submission form (update submission token here)
├── admin.html          # Admin/Manager tracking dashboard
├── README.md           # This file
├── TESTING.md          # Testing checklist
└── .git/
```

## Key Features

### ✅ Engineer Submission (`index.html`)
- **Simple, no-tech UX**: Just name and form
- **Mobile-friendly**: Works on phones for on-the-go submission
- **No authentication required**: Engineers don't need GitHub accounts
- **Auto-creates GitHub Issues** with structured timesheet data
- **Automatic labeling**: All issues tagged `timesheet` + `pending-review`
- **Success notifications**: Clear feedback on submission status

### ✅ Admin Dashboard (`admin.html`)
- **Real-time data**: Fetches live timesheet issues from GitHub
- **Structured view**: Daily breakdown with hours, job, description for each engineer
- **Status management**:
  - **Pending Review** (red): New submissions awaiting admin check
  - **Admin Verified** (blue): Admin has reviewed and confirmed accuracy
  - **Manager Approved** (green): Manager approved for payroll processing
- **Action buttons**:
  - **✔️ Verify**: Adds `admin-verified` label
  - **💰 Approve**: Adds `manager-approved` label (payroll-ready)
- **Filtering**: View by status (all, pending, verified, approved)
- **CSV Export**: Download verified timesheets for payroll
- **Refresh**: Sync latest data from GitHub
- **Audit Trail**: Every action logged in GitHub issue history + labels

### 🔒 Security Model

| Component | Security |
|-----------|----------|
| **Engineer Form** | Submission token: Write-only for issues in this repo. No risk of data leakage. |
| **Admin Dashboard** | Admin token: Full read/write, used only by admins. Personal and private. |
| **Data Storage** | GitHub Issues (private repo). Built-in access control and audit trail. |
| **Token Protection** | Stored in browser sessionStorage only. Cleared when browser closes. |
| **Repository** | Private repository (with GitHub Pro) or public with GitHub Pages access restrictions. |

## Important Security Notes

⚠️ **The submission token in `index.html` is intentionally public** because:
- It's hardcoded in the HTML (can't be hidden)
- It has minimal permissions (write-only for issues)
- It can only create new issues, not read or modify them
- Engineers can't use it to see other timesheets or break anything else

✅ **The admin token is private**:
- Never commit this to the repo
- Paste it only when you need to access the admin dashboard
- It has full read/write on issues but only for this repo
- It's stored in browser memory only (cleared on browser close)

## Troubleshooting

### "GitHub API error: 401"
- Admin token is invalid or expired
- Check that you copied the token correctly (no extra spaces)
- Verify the token still exists (might have been revoked)
- Generate a new admin token if needed

### "GitHub API error: 403"
- Admin token doesn't have proper permissions
- Ensure you set **Issues: Read & Write** when creating the token
- Ensure token is scoped to `tstimesheets` repository only

### Engineer form won't submit
- Check browser console (F12) for errors
- Verify the **submission token is valid** (if it expires, admin needs to update `index.html`)
- Ensure all form fields are filled

### No timesheet issues showing in dashboard
- Refresh the admin page (**🔄 Refresh** button)
- Verify your admin token is saved
- Check that you're using the **admin token**, not the submission token
- Verify the repository has the `timesheet` label

### CSV Export appears empty
- Make sure your filter includes the data you want
- Refresh the dashboard first
- Try "All Submissions" filter if viewing specific statuses

## Workflow Example

**Monday Morning:**
- John Doe submits his timesheet for the week at 8 AM
- GitHub Issue auto-created with label `pending-review`

**Wednesday Afternoon:**
- Admin reviews John's submission
- Clicks **✔️ Verify** after checking hours
- GitHub Issue now has label `admin-verified`

**Friday Morning:**
- Manager reviews verified submissions
- Clicks **💰 Approve** for payroll
- GitHub Issue now has label `manager-approved`
- Manager exports CSV with all approved timesheets
- Feeds data into payroll system

**Audit Trail:**
- GitHub stores complete history of all submissions
- Labels show review status
- Issue comments/edits are logged automatically

## Upgrading for Privacy (GitHub Pro)

If you upgrade to GitHub Pro ($4/month):
1. Make the repository **Private**
2. GitHub Pages automatically restricts to repo collaborators only
3. Engineers still access via simple public URL (GitHub login required)
4. Admin dashboard access is also restricted to collaborators

## Future Enhancements

- Email notifications when status changes
- Recurring timesheet templates
- Integration with payroll systems (QuickBooks, etc.)
- Bulk operations (approve multiple at once)
- Time tracking API for automated logging
- Mobile app version

## Support

For issues or improvements:
1. Create a GitHub Issue on this repository
2. Describe the problem
3. Include screenshots if helpful

---

**Made with ❤️ for the Trades** — No databases, no subscriptions, just GitHub Issues.

