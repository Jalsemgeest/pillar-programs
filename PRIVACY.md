# Privacy Policy for Pillar

**Last updated: August 3, 2026**

Pillar is a workout tracking app. This policy explains what data the app collects, how it is used, and your rights.

## Data We Collect

**Data you provide**
- Workout logs (exercises, sets, reps, weight, duration, distance)
- Personal profile information (age, weight, height, resting heart rate) used solely to calculate heart rate training zones
- App settings and preferences

**Data collected automatically**
- Heart rate data from Apple Watch or connected sensors, accessed only during active workout sessions and stored locally on your device
- HealthKit workout data (read access only) if you grant permission, used to display workout history within the app

## How We Use Your Data

By default, all data is stored **locally on your device**. If you choose to sign in, your workout history is optionally synced to our cloud so it can be backed up and, in future, shared with a coach.

- Workout logs are used to display your training history and progress
- Profile information is used only for on-device calculations (heart rate zones, personal records)
- HealthKit data is read with your explicit permission and used only within the app

## Cloud Sync (Optional)

If you create an account and sign in, your completed workout sessions (exercises, sets, reps, weight, duration, heart rate stats) are synced to our servers. This data is:

- Stored securely on Supabase infrastructure, hosted on AWS
- Accessible only to you — protected by row-level security policies that prevent any other user or third party from accessing your data
- Never sold, rented, or shared with advertisers or data brokers
- Deletable on request by contacting us at alsemgeester@gmail.com

Your personal profile (age, weight, height) and app settings are never synced — they remain local only.

## Data Sharing

We do not sell, rent, or share your personal data with any third parties.

The only external network requests the app makes are:
- Fetching program updates from [jalsemgeest.github.io/pillar-programs](https://jalsemgeest.github.io/pillar-programs) — downloads workout program JSON files and sends no personal data
- Syncing workout sessions to our Supabase backend, if you are signed in

## Data Storage and Deletion

All local data is stored in your device's local database (SwiftData). Deleting the app removes all locally stored data. You can also export or delete data from within the app.

If you have a cloud account, your synced data can be deleted by contacting us at alsemgeester@gmail.com. We will delete it within 30 days.

## HealthKit

Pillar requests HealthKit permissions only when you choose to use health-related features. We request:
- **Read**: Workouts and heart rate data, to display activity history in the app
- **Write**: Workouts, to save completed sessions to Health

HealthKit data is never used for advertising, shared with data brokers, or uploaded to any server.

## Children's Privacy

Pillar is not directed at children under 13 and does not knowingly collect data from children.

## Changes to This Policy

If this policy changes materially, we will update the "Last updated" date above. Continued use of the app after changes constitutes acceptance.

## Contact

Questions about this policy can be directed to: alsemgeester@gmail.com
