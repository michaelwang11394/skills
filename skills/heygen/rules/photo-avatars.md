---
name: photo-avatars
description: Creating avatars from photos (talking photos) for HeyGen
metadata:
  tags: photo-avatar, talking-photo, avatar-iv, image-to-video
---

# Photo Avatars (Talking Photos)

Photo avatars allow you to animate a static photo and make it speak. This is useful for creating personalized video content from portraits, headshots, or any suitable image.

## Overview

HeyGen offers several approaches to photo-based avatars:

| Type | Description | Quality |
|------|-------------|---------|
| Talking Photo | Basic photo animation | Good |
| Photo Avatar | Enhanced photo avatar with motion | Better |
| Avatar IV | Latest generation photo avatar | Best |

## Uploading a Photo for Talking Photo

### Step 1: Upload the Image

```typescript
// Upload photo as an asset
const assetId = await uploadFile("./portrait.jpg", "image/jpeg");
```

### Step 2: Create Talking Photo

```bash
curl -X POST "https://api.heygen.com/v2/talking_photo" \
  -H "X-Api-Key: $HEYGEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "image_url": "https://files.heygen.ai/asset/your_asset_id"
  }'
```

### TypeScript

```typescript
interface TalkingPhotoResponse {
  error: null | string;
  data: {
    talking_photo_id: string;
  };
}

async function createTalkingPhoto(imageUrl: string): Promise<string> {
  const response = await fetch("https://api.heygen.com/v2/talking_photo", {
    method: "POST",
    headers: {
      "X-Api-Key": process.env.HEYGEN_API_KEY!,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ image_url: imageUrl }),
  });

  const json: TalkingPhotoResponse = await response.json();

  if (json.error) {
    throw new Error(json.error);
  }

  return json.data.talking_photo_id;
}
```

## Using Talking Photo in Video

```typescript
const videoConfig = {
  video_inputs: [
    {
      character: {
        type: "talking_photo",
        talking_photo_id: "your_talking_photo_id",
      },
      voice: {
        type: "text",
        input_text: "Hello! This is my talking photo speaking!",
        voice_id: "1bd001e7e50f421d891986aad5158bc8",
      },
    },
  ],
  dimension: { width: 1920, height: 1080 },
};

const videoId = await generateVideo(videoConfig);
```

## Avatar IV API

Avatar IV is HeyGen's latest photo avatar technology with improved quality and natural motion.

### Generate Avatar IV Video

```bash
curl -X POST "https://api.heygen.com/v2/video/av4/generate" \
  -H "X-Api-Key: $HEYGEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "photo_s3_key": "path/to/uploaded/photo.jpg",
    "script": "Hello! This is Avatar IV with enhanced quality.",
    "voice_id": "1bd001e7e50f421d891986aad5158bc8",
    "video_orientation": "portrait",
    "video_title": "My Avatar IV Video"
  }'
```

### TypeScript

```typescript
interface AvatarIVRequest {
  photo_s3_key: string;
  script: string;
  voice_id: string;
  video_orientation?: "portrait" | "landscape" | "square";
  video_title?: string;
  fit?: "cover" | "contain";
  custom_motion_prompt?: string;
  enhance_custom_motion_prompt?: boolean;
}

interface AvatarIVResponse {
  error: null | string;
  data: {
    video_id: string;
  };
}

async function generateAvatarIVVideo(config: AvatarIVRequest): Promise<string> {
  const response = await fetch(
    "https://api.heygen.com/v2/video/av4/generate",
    {
      method: "POST",
      headers: {
        "X-Api-Key": process.env.HEYGEN_API_KEY!,
        "Content-Type": "application/json",
      },
      body: JSON.stringify(config),
    }
  );

  const json: AvatarIVResponse = await response.json();

  if (json.error) {
    throw new Error(json.error);
  }

  return json.data.video_id;
}
```

## Avatar IV Options

### Video Orientation

| Orientation | Dimensions | Use Case |
|-------------|------------|----------|
| `portrait` | 720x1280 | TikTok, Stories |
| `landscape` | 1280x720 | YouTube, Web |
| `square` | 720x720 | Instagram Feed |

### Fit Options

| Fit | Description |
|-----|-------------|
| `cover` | Fill the frame, may crop edges |
| `contain` | Fit entire image, may show background |

### Custom Motion Prompts

Add specific motion or expression:

```typescript
const config = {
  photo_s3_key: "path/to/photo.jpg",
  script: "Let me tell you about our product.",
  voice_id: "voice_id",
  custom_motion_prompt: "nodding head and smiling",
  enhance_custom_motion_prompt: true,
};
```

## Photo Avatar Groups

Organize multiple photo looks:

### Create Photo Avatar Group

