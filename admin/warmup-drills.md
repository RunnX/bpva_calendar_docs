---
layout: default
title: Warmup Drills
description: Create warmup routines with video demonstrations and action levels
---

# Warmup Drills & Action Level Media

## Overview

The Warmup Drills system provides structured warmup sequences with progressive action levels and multimedia demonstrations. Athletes can follow along with video-guided warmups to properly prepare for pole vault training and competition.

## Action Levels

### Progressive Warmup System

The BPVA warmup system uses 4 progressive action levels, each building on the previous:

**Level 1: Light Activation (5-7 minutes)**
- Gentle movements to increase heart rate
- Joint mobility exercises
- Light cardio (jogging, jumping jacks)
- Mental preparation and focus

**Level 2: Dynamic Mobility (5-7 minutes)**
- Dynamic stretching sequences
- Sport-specific mobility patterns
- Balance and proprioception work
- Muscle activation exercises

**Level 3: Sport-Specific Movements (5-7 minutes)**
- Pole vault technical drills at low intensity
- Approach run work
- Plant and takeoff drills
- Swing and inversion movements

**Level 4: High-Intensity Prep (3-5 minutes)**
- Near-competition intensity drills
- Short approach jumps
- Explosive movements
- Mental visualization and competition prep

## Action Level Media

### Video Demonstrations

Each action level includes comprehensive video content:

**Overview Videos**
- Shows complete sequence for the level
- Real-time pacing (follow-along style)
- Multiple camera angles
- Audio coaching cues
- On-screen timers and rep counters

**Individual Drill Videos**
- Detailed breakdown of each drill
- Slow-motion technique demonstrations
- Common mistakes and corrections
- Variations for different skill levels
- Coaching points overlay

**Video Best Practices**
- Duration: 30-90 seconds per drill
- Resolution: 1080p minimum
- Clear audio commentary
- Professional lighting
- Show full athlete in frame
- Include side and front views
- Add text overlays for key points

### Images & Photo References

**Form Photos**
- Key positions in each drill
- Before/after comparisons
- Common mistakes vs correct form
- Multiple angles (front, side, back)

**Sequence Images**
- Step-by-step progression
- Numbered sequence showing movement flow
- Arrow indicators for direction
- Annotated coaching points

**Cue Cards**
- Visual reminders of coaching cues
- Quick reference during warmup
- Printable for gym walls
- Color-coded by action level

### Audio Content

**Coaching Voice-Overs**
- Instructional guidance during drill
- Motivation and encouragement
- Counting reps and timing
- Breathing cues
- Safety reminders

**Background Music**
- Energizing music for warmup
- Tempo matches drill intensity
- Royalty-free or licensed
- Volume balanced with voice-over

## Creating Warmup Sequences

### Warmup Sequence Builder

1. **Access Warmup Management**
   - Navigate to Admin Dashboard
   - Tap "Warmup Drills"
   - View existing sequences or create new

2. **Sequence Information**
   - Name: "Pre-Practice Warmup", "Competition Day Warmup"
   - Target audience: All, Beginner, Intermediate, Advanced
   - Total duration: 20-30 minutes typical
   - When to use: Practice, meets, conditioning sessions

3. **Build Action Level 1**
   - Add 4-6 drills for light activation
   - Set duration for each (30 sec - 2 min)
   - Upload demonstration videos
   - Add coaching cues
   - Define transitions between drills

4. **Build Action Level 2**
   - Add 5-7 dynamic mobility drills
   - Increase intensity from Level 1
   - Focus on sport-specific movements
   - Include balance and proprioception work
   - Upload videos and images

5. **Build Action Level 3**
   - Add pole vault technical drills
   - Lower intensity versions of vault movements
   - Approach run progressions
   - Plant and swing drills
   - Multimedia demonstrations critical here

6. **Build Action Level 4**
   - High-intensity prep drills
   - Short approach jumps
   - Explosive movements
   - Competition simulation
   - Mental preparation cues

