---
layout: default
title: Exercise Library
description: Maintain a library of pole vault exercises and drills
---

# Exercise Library & Warmup Drills

## Overview

The BPVA Calendar app includes comprehensive tools for managing pole vault exercise libraries and warmup drill sequences. These features help coaches organize training programs and ensure athletes have access to proper technique demonstrations.

## Exercise Library

### Purpose

The Exercise Library allows coaches to maintain a centralized database of pole vault-specific exercises that can be reused across multiple workout templates. This ensures consistency in training and makes workout creation more efficient.

### Exercise Categories

**Strength Training**
- Lower body (squats, lunges, box jumps)
- Upper body (pull-ups, dips, bench press)
- Core (planks, ab rollouts, Russian twists)
- Olympic lifts (cleans, snatches, jerks)

**Technical Drills**
- Approach run drills
- Plant and takeoff
- Swing and turn
- Inversion exercises
- Extension and bar clearance

**Conditioning**
- Sprint intervals
- Endurance runs
- Plyometrics
- Agility drills

**Flexibility & Mobility**
- Dynamic stretching
- Static stretching
- Foam rolling
- Yoga poses

### Exercise Attributes

Each exercise in the library includes:
- **Name**: Exercise identifier (e.g., "Box Jumps")
- **Category**: Type of exercise (strength, technical, conditioning, flexibility)
- **Description**: Detailed instructions on how to perform
- **Equipment**: Required equipment (pole, runway, box, barbell, etc.)
- **Difficulty**: Beginner, Intermediate, Advanced
- **Video URL**: Optional link to demonstration video
- **Image URL**: Optional reference image
- **Coaching Cues**: Key points to emphasize
- **Common Mistakes**: What to watch for and correct
- **Progressions**: How to make easier or harder

### Creating Exercises

1. **Access Exercise Library**
   - Navigate to Admin Dashboard
   - Tap "Exercise Library"
   - View existing exercises

2. **Add New Exercise**
   - Tap "Add Exercise" button
   - Enter exercise name
   - Select category
   - Choose difficulty level
   - Add detailed description

3. **Add Media**
   - Upload demonstration video (optional)
   - Upload reference images (optional)
   - Add external video links (YouTube, Vimeo)

4. **Add Coaching Points**
   - List 3-5 key coaching cues
   - Note common mistakes
   - Describe progressions/regressions

5. **Save Exercise**
   - Review all information
   - Tap "Save"
   - Exercise now available in workout builder

### Using Exercises in Workouts

When creating workout templates:
1. Select exercises from library
2. Define sets, reps, and rest periods
3. Add pole vault specific parameters (pole length, step count, grip height)
4. Assign to athletes

### Exercise Management

**Edit Exercises**
- Update descriptions as technique evolves
- Add new videos or images
- Refine coaching cues
- Adjust difficulty levels

**Delete Exercises**
- Remove outdated exercises
- Cannot delete if used in active workouts
- Archive instead of delete to preserve workout history

**Duplicate Exercises**
- Copy existing exercise as template
- Modify for variations
- Saves time creating similar exercises

## Warmup Drills

### Purpose

Warmup Drills provide structured warmup sequences with video demonstrations and progressive action levels. Athletes can follow along with proper warmup routines before practice or competition.

### Warmup Structure

**Progressive Action Levels**
- Level 1: Light activation (5-7 minutes)
- Level 2: Dynamic mobility (5-7 minutes)
- Level 3: Sport-specific movements (5-7 minutes)
- Level 4: High-intensity prep (3-5 minutes)

### Creating Warmup Sequences

1. **Access Warmup Management**
   - Navigate to Admin Dashboard
   - Tap "Warmup Drills"
   - View existing sequences

2. **Create New Sequence**
   - Tap "New Warmup Sequence"
   - Enter sequence name (e.g., "Pre-Practice Warmup")
   - Select target action level

3. **Add Drills to Sequence**
   - Select drill type (dynamic stretch, activation, drill)
   - Enter drill name and description
   - Set duration or rep count
   - Define rest periods

4. **Upload Media**
   - Record or upload demonstration video
   - Add multiple camera angles if needed
   - Upload images for reference
   - Add slow-motion clips for technique

5. **Organize Sequence**
   - Drag to reorder drills
   - Group by action level
   - Set transitions between levels
   - Add notes or coaching points

### Action Level Media

Each action level can have:
- **Overview Video**: Shows entire level sequence
- **Individual Drill Videos**: Detailed demonstrations
- **Images**: Form photos and key positions
- **Coaching Audio**: Voice-over instructions
- **Cue Cards**: Visual reminders of key points