```typescript
interface PhotoAvatarGroupRequest {
  image_key: string;
  name: string;
  generation_id?: string;
}

async function createPhotoAvatarGroup(
  imageKey: string,
  name: string
): Promise<string> {
  const response = await fetch(
    "https://api.heygen.com/v2/photo_avatar/avatar_group/create",
    {
      method: "POST",
      headers: {
        "X-Api-Key": process.env.HEYGEN_API_KEY!,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ image_key: imageKey, name }),
    }
  );

  const json = await response.json();

  if (json.error) {
    throw new Error(json.error);
  }

  return json.data.id;
}
```

### Add Photos to Group

```typescript
async function addPhotosToGroup(
  groupId: string,
  imageKeys: string[],
  name: string
): Promise<void> {
  const response = await fetch(
    "https://api.heygen.com/v2/photo_avatar/avatar_group/add",
    {
      method: "POST",
      headers: {
        "X-Api-Key": process.env.HEYGEN_API_KEY!,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        group_id: groupId,
        image_keys: imageKeys,
        name,
      }),
    }
  );

  const json = await response.json();

  if (json.error) {
    throw new Error(json.error);
  }
}
```

## Photo Requirements

For best results, use photos that meet these criteria:

### Technical Requirements

| Aspect | Requirement |
|--------|-------------|
| Format | JPEG, PNG |
| Resolution | Minimum 512x512px |
| File size | Under 10MB |
| Face visibility | Clear, front-facing |

### Quality Guidelines

1. **Lighting** - Even, natural lighting on face
2. **Expression** - Neutral or slight smile
3. **Background** - Simple, uncluttered
4. **Face position** - Centered, not cut off
5. **Clarity** - Sharp, in focus
6. **Angle** - Straight-on or slight angle

## Generating AI Photos

Generate synthetic photo avatars:

```typescript
interface GeneratePhotoRequest {
  appearance: string;
  age?: string;
  gender?: "male" | "female";
  ethnicity?: string;
  orientation?: string;
  pose?: string;
  style?: string;
}

async function generatePhotoAvatar(
  config: GeneratePhotoRequest
): Promise<string> {
  const response = await fetch(
    "https://api.heygen.com/v2/photo_avatar/photo/generate",
    {
      method: "POST",
      headers: {
        "X-Api-Key": process.env.HEYGEN_API_KEY!,
        "Content-Type": "application/json",
      },
      body: JSON.stringify(config),
    }
  );

  const json = await response.json();
  return json.data.generation_id;
}

// Check generation status
async function checkPhotoGeneration(generationId: string) {
  const response = await fetch(
    `https://api.heygen.com/v2/photo_avatar/generation/${generationId}`,
    { headers: { "X-Api-Key": process.env.HEYGEN_API_KEY! } }
  );

  return response.json();
}
```

## Complete Workflow: Photo to Video

```typescript
async function createVideoFromPhoto(
  photoPath: string,
  script: string,
  voiceId: string
): Promise<string> {
  // 1. Upload photo
  console.log("Uploading photo...");
  const assetId = await uploadFile(photoPath, "image/jpeg");

  // 2. Create talking photo
  console.log("Creating talking photo...");
  const talkingPhotoId = await createTalkingPhoto(
    `https://files.heygen.ai/asset/${assetId}`
  );

  // 3. Generate video
  console.log("Generating video...");
  const videoId = await generateVideo({
    video_inputs: [
      {
        character: {
          type: "talking_photo",
          talking_photo_id: talkingPhotoId,
        },
        voice: {
          type: "text",
          input_text: script,
          voice_id: voiceId,
        },
      },
    ],
    dimension: { width: 1920, height: 1080 },
  });

  // 4. Wait for completion
  console.log("Processing video...");
  const videoUrl = await waitForVideo(videoId);

  return videoUrl;
}
```

## Managing Photo Avatars

### Get Photo Avatar Details

```typescript
async function getPhotoAvatar(id: string) {
  const response = await fetch(
    `https://api.heygen.com/v2/photo_avatar/${id}`,
    { headers: { "X-Api-Key": process.env.HEYGEN_API_KEY! } }
  );

  return response.json();
}
```

### Delete Photo Avatar

```typescript
async function deletePhotoAvatar(id: string): Promise<void> {
  const response = await fetch(
    `https://api.heygen.com/v2/photo_avatar/${id}`,
    {
      method: "DELETE",
      headers: { "X-Api-Key": process.env.HEYGEN_API_KEY! },
    }
  );

  if (!response.ok) {
    throw new Error("Failed to delete photo avatar");
  }
}
```

## Best Practices

1. **Use high-quality photos** - Better input = better output
2. **Front-facing portraits** - Work best for animation
3. **Neutral expressions** - Allow for more natural animation
4. **Test different photos** - Results vary by image
5. **Consider Avatar IV** - For highest quality results
6. **Organize with groups** - Keep photo avatars organized

## Limitations

- Photo quality significantly affects output
- Side-profile photos have limited support
- Full-body photos may not animate properly
- Some expressions may look unnatural
- Processing time varies by complexity
