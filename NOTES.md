# NOTES.md

## 1. Explain One Fix: dates_overlap

The original check was `start_b <= start_a <= end_b` — it only asked whether the new booking's *start date* landed inside an existing one. That catches some cases, but it completely misses the situation where the new booking starts *before* the existing one and still runs into it. For example, trying to book Jan 1–12 when Jan 10–15 is already taken would slip right through.

The fix swaps it out for `start_a <= end_b and start_b <= end_a`. The logic here is simple: two ranges overlap if each one starts before the other ends. It doesn't matter which one comes first on the calendar — this handles every case.

## 2. Show the Failure (Double-Booking)

There's already a confirmed booking for the Canon DSLR Camera from **2026-01-10 to 2026-01-15** in bookings.json. With the original code, you could book that same camera from **2026-01-01 to 2026-01-12** without any error — even though Jan 10–12 is clearly double-booked. The fix catches that overlap and returns a 409 conflict instead.

## 3. AI Use

I used AI to get a first pass on the codebase and help identify where the bugs were. After that I went through each one manually to make sure it actually made sense: traced `rental_days` against the inclusive billing rule written in the code comments, walked through the overlap scenarios for `dates_overlap` by hand, checked that the maintenance filter was applied at all three API endpoints (the equipment list, availability, and the booking POST), and verified the frontend fix by matching the event listener registrations against the actual element IDs in the HTML.
