# Testing Checklist

## Pre-Test Setup

1. ✅ Create two Fine-Grained Personal Access Tokens on GitHub:

   **Token A: Submission Token (Limited - Write Only)**
   - Go to: https://github.com/settings/tokens?type=beta
   - Name: `tstimesheets-submission`
   - Repository: `tstimesheets` (Only this repo)
   - Permissions: **Issues: Write only**
   - Copy the token
   - Paste into [index.html](index.html) line 14: `const SUBMISSION_TOKEN = 'github_pat_YOUR_TOKEN';`
   - **Commit & push this change** (it's safe because token is write-only)
   
   **Token B: Admin Token (Full Read/Write)**
   - Name: `tstimesheets-admin`
   - Repository: `tstimesheets` (Only this repo)
   - Permissions: **Issues: Read & Write**
   - Copy the token
   - **DO NOT commit to repo** - keep it private for admin use only

## Test 1: Engineer Submission Form (`index.html`) - NO TOKEN NEEDED ✨

### Setup
- [ ] Open `index.html` in a browser
- [ ] You should see: "👷 Trades Timesheet Submission" header
- [ ] **NO token input field** anywhere (key difference - engineer-friendly!)
- [ ] Just see: Name dropdown + date picker + daily form fields

### Form Submission (Zero Tech Knowledge Required!)
- [ ] Select engineer name from dropdown (e.g., "John Doe")
- [ ] Pick a recent week commencing date (Monday)
- [ ] Fill in all 5 days:
  - Each day needs: Hours, Job type, Description
  - Try different job types and hours values
- [ ] Click **Submit Timesheet**
- [ ] Should see: ✅ Timesheet submitted successfully! (success alert)
- [ ] Check form is reset (cleared)
- [ ] **No token was needed at all** ← This is the win!

### Verify on GitHub
- [ ] Go to your `tstimesheets` repository on GitHub
- [ ] Go to **Issues** tab
- [ ] Should see a NEW issue with title: `📋 Timesheet: [Name] - Week of [Date]`
- [ ] Issue should have labels: `timesheet`, `pending-review`
- [ ] Issue body should contain:
  - Engineer name
  - Week commencing date
  - Submission timestamp
  - Breakdown of each day with hours, job, and description

## Test 2: Admin Dashboard (`admin.html`) - ADMIN TOKEN REQUIRED 🔐

### Setup
- [ ] Open `admin.html` in a browser
- [ ] You should see: "🔐 Admin Dashboard & Verification Grid" header (lock icon!)
- [ ] Subtitle says: "Admin access only"
- [ ] Yellow "Admin Token Required" section at top
- [ ] Token input field

### Unlock with Admin Token
- [ ] Paste your **Admin Token** (the full read/write token, NOT the submission token)
- [ ] Click **🔐 Unlock Admin Panel**
- [ ] Should see: ✅ GitHub token loaded from session storage (success alert)
- [ ] Dashboard should automatically fetch and display data

### View Submissions
- [ ] Should see table with columns:
  - Issue #
  - Engineer
  - Week of
  - Daily Breakdown
  - Admin Verification
  - Manager Approval
  - Actions
- [ ] Should show the issue you just created from Test 1
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
- [ ] Select "Pending Review" - no items should show (you approved it)
- [ ] Select "Admin Verified" - should show the item you verified (but not approved)
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
  - Status columns show correct values

### Test Refresh
- [ ] Click **🔄 Refresh Data**
- [ ] Dashboard should reload latest data from GitHub

## Test 3: Token Types & Security Verification

### Using Wrong Token in Admin Dashboard
- [ ] Try pasting the **Submission Token** into admin.html
- [ ] Click **🔐 Unlock Admin Panel**
- [ ] Should see: ❌ Error: GitHub API error: 403 Forbidden
- [ ] This is **correct behavior** - submission token doesn't have read permissions
- [ ] Now paste the correct **Admin Token** and it works
- [ ] This demonstrates the token separation security model ✅

### Engineer Form Works WITHOUT Any Token
- [ ] Open `index.html` fresh
- [ ] Form works perfectly with NO token input
- [ ] Submit successfully
- [ ] This shows engineers need zero tech knowledge ✅

## Test 4: Multiple Submissions & Workflow

### Create 3 Different Submissions (All Without Token!)
- [ ] Submit as "John Doe" for Week 1
- [ ] Submit as "Jane Smith" for Week 1  
- [ ] Submit as "Dave Wright" for Week 2
- [ ] Admin dashboard (with admin token) should show all 3
- [ ] Verify one, approve one, leave one pending
- [ ] Filters should correctly show each status

### CSV Export with Mix of Statuses
- [ ] Export CSV (respects current filter)
- [ ] Should include all 3 with different status values

## Test 5: Error Handling & Edge Cases

### Invalid Admin Token
- [ ] On `admin.html`, paste a garbage token like `xyz123`
- [ ] Click **🔐 Unlock Admin Panel**
- [ ] Should see: ❌ Error: GitHub API error: 401 Unauthorized

### No Admin Token
- [ ] Clear sessionStorage (DevTools → Application → Session Storage → Clear All)
- [ ] Try to click **🔄 Refresh** on admin dashboard
- [ ] Should see: ❌ Error: GitHub token not configured. Please save your token first.

### Submission Token Expires (Maintenance Scenario)
- [ ] Generate submission token with 1-day expiration (for testing)
- [ ] Add it to index.html and push
- [ ] Engineer submits successfully
- [ ] Simulate token expiring (wait or regenerate)
- [ ] Engineer tries to submit again
- [ ] Should see: ❌ GitHub API error: 401
- [ ] Admin regenerates new submission token
- [ ] Admin updates index.html line 14 with new token
- [ ] Commits & pushes
- [ ] Engineer can submit again ✅
- [ ] (This is normal maintenance - set 90-day expiration to minimize updates)

## Test 6: GitHub Issue Data Integrity

### Check Issue Body Format
- [ ] Open a submitted issue on GitHub
- [ ] Verify issue body contains:
  - Engineer name
  - Week commencing date
  - Submission timestamp
  - Daily breakdown (Mon-Fri)
  - Each day has: Hours, Job, Description
- [ ] Verify admin.html parses this correctly in dashboard

### Check Labels
- [ ] New issue: `timesheet` + `pending-review`
- [ ] After admin clicks Verify: also has `admin-verified`
- [ ] After manager clicks Approve: also has `manager-approved`
- [ ] All labels persist and appear in filters

### Check Issue Timeline
- [ ] Open GitHub issue history
- [ ] Should see:
  - Original submission (by bot with submission token)
  - Label additions (by admin with admin token)
  - Any comment updates

## Test 7: Full End-to-End Workflow

### Complete Scenario
1. [ ] Engineer John submits Monday afternoon (no token needed)
2. [ ] Issue created with labels: `timesheet`, `pending-review`
3. [ ] Admin logs into admin.html with their personal token Wednesday
4. [ ] Sees John's submission in "Pending Review" filter
5. [ ] Reviews the work hours and descriptions
6. [ ] Clicks **✔️ Verify**
7. [ ] Issue now labeled `admin-verified`, shows blue badge
8. [ ] Manager logs in Friday morning
9. [ ] Sees verified submissions in "Admin Verified" filter
10. [ ] Clicks **💰 Approve** on John's entry
11. [ ] Issue now labeled `manager-approved`, shows green badge
12. [ ] Manager clicks **📥 Export to CSV**
13. [ ] CSV includes John's entry with status "Verified" and "Approved"
14. [ ] Manager imports into payroll system
15. [ ] ✅ Complete workflow verified!

## Cleanup After Testing

1. [ ] Delete test issues from GitHub if desired (or keep for records)
2. [ ] Both tokens should remain active
3. [ ] Commit final changes with new index.html (with submission token)
4. [ ] Push to main

## Known Limitations & Design Decisions

### ✅ Submission Token is Intentionally Public (in HTML)
- It's hardcoded in the HTML (can't be hidden)
- It has **minimal permissions**: Write-only for issues in this repo
- It **cannot**:
  - Read issues
  - Delete issues
  - Access other repos
  - Access any other account data
