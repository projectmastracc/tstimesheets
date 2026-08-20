# Testing Checklist

## Pre-Test Setup

1. ✅ Create a Fine-Grained Personal Access Token on GitHub
   - Go to: https://github.com/settings/tokens?type=beta
   - Name: `tstimesheets-test`
   - Repository: `projectmastracc/tstimesheets` (Only this repo)
   - Permissions: Issues: Read & Write
   - Copy the token

## Test 1: Engineer Submission Form (`index.html`)

### Setup
- [ ] Open `index.html` in a browser
- [ ] You should see: "👷 Trades Timesheet Submission" header
- [ ] Yellow "GitHub Token Required" section at top

### Execute
- [ ] Paste your GitHub PAT in the token field
- [ ] Click **💾 Save Token**
- [ ] Should see: ✅ GitHub token saved to session storage (success alert)

### Form Submission
- [ ] Select engineer name from dropdown (e.g., "John Doe")
- [ ] Pick a recent week commencing date (Monday)
- [ ] Fill in all 5 days:
  - Each day needs: Hours, Job type, Description
  - Try different job types and hours values
- [ ] Click **Submit Timesheet**
- [ ] Should see: ✅ Timesheet submitted successfully! Issue created in GitHub. (success alert)
- [ ] Check form is reset (cleared)

### Verify on GitHub
- [ ] Go to your `tstimesheets` repository on GitHub
- [ ] Go to **Issues** tab
- [ ] Should see a NEW issue with title: `📋 Timesheet: [Name] - Week of [Date]`
- [ ] Issue should have labels: `timesheet`, `pending-review`
- [ ] Issue body should contain:
  - Engineer name
  - Week commencing date
  - Breakdown of each day with hours, job, and description

## Test 2: Admin Dashboard (`admin.html`)

### Setup
- [ ] Open `admin.html` in a browser
- [ ] You should see: "💼 Admin Dashboard & Verification Grid" header
- [ ] Yellow "GitHub Token Required" section at top

### Load Data
- [ ] Paste the same GitHub PAT
- [ ] Click **💾 Save Token**
- [ ] Should see: ✅ GitHub token loaded from session storage (success alert)
- [ ] Dashboard should automatically fetch data

### View Submissions
- [ ] Should see table with columns:
  - Issue #
  - Engineer
  - Week of
  - Daily Breakdown
  - Admin Verification
  - Manager Approval
  - Actions
- [ ] Should show the issue you just created
- [ ] Daily breakdown should display Mon-Fri with hours and job types
- [ ] Admin status should show: ⧖ Awaiting Review (red badge)
- [ ] Manager status should show: ⧖ Pending (red badge)

### Test Verification (Admin Step)
- [ ] Click **✔️ Verify** button on a row
- [ ] Dashboard should refresh
- [ ] Issue should now show: ✓ Admin Verified (blue badge)
- [ ] Go back to GitHub Issues tab
- [ ] That issue should now have `admin-verified` label added

### Test Approval (Manager Step)
- [ ] Click **💰 Approve** button on the verified row
- [ ] Dashboard should refresh
- [ ] Issue should now show: ✓ BACS Approved (green badge)
- [ ] Go back to GitHub Issues tab
- [ ] That issue should now have `manager-approved` label added

### Test Filters
- [ ] Dropdown filter "Filter by Status" should have options:
  - All Submissions
  - Pending Review
  - Admin Verified
  - Manager Approved
- [ ] Select "Pending Review" - no items should show
- [ ] Select "Admin Verified" - should show the item you verified
- [ ] Select "Manager Approved" - should show the item you approved
- [ ] Select "All Submissions" - should show all items

### Test CSV Export
- [ ] Make sure at least one submission exists
- [ ] Click **📥 Export to CSV**
- [ ] A file should download with name: `timesheets_export_[DATE].csv`
- [ ] Open the CSV and verify:
  - Issue number
  - Engineer name
  - Week date
  - All daily hours
  - Status columns show: Verified/Pending and Approved/Pending

### Test Refresh
- [ ] Click **🔄 Refresh Data**
- [ ] Dashboard should reload data from GitHub

## Test 3: Token Security

### Browser Session Storage
- [ ] Token should be stored in **sessionStorage**, not localStorage
- [ ] Open browser DevTools (F12 → Application → Session Storage)
- [ ] Should see: `github_token` with your PAT value
- [ ] Close the browser completely
- [ ] Reopen `index.html` or `admin.html`
- [ ] Token should be gone (you'll need to paste it again)
- [ ] This is **by design** - token cleared on browser close for security

## Test 4: Error Handling

### Invalid Token
- [ ] On `index.html`, paste an invalid token
- [ ] Try to submit a form
- [ ] Should see: ❌ Error: GitHub API error: 401 Unauthorized (or similar)

### No Token
- [ ] Clear sessionStorage (DevTools → Application → Session Storage → Clear All)
- [ ] Try to refresh dashboard on `admin.html`
- [ ] Should see: ❌ Error: GitHub token not configured. Please save your token first.

### Network Error (Simulate)
- [ ] Go offline (disconnect internet)
- [ ] Click **🔄 Refresh** on admin dashboard
- [ ] Should show error alert
- [ ] Go back online
- [ ] Refresh should work again

## Test 5: Multiple Submissions

### Create 3 Different Submissions
- [ ] Submit as "John Doe" for Week 1
- [ ] Submit as "Jane Smith" for Week 1
- [ ] Submit as "Dave Wright" for Week 2
- [ ] Admin dashboard should show all 3
- [ ] Verify one, approve one, leave one pending
- [ ] Filters should work correctly for each status

### CSV Export with Mix of Statuses
- [ ] Export CSV
- [ ] Should include all 3 with different status values

## Test 6: GitHub Pages Access (Optional)

*Only if you've set up GitHub Pages in repo settings*

- [ ] Visit the GitHub Pages URL for your repo
- [ ] Both `index.html` and `admin.html` should be accessible
- [ ] Since repo is Private, non-collaborators should see 404

## Cleanup

After successful testing:

1. [ ] You can delete `mock1.html` from the repo
2. [ ] Keep `index.html`, `admin.html`, and `README.md`
3. [ ] Commit and push: `git add -A && git commit -m "feat: GitHub-backed timesheet system"`

## Known Limitations & Notes

- ⚠️ Token stored in browser memory only (sessionStorage)
- ⚠️ Private repo access requires GitHub login
- ⚠️ Public access without GitHub account requires external auth proxy
- ✅ All data persists in GitHub (audit trail available)
- ✅ Labels provide easy filtering and tracking
- ✅ CSV export works with filtered view
- ✅ Works offline after initial GitHub API call (cached data)

## Support

If tests fail:
1. Check browser console (F12) for error messages
2. Verify token is still valid on GitHub
3. Ensure token has "Issues: Read & Write" permissions
4. Verify repository is set to Private (if using GitHub Pages)
5. Check GitHub API rate limits: https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting

---

Good luck with your testing! 🚀