7. **Review & Publish**
   - Test flow through all 4 levels
   - Ensure videos load properly
   - Check total duration (target 20-30 min)
   - Publish to athletes

### Adding Media to Action Levels

**Upload Videos**
1. Tap "Add Video" for action level or specific drill
2. Choose video from device or record new
3. Trim video if needed
4. Add title and description
5. Enable audio or coaching voice-over
6. Save and preview

**Upload Images**
1. Tap "Add Image" for drill
2. Select image from device or take photo
3. Crop and adjust
4. Add annotations or text overlays (optional)
5. Save and position in sequence

**Add Audio**
1. Tap "Add Audio" for drill
2. Record coaching voice-over OR upload audio file
3. Sync with video if applicable
4. Adjust volume levels
5. Save and test playback

## Athlete Experience

### Following Warmup Sequences

**Pre-Practice**
1. Athlete opens app 20-30 min before practice
2. Selects assigned warmup sequence
3. Starts at Action Level 1
4. Follows along with video demonstrations
5. Progresses through all 4 levels
6. Completes warmup before practice starts

**During Warmup**
- Videos play in sequence automatically
- Optional manual navigation between drills
- Pause/resume at any point
- Repeat specific drills if needed
- Progress indicator shows completion

**Warmup Tracking**
- System logs when warmup started
- Records completion time
- Tracks which levels completed
- Saves progress if interrupted
- Shows warmup history

### Athlete Features

**Favorites**
- Athletes can favorite warmup sequences
- Quick access to preferred warmups
- Create custom warmup from favorite drills
- Share favorites with teammates (future)

**Adjustments**
- Skip drills if injured (with coach approval)
- Modify duration if time-limited
- Repeat levels that feel particularly needed
- Add notes about how warmup felt

**Progress Tracking**
- Warmup completion stats
- Correlation with performance
- Identify which warmups are most effective
- Build consistency habits

## Integration with Practice Schedules

### Assigning Warmups to Practices

**Calendar Event Assignment**
1. Create or edit calendar event for practice
2. In event details, select "Assign Warmup"
3. Choose warmup sequence from library
4. Set when athletes should start warmup
5. Save event

**Athlete Notifications**
- Athletes receive notification when warmup assigned
- Push notification reminder 30 min before practice
- In-app notification shows warmup details
- Athletes can preview warmup sequence

**Coach Dashboard**
- See which athletes completed assigned warmup
- View completion times
- Identify athletes who skipped warmup
- Follow up with athletes as needed

## Data Model

### WarmupSequence
```dart
{
  "id": "warmup_uuid",
  "name": "Pre-Practice Warmup - Full Sequence",
  "description": "Complete 4-level warmup for practice days",
  "targetAudience": "all",
  "totalDuration": "25 minutes",
  "actionLevels": [
    {
      "level": 1,
      "name": "Light Activation",
      "duration": "6 minutes",
      "drills": [...],
      "overviewVideoUrl": "https://...",
      "notes": "Focus on controlled, gentle movements"
    },
    {
      "level": 2,
      "name": "Dynamic Mobility",
      "duration": "7 minutes",
      "drills": [...],
      "overviewVideoUrl": "https://...",
      "notes": "Increase range of motion gradually"
    },
    {
      "level": 3,
      "name": "Sport-Specific",
      "duration": "7 minutes",
      "drills": [...],
      "overviewVideoUrl": "https://...",
      "notes": "Low-intensity vault movements"
    },
    {
      "level": 4,
      "name": "High-Intensity Prep",
      "duration": "5 minutes",
      "drills": [...],
      "overviewVideoUrl": "https://...",
      "notes": "Prepare for competition-level intensity"
    }
  ],
  "createdBy": "coach_uid",
  "createdAt": "2024-01-15T10:00:00Z",
  "updatedAt": "2024-01-20T14:30:00Z"
}
```