- **Safe by design**: Limited blast radius

### ✅ Admin Token is Private (sessionStorage only)
- Only admin/manager knows this token
- Stored in browser memory only
- Cleared when browser closes
- Has full read/write for this repo only

### ✅ Zero Token Burden on Engineers
- Engineers don't need GitHub account
- Engineers don't need to manage credentials
- Engineers just fill out form and click submit
- **Much better UX** for trade engineers

### ⚠️ Admin Maintains Submission Token
- Admin is responsible for regenerating submission token if it expires
- Admin updates index.html with new token
- Admin commits & pushes change
- This happens once per 90 days (recommended expiration)
- Small maintenance overhead, huge UX win for engineers

## Success Criteria - All Should Be True ✅

1. ✅ Engineers can submit with **zero tokens**, just name + form
2. ✅ Admins/Managers can access dashboard with their **personal token only**
3. ✅ Admins can verify (add `admin-verified` label)
4. ✅ Managers can approve (add `manager-approved` label)
5. ✅ CSV export works correctly with all data
6. ✅ Filters work for all statuses (Pending, Verified, Approved)
7. ✅ Invalid/missing admin token shows clear errors
8. ✅ Submission token is read-only and safe (can't break anything)
9. ✅ GitHub Issues reflect all actions (labels, statuses, timestamps)
10. ✅ System is **zero-cost** (only GitHub, no external services)
11. ✅ **Trade engineers can use without tech knowledge**

---

🎉 **Testing complete = Trade engineers submitting timesheets with zero friction!**

Good luck! 🚀