### Assigning Warmups

**To Practice Sessions**
1. Open calendar event for practice
2. Select "Assign Warmup"
3. Choose warmup sequence
4. All athletes see warmup in their app before practice

**To Workouts**
1. Create workout template
2. Add warmup as first section
3. Athletes complete warmup before main workout

**Athlete Access**
- Athletes can view warmups on demand
- Follow along with videos
- Track warmup completion
- Save favorite warmups

### Warmup Best Practices

**Progression Principle**
- Start with gentle movements (Level 1)
- Progress to more dynamic actions (Levels 2-3)
- Finish with sport-specific drills (Level 4)
- Never skip levels

**Time Management**
- Total warmup: 20-30 minutes
- Don't rush early levels
- Adjust based on athlete age and experience
- Longer warmups in cold weather

**Video Guidelines**
- Clear lighting and camera angle
- Show full body in frame
- Demonstrate at normal speed
- Include slow-motion replay
- Add text overlays for reps/duration

## Data Model

### Exercise
```dart
{
  "id": "exercise_uuid",
  "name": "Box Jumps",
  "category": "strength",
  "subcategory": "plyometric",
  "description": "Explosive jump onto elevated box from standing position...",
  "difficulty": "intermediate",
  "equipment": ["box", "flat surface"],
  "videoUrl": "https://storage.googleapis.com/exercises/box-jumps.mp4",
  "imageUrl": "https://storage.googleapis.com/exercises/box-jumps-form.jpg",
  "coachingCues": [
    "Arms drive upward forcefully",
    "Land softly with bent knees",
    "Full hip extension at takeoff"
  ],
  "commonMistakes": [
    "Landing with locked knees",
    "Insufficient arm drive",
    "Hesitating at takeoff"
  ],
  "progressions": {
    "easier": "Step-ups",
    "harder": "Single-leg box jumps"
  },
  "createdBy": "admin_uid",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

### WarmupSequence
```dart
{
  "id": "warmup_uuid",
  "name": "Pre-Practice Warmup",
  "actionLevels": [
    {
      "level": 1,
      "name": "Light Activation",
      "drills": [
        {
          "name": "Jumping Jacks",
          "duration": "2 minutes",
          "videoUrl": "https://...",
          "description": "Full body activation..."
        }
      ],
      "mediaUrls": ["https://..."],
      "notes": "Focus on controlled movements"
    }
  ],
  "totalDuration": "25 minutes",
  "targetAudience": "all",
  "createdBy": "admin_uid",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

## Integration with Workouts

### Workout Template with Warmup

When creating workouts:
1. Add warmup section (from warmup library)
2. Add main exercises (from exercise library)
3. Add cooldown (optional)
4. Define sets/reps for each exercise
5. Assign to athletes

Athletes see:
- Warmup video demonstrations
- Exercise instructions with media
- Progressive difficulty levels
- Completion tracking

## Best Practices

### Exercise Library Management

**Organization**
- Use clear, consistent naming
- Categorize accurately
- Add detailed descriptions
- Include progressions for all levels

**Media Quality**
- High-resolution videos (1080p minimum)
- Multiple camera angles
- Clear audio for instructions
- Professional lighting

**Regular Updates**
- Review quarterly
- Update based on coach feedback
- Add new exercises as sport evolves
- Archive outdated techniques

### Warmup Design

**Effective Sequences**
- Total duration: 20-30 minutes
- Progressive intensity
- Sport-specific movements
- Address common problem areas

**Video Production**
- Record in actual training environment
- Use athletes as demonstrators
- Show common mistakes and corrections
- Add text overlays and timers

**Athlete Engagement**
- Make warmups fun and varied
- Rotate sequences to prevent boredom
- Allow athlete input on favorite drills
- Recognize athletes who complete warmups

## Troubleshooting

**Videos Won't Load**
- Check file size (max 50MB recommended)
- Verify video format (MP4 preferred)
- Test internet connection
- Use lower resolution for mobile

**Exercise Not Showing in Workout Builder**
- Verify exercise is saved
- Check category assignment
- Refresh exercise library
- Clear app cache

**Athletes Not Completing Warmups**
- Make sequences shorter (start with 15 min)
- Add more engaging videos
- Gamify with completion tracking
- Coach accountability

## Future Enhancements

Potential features under consideration:
- Exercise favoriting for quick access
- Athlete custom warmup creation
- Social sharing of exercises
- Exercise variation suggestions
- AI form analysis from video uploads
- Integration with wearable devices
- Warmup reminder notifications
- Community exercise database
