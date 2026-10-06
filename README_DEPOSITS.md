# Auto-Deposit System for Jody Ray Dulus

**Account Holder:** Jody Ray Dulus  
**Username:** jdwjqifjwqo  
**Repository:** city.python.tunnels (looper branch)

## Overview

This system automatically:
1. **Tracks** all contributions (Stripe, GitHub, cash, conversions) in a unified ledger
2. **Approves** deposits when balance ≥ $1.00
3. **Deposits** to your Stripe-connected bank account
4. **Logs** all transactions for audit

## Files

- `data/contributions.yml` — Master ledger with running balance
- `config/deposit.yml` — Auto-deposit configuration
- `backend/sync_contributions.py` — Aggregates all contribution sources
- `backend/auto_deposit.py` — Creates Stripe payouts
- `.github/workflows/sync-and-deposit.yml` — Automated workflow

## How It Works

### Daily Schedule
1. **00:00 UTC** → Syncs all contributions
2. Checks if `total_balance ≥ $1.00`
3. If yes → Auto-approves and deposits to bank
4. Updates ledger and logs

### Manual Trigger
```bash
gh workflow run sync-and-deposit.yml -r looper
```

## Setup: GitHub Secrets

Add these to your repository settings:

```
STRIPE_API_KEY       = sk_live_... (your Stripe API key)
STRIPE_ACCOUNT_ID    = acct_...    (your Stripe account ID)
```

**How to find them:**
1. Go to Stripe Dashboard → API Keys
2. Copy your Live Secret Key → `STRIPE_API_KEY`
3. Go to Stripe Dashboard → Account Settings → Account ID
4. Copy Account ID → `STRIPE_ACCOUNT_ID`

## View Balance

Check the current balance anytime:
```bash
cat data/contributions.yml | grep total_balance
```

## View Deposits

All deposits are logged:
```bash
cat data/contributions.yml | grep -A 10 "deposits:"
cat logs/deposits.log
```

## Configuration

Edit `config/deposit.yml` to change:
- `minimum_balance` — Minimum before auto-deposit (default: $1.00)
- `frequency` — How often to check (default: daily)
- `approval_threshold` — Automatic threshold (default: $0.01)

## Testing

To test the deposit system without real money:

1. **Stripe Test Mode** (if using Stripe test keys)
2. **Manual sync** first:
   ```bash
   python backend/sync_contributions.py
   ```
3. **Check the balance:**
   ```bash
   cat data/contributions.yml
   ```
4. **Run deposit script manually:**
   ```bash
   export STRIPE_API_KEY="your_key"
   export STRIPE_ACCOUNT_ID="your_account_id"
   python backend/auto_deposit.py
   ```

## Support

- **Stripe Docs:** https://stripe.com/docs/payouts
- **GitHub Actions:** https://docs.github.com/en/actions
- **YAML Format:** https://yaml.org/spec/1.2/spec.html

---

**Status:** ✓ Ready for production  
**Last Updated:** 2026-10-06  
**Owner:** jdwjqifjwqo (Jody Ray Dulus)
