# Release Notes - Version 2.0.1 (Build 69)

## What's New in This Version

### 🏆 Indoor Meets, Rebuilt

- **Meets Now Live in the App**: Indoor meets moved out of the public Google Calendar (which is also embedded on the BPVA website) into their own in-app list, so they no longer show up on the public site
- **Import From Google Calendar**: Admins can copy existing Google Calendar meets into the new system in one tap — existing sign-ups and payment status attach automatically, and it's safe to re-run
- **"I'm Going with BPVA"**: Renamed from "Register" to make clear the app only tells BPVA you're going — actual meet entry still happens on MileSplit or Athletic.net, done by your coach
- **Entry Site Shown Per Meet**: Each meet now shows exactly where your coach enters you (MileSplit or Athletic.net), with a direct link
- **Status at a Glance**: Your "Going with BPVA" / "Going · Paid" status now shows right on the calendar card and in the event details popup — no need to open the Indoor Meets screen to check
- **Jump Straight to a Meet**: Tapping a meet's details now takes you directly to that meet (scrolled to and highlighted), not just the full list
- **For Parents**: See every linked athlete's status for a meet right from the calendar, with payment-pending totals that scale correctly across multiple athletes
- **Admin List Spans a Full Year Back and Ahead**: The admin Indoor Meets list now covers 12 months back through a year ahead, with separate Edit and Delete actions so deleting a meet doesn't require opening the editor first
- **Smarter Import**: Venue names are now split out from the full address on import, and the MileSplit/Athletic.net link is no longer repeated in a meet's notes

### 🔄 Non-App Athlete Account Recovery

- **Convert to App User, Fixed**: Converting a walk-in (non-app) athlete to a full account now correctly carries over their check-ins, waivers, forms, documents, meet sign-ups, and pole checkouts, instead of leaving them orphaned
- **Admin Recovery Tool**: New "Recover Check-ins" screen (Athletes menu) for any records affected by the old conversion flow

### 🐛 Bug Fixes

- Fixed a layout overflow on the admin Indoor Meets screen's meet selector
- Fixed indoor meets sometimes appearing below an unrelated all-day calendar event (e.g. "Gym Closed") on the same day
- Fixed the meet details popup overflowing on longer descriptions
- Fixed a non-functional "launch" icon on meet calendar cards — it now opens the meet's entry link directly

---

This update completes the move of indoor meets into the app and fixes a data-integrity issue affecting converted walk-in athlete accounts, alongside general polish from in-app testing.
