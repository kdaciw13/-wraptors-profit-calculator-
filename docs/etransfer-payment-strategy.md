# E-Transfer Payment Strategy — Wraptors Calgary

## The Problem

E-Transfer cannot be verified programmatically. The business needs a system that:
- Doesn't require awkward "did you pay?" conversations at the front desk
- Doesn't let unpaid bookings block slots from paying customers
- Creates urgency without being pushy
- Handles same-day and next-day bookings differently

---

## The Solution: Smart Hold with 2-Hour Deadline

### Core Rule

**Payment must be received 2 hours before the appointment time. If not received, the slot is automatically released on a first-come, first-served basis.**

### Why 2 Hours?

- Gives the shop time to rebook the released slot
- Gives the customer enough time to see reminders and act
- Avoids the "I was driving to your shop" excuse
- Creates real urgency without feeling unreasonable

---

## Customer Flow

### E-Transfer Booking (appointment is 2+ hours away)

```
Customer books appointment for Wednesday 10:00 AM
  |
  v
INSTANT — Confirmation text/email:
  "Your appointment is reserved! To confirm your spot, send your e-Transfer
   to wraptorscalgary@gmail.com by Wednesday 8:00 AM (2 hours before your
   appointment). Spots are first-come, first-served — if payment isn't
   received by the deadline, your time slot may be taken by another customer."
  |
  v
12 HOURS BEFORE (Tue 10:00 PM) — Reminder:
  "Reminder: Your Wraptors appointment is tomorrow at 10:00 AM.
   Send your e-Transfer by 8:00 AM to keep your spot.
   Amount: $[total] | To: wraptorscalgary@gmail.com"
  |
  v
4 HOURS BEFORE (Wed 6:00 AM) — Final warning:
  "Your appointment is in 4 hours. Send your e-Transfer by 8:00 AM
   or your spot will be released to the next customer in line."
  |
  v
2 HOURS BEFORE (Wed 8:00 AM) — Deadline:
  |
  +-- PAYMENT RECEIVED --> "Payment confirmed! See you at 10:00 AM."
  |
  +-- NO PAYMENT --> "Your appointment has been released because we didn't
                      receive your e-Transfer. Contact us to rebook:
                      587-433-9727 or wraptorscalgary@gmail.com"
```

### Credit Card / Apple Pay Booking (available always)

```
Customer books + pays instantly
  |
  v
INSTANT — "Booking confirmed! Payment received. See you [date] at [time]."
```

### Same-Day Booking (less than 2 hours until appointment)

```
E-Transfer option is HIDDEN — only Credit Card or Apple Pay available.
Reason: No time for e-Transfer verification.
```

---

## The Front Desk Problem — Solved

With this system, the front desk NEVER has to ask about payment:

| Scenario | What Happens |
|----------|-------------|
| Customer shows up, paid via card | Already confirmed. Check them in. |
| Customer shows up, paid via e-Transfer | Pipeline shows "Payment Received." Check them in. |
| Customer shows up, e-Transfer pending | Check GHL pipeline. If still in "Appointment Booked" = not paid. Say: "I see we haven't received your e-Transfer yet. Would you like to pay by card instead?" |
| Customer didn't pay by deadline | They already got an auto-text saying their slot was released. If they show up anyway, offer to rebook if a slot is available. |

**Key insight:** The system does all the payment chasing. The front desk only needs to check the pipeline status — never make an awkward phone call or in-person ask.

---

## GHL Pipeline Setup

### Pipeline: "Wraptors Appointments"

**Stages:**

```
1. Appointment Booked (unpaid)
   - Contact lands here on booking
   - Triggers: instant confirmation text with payment deadline
   - Triggers: 12-hour reminder
   - Triggers: 4-hour final warning

2. Payment Received
   - Contact is MANUALLY dragged here when e-Transfer is verified
   - OR auto-placed here if paid by credit card/Apple Pay
   - Triggers: "Payment confirmed!" text

3. Reminded (24hr before appointment)
   - Auto-move 24 hours before appointment
   - Triggers: "See you tomorrow!" reminder with address/details

4. Checked In
   - Front desk moves contact here on arrival
   - Tracks who showed up

5. In Progress
   - Work has started on the vehicle

6. Complete
   - Job done, vehicle ready for pickup
   - Triggers: "Your vehicle is ready!" text
   - Triggers: Review request (link to Google Reviews)

FALLBACK STAGE:
   Slot Released
   - Auto-move if no payment by deadline
   - Triggers: "Your appointment was released" text
   - Tag contact as "no-payment" for future reference
```

### Automation Rules:

| Trigger | Action |
|---------|--------|
| New contact in "Appointment Booked" | Send booking confirmation + payment deadline text |
| 12 hours before appointment & still in "Appointment Booked" | Send reminder text |
| 4 hours before appointment & still in "Appointment Booked" | Send final warning text |
| 2 hours before appointment & still in "Appointment Booked" | Move to "Slot Released" + send release text |
| Contact moved to "Payment Received" | Send payment confirmation text |
| Contact moved to "Complete" | Send review request after 2 hours |

---

## Urgency Messaging — What the Customer Sees

### On the Calculator (confirmation screen, e-Transfer selected):

> **Your appointment is reserved!**
>
> Send your e-Transfer to **wraptorscalgary@gmail.com** by **[deadline time]** to confirm your spot.
>
> Amount: **$[total]**
>
> Spots are first-come, first-served — if we don't receive your payment by the deadline, your time slot will be released to the next customer.

### Why This Works:

1. **Honest scarcity** — It's true. The slot IS limited. This isn't a fake countdown timer.
2. **Clear deadline** — No ambiguity. They know exactly when they need to pay by.
3. **Consequence stated** — "your time slot will be released" tells them what happens if they don't act.
4. **No guilt** — It's framed as a system policy, not a personal ask.

---

## Edge Cases

| Scenario | How to Handle |
|----------|--------------|
| Customer pays 5 min after deadline | If slot hasn't been rebooked, manually restore it. Be gracious — "We got your payment just in time!" |
| Customer wants to change appointment time | Cancel + rebook. New payment deadline applies. |
| Customer pays wrong amount | Text them: "We received $X but your total is $Y. Please send the remaining $Z." Hold the slot until resolved. |
| Customer sends e-Transfer to wrong email | They'll need to contact their bank. Offer to hold the slot if they can prove they sent it. |
| Repeat customer / trusted relationship | Shop can override the deadline at their discretion. GHL allows manual stage moves. |

---

## Summary

- **E-Transfer:** Book first, pay by 2 hours before appointment
- **Credit Card / Apple Pay:** Instant confirmation, no hold needed
- **Same-day within 2 hours:** Card only, no e-Transfer option
- **No double-booking:** Slot held until deadline, then released
- **All reminders automated via GHL:** Zero manual payment chasing
- **Front desk never asks about payment:** System handles everything
