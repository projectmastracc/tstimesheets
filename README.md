# Trades Timesheet & Admin Verification System

A **zero-cost, GitHub-only** timesheet tracking system. Engineers submit weekly hours, admins verify, and managers approve—all data stored securely as GitHub Issues with no external database required.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│  Engineer (index.html) - Private GitHub Pages                       │
│  ✓ Enter weekly hours via form                                      │
│  ✓ Submit → Creates GitHub Issue automatically                      │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
              GitHub Issues (Private Repository)
              Labels: timesheet, pending-review, admin-verified, manager-approved
                           │
        ┌──────────────────┴──────────────────┐
        ▼                                      ▼
┌──────────────────────┐              ┌──────────────────────┐
│  Admin (admin.html)  │              │  Manager (admin.html)│
│  ✓ View submissions  │              │  ✓ Approve & Process│
│  ✓ Verify accuracy   │              │  ✓ Add BACS labels  │
│  ✓ Export CSV        │              │  ✓ Export to payroll│
└──────────────────────┘              └──────────────────────┘
```

## Quick Start

### 1. Create a Fine-Grained Personal Access Token

Follow [GitHub's documentation](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token):

1. Go to **GitHub Settings → Developer settings → Personal access tokens → Fine-grained tokens**
2. Click **Generate new token**
3. Name: `tstimesheets-token`
4. Expiration: Set as needed (90 days recommended for security)
5. **Repository access**: Select **Only select repositories** → `tstimesheets`
6. **Permissions**: Under "Repository permissions", set:
   - **Issues**: Read & Write ✓
7. Click **Generate token**
8. **Copy the token immediately** (you won't see it again)

### 2. Use the System

#### **For Engineers** (`index.html`)

1. **Open the Engineer Submission Form**: Go to `index.html` in your browser
2. **Paste your GitHub PAT**:
   - In the yellow "GitHub Token Required" section at the top
   - Paste the token you generated above
   - Click **💾 Save Token** (saved securely in browser session only)
3. **Fill in your timesheet**:
   - Select your name
   - Choose the week commencing date (Monday)
   - Enter hours, job type, and description for each day
4. **Submit**: Click **Submit Timesheet**
   - Your submission becomes a new GitHub Issue automatically
   - You'll see a success message

#### **For Admin/Manager** (`admin.html`)

1. **Open the Admin Dashboard**: Go to `admin.html` in your browser
2. **Paste your GitHub PAT** (same process as engineers)
3. **Review Submissions**:
   - The dashboard shows all timesheet issues
   - Filter by status: Pending Review, Admin Verified, Manager Approved
4. **Verify (Admin Step)**:
   - Review the hours and descriptions
   - Click **✔️ Verify** to mark as admin-verified
5. **Approve (Manager Step)**:
   - After admin verification, click **💰 Approve** to mark as manager-approved
   - This adds the `manager-approved` label for payroll processing
6. **Export Data**:
   - Click **📥 Export to CSV** to download verified timesheets
   - Use for payroll, accounting, or record-keeping

### 3. GitHub Pages Setup (Optional for Public Hosting)

To host `index.html` and `admin.html` on GitHub Pages:

1. Go to **Repository Settings → Pages**
2. Set **Source** to "Deploy from a branch"
3. Select **Branch**: `main`, **Folder**: `/` (root)
4. Click **Save**

**Access Control**:
- Since your repository is **Private**, only collaborators added to the repo can access these pages
- Engineers without GitHub accounts can use via shared link (with login required by GitHub)
- For full engineer access without GitHub logins, consider an authentication proxy or use a public form service

## File Structure

```
tstimesheets/
├── index.html          # Engineer submission form
├── admin.html          # Admin/Manager tracking dashboard
├── mock1.html          # (Deprecated - delete after testing)
└── README.md           # This file
```

## Key Features

### ✅ Engineer Submission (`index.html`)

- Clean, mobile-friendly form for daily hour entry
- 5 working days with hours, job type, and description fields
- GitHub API integration: auto-creates issues with structured data
- Secure token handling (sessionStorage, cleared on browser close)
- Success/error notifications

### ✅ Admin Dashboard (`admin.html`)

- Real-time fetch of all timesheet issues from GitHub
- Parse issue body to extract structured data
- Status badges: Pending, Admin Verified, Manager Approved
- Filter by status
- Action buttons:
  - **✔️ Verify**: Adds `admin-verified` label
  - **💰 Approve**: Adds `manager-approved` label
- **🔄 Refresh**: Sync latest from GitHub
- **📥 Export CSV**: Download for payroll
- **⚠️ Clear All**: Close all issues (for reset/cleanup)

### 🔒 Security

- **Tokens never hardcoded**: Stored in browser sessionStorage (memory)
- **Cleared on browser close**: No persistent credentials on disk
- **Repository Privacy**: Only repo collaborators can access GitHub Pages
- **Label-based tracking**: No sensitive data in issue bodies (just hours & descriptions)
- **Audit trail**: All actions logged in GitHub issue history & labels

## Troubleshooting

### "GitHub API error: 401"
- Token is invalid or expired
- Check that you copied the token correctly (no extra spaces)
- Generate a new token if it's been more than 90 days

### "GitHub API error: 403"
- Token doesn't have write permission
- Ensure you set **Issues: Read & Write** when creating the token
- Ensure token is scoped to the `tstimesheets` repository only

### "No timesheet submissions yet"
- Check that the `timesheet` label exists on issues (auto-created on first submission)
- Try **🔄 Refresh** on the admin dashboard
- Verify the token has been saved in browser

### Form doesn't submit
- Save your GitHub token first (yellow section at top)
- Check browser console for error messages (F12)
- Ensure all form fields are filled

### CSV Export shows only filtered data
- This is intentional—export respects your current filter
- Use "All Submissions" filter to export everything

## Future Enhancements

- Multi-level approvals (additional manager tiers)
- Automated email notifications on status changes
- Timesheet templates for recurring projects
- Bulk operations (approve multiple at once)
- Payroll integration (direct export to accounting tools)
- Mobile app (React Native or similar)

## Support & Contributing

For issues, feature requests, or improvements:
1. Create a GitHub Issue on the `tstimesheets` repository
2. Describe the problem or feature
3. Include screenshots if relevant

## License

This system is provided as-is for internal use.

---

**Made with ❤️ for the Trades**