### ActionLevelDrill
```dart
{
  "name": "Leg Swings - Forward/Back",
  "description": "Stand beside pole or wall, swing leg forward and back...",
  "duration": "60 seconds",
  "reps": "15 each leg",
  "videoUrl": "https://storage.googleapis.com/warmups/leg-swings-fb.mp4",
  "videoThumbnailUrl": "https://storage.googleapis.com/warmups/leg-swings-fb-thumb.jpg",
  "imageUrls": [
    "https://storage.googleapis.com/warmups/leg-swings-start.jpg",
    "https://storage.googleapis.com/warmups/leg-swings-forward.jpg",
    "https://storage.googleapis.com/warmups/leg-swings-back.jpg"
  ],
  "audioUrl": "https://storage.googleapis.com/warmups/leg-swings-coaching.mp3",
  "coachingCues": [
    "Keep standing leg slightly bent",
    "Controlled swing - no ballistic movements",
    "Gradually increase range of motion"
  ],
  "commonMistakes": [
    "Swinging too aggressively too soon",
    "Locked standing leg",
    "Leaning torso too far back"
  ],
  "order": 3
}
```

### AthleteWarmupLog
```dart
{
  "id": "log_uuid",
  "athleteId": "athlete_uid",
  "warmupSequenceId": "warmup_uuid",
  "practiceEventId": "event_uuid",
  "startedAt": "2024-02-10T15:30:00Z",
  "completedAt": "2024-02-10T15:55:00Z",
  "levelsCompleted": [1, 2, 3, 4],
  "drillsSkipped": [],
  "notes": "Felt great, good energy",
  "rating": 5
}
```

## Best Practices

### Creating Effective Warmups

**Progression**
- Always start with low intensity (Level 1)
- Build gradually through levels
- Never skip levels
- Allow adequate time for each level

**Duration**
- Minimum 20 minutes for practice
- 30 minutes for competition
- Longer in cold weather
- Adjust for athlete age and experience

**Variety**
- Rotate warmup sequences to prevent boredom
- Seasonal variations (indoor vs outdoor)
- Competition-specific warmups
- Recovery day modified warmups

**Safety**
- Never rush warmup to save time
- Watch for athlete fatigue during warmup (red flag)
- Modify for injured athletes
- Proper environment (space, temperature)

### Video Production

**Recording Tips**
- Use tripod for stable footage
- Good natural or artificial lighting
- Quiet location for clear audio
- Multiple takes to get best demonstration
- Include different angles in separate clips

**Editing**
- Keep drill videos concise (30-90 sec)
- Add text overlays for clarity
- Include slow-motion for complex movements
- Add timer or rep counter overlay
- Background music at low volume

**Technical Specs**
- Resolution: 1080p minimum (4K better)
- Format: MP4 (H.264 codec)
- Frame rate: 30 or 60 fps
- File size: Under 100MB per video
- Aspect ratio: 16:9 (landscape)

### Athlete Adoption

**Buy-In**
- Explain why each level matters
- Show performance correlation data
- Coach participation (lead warmup)
- Make it fun and engaging
- Recognize consistent completion

**Accountability**
- Track warmup completion
- Include in practice expectations
- Positive reinforcement for consistency
- Address athletes who skip warmup
- Make warmup non-negotiable before vaulting

## Troubleshooting

**Videos Buffer or Won't Load**
- Check internet connection
- Reduce video quality setting
- Download warmup for offline use
- Clear app cache
- Ensure adequate storage on device

**Athletes Skipping Warmup**
- Make warmup mandatory
- Track completion in dashboard
- Shorten warmup if time is issue
- Make warmup more engaging
- Coach conversation with athlete

**Warmup Takes Too Long**
- Review duration of each drill
- Eliminate redundant drills
- Tighten transitions between levels
- Create "Express Warmup" version (15 min)

## Future Enhancements

Potential features under consideration:
- AI form analysis from athlete video uploads
- Athlete-created custom warmups
- Social warmup challenges
- Integration with wearable devices (heart rate zones)
- Weather-based warmup recommendations
- Injury-specific modified warmups
- Warmup effectiveness scoring
- Community sharing of warmups
- Live group warmup sessions
- Voice-activated coaching during warmup
