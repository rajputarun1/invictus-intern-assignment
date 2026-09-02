# Bugs found

Add one section per issue. Bug 1 is filled in to show the format — fix it, then write what you changed. Copy the blank template for the rest.

Keep this file in the repo and **commit it** with your fixes.

---

## Bug 1

**How to reproduce:** Open the app. The expense list says “Newest first”. The first row is Wine (7 Mar). Board game (15 Mar) is further down.

**What is wrong:** The list is showing oldest expenses first. Newest should be at the top.

**What I changed:** Swapped the order of comparison inside the `sort` function in `ExpenseList.jsx`. Changing `dateValue(a.date) - dateValue(b.date)` to `dateValue(b.date) - dateValue(a.date)` correctly inverts the list to show the latest entries first.

---

## Bug 2

**How to reproduce:** Use the filters to view a specific category (like "Food"), then try to edit an amount or delete an expense from the filtered view.

**What is wrong:** The component uses the array's index from the filtered list when triggering update or delete actions. Because the reducer expects the index of the full array, it ends up altering or deleting the completely wrong expense in the global state.

**What I changed:** Refactored the data flow to use unique item IDs instead of array positions. I updated `ExpenseList.jsx` to pass `expense.id` through the `onDelete` and `onSaveAmount` callbacks, adjusted `App.jsx` to forward that ID, and modified the reducer cases in `store.js` to look up and modify expenses by matching their specific ID.

---

## Bug 3

**How to reproduce:** Create a new expense where one person pays for the bill but unchecks themselves from the "Split between" section so they aren't responsible for a share.

**What is wrong:** In `balances.js`, there is an incorrect fallback conditional that checks if the payer is missing from the split. If they are, it mistakenly subtracts money from what they are owed. This effectively charges them for an expense they didn't share in, breaking the balance calculation.

**What I changed:** Removed the problematic `if` block (`if (!(exp.paidBy in shares)...`) from the `computeBalances` function in `balances.js`. The calculation now relies solely on adding the total amount to the payer's credit and deducting exact shares from whoever was selected in the split, which naturally covers this scenario.

---

## Bug 4

**How to reproduce:** Fill out the form to add a brand-new expense, save it, and then hit refresh on your browser window.

**What is wrong:** When the app reloads and pulls the saved state from `localStorage`, `JSON.parse` brings the dates back as plain text strings. While the original seed data dates get manually converted back to real JavaScript `Date` objects during hydration, new entries stay as strings, which breaks the sorting function.

**What I changed:** Updated the `loadState` function inside `store.js` to map over the expenses array after parsing it from local storage, explicitly casting every `date` value back into a native `Date` object so sorting functions work properly across sessions.

---
