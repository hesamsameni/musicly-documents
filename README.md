# Musicly API Documentation

This document provides comprehensive API documentation for mobile developers integrating with the Musicly backend.

## Table of Contents

- [Base URL](#base-url)
- [Authentication](#authentication)
- [Error Handling](#error-handling)
- [Endpoints](#endpoints)
  - [Posts](#posts)
  - [Users](#users)
  - [Comments](#comments)
  - [Bookmarks](#bookmarks)
  - [Feed](#feed)
  - [Songs](#songs)
  - [Newsletter](#newsletter)
  - [Music Integrations](#music-integrations)

---

## Base URL

```
Production: https://musicly.me/api
Development: http://localhost:3000/api
```

---

## Authentication

Most endpoints require authentication using Supabase JWT tokens. There are two types of authentication:

### 1. User Authentication (Supabase JWT)

For endpoints that require user authentication, include the Supabase access token in the `Authorization` header:

```
Authorization: Bearer <supabase_access_token>
```

**How to get the token:**

- After user login/signup with Supabase, retrieve the access token from the Supabase client
- The token should be included in all authenticated requests

### 2. App Secret Authentication

Some endpoints (like `/api/song`, `/api/song-detail`, `/api/preview`, `/api/users/search`) require an app secret instead of user authentication:

```
x-app-secret: <app_secret>
```

OR

```
Authorization: Bearer <app_secret>
```

**Note:** Contact hesam.sameni@gmail.com to obtain the app secret.

---

## Error Handling

All endpoints follow standard HTTP status codes:

- `200` - Success
- `400` - Bad Request (missing or invalid parameters)
- `401` - Unauthorized (missing or invalid authentication)
- `403` - Forbidden (insufficient permissions)
- `404` - Not Found
- `409` - Conflict (e.g., duplicate subscription)
- `500` - Internal Server Error

Error responses follow this format:

```json
{
  "statusCode": 400,
  "statusMessage": "Bad Request",
  "message": "Detailed error message"
}
```

---

## Endpoints

### Posts

#### Create Post

Create a new post with a song.

**Endpoint:** `POST /api/posts`

**Authentication:** Required (User JWT)

**Request Body:**

```json
{
  "songTitle": "Song Title",
  "artist": "Artist Name",
  "songLinks": {
    "spotify": "https://open.spotify.com/track/...",
    "youtube": "https://youtube.com/watch?v=...",
    "apple": "https://music.apple.com/...",
    "soundcloud": "https://soundcloud.com/...",
    "deezer": "https://deezer.com/track/..."
  },
  "songCountry": "US",
  "songThumbnail": "https://image.url",
  "content": "Post content text"
}
```

**Response:**

```json
{
  "id": "post_id",
  "user_id": "user_id",
  "song_id": "song_id",
  "content": "Post content text",
  "created_at": "2024-01-01T00:00:00Z",
  "profiles": [
    {
      "user_id": "user_id",
      "display_name": "User Name",
      "avatar_url": "https://avatar.url",
      "username": "username"
    }
  ],
  "songs": [
    {
      "song_id": "song_id",
      "title": "Song Title",
      "artist": "Artist Name",
      "thumbnailUrl": "https://image.url",
      "linksByPlatform": {
        "spotify": "https://open.spotify.com/track/..."
      }
    }
  ],
  "likes": 0,
  "reposts": 0,
  "commentCount": 0,
  "isLiked": false,
  "isReposted": false
}
```

---

#### Get Post

Get a single post by ID.

**Endpoint:** `GET /api/posts/:id`

**Authentication:** Optional (User JWT for `isLiked` and `isReposted`)

**Response:**

```json
{
  "id": "post_id",
  "user_id": "user_id",
  "song_id": "song_id",
  "content": "Post content text",
  "created_at": "2024-01-01T00:00:00Z",
  "profiles": [
    {
      "user_id": "user_id",
      "display_name": "User Name",
      "avatar_url": "https://avatar.url"
    }
  ],
  "songs": [
    {
      "song_id": "song_id",
      "title": "Song Title",
      "artist": "Artist Name",
      "thumbnailUrl": "https://image.url",
      "linksByPlatform": {}
    }
  ],
  "likes": [],
  "reposts": 0,
  "commentCount": 0,
  "isLiked": false,
  "isReposted": false
}
```

---

#### Delete Post

Delete a post (only by the post owner).

**Endpoint:** `DELETE /api/posts/:id`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "success": true
}
```

---

#### Like/Unlike Post

Toggle like on a post.

**Endpoint:** `POST /api/posts/:id/like`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "liked": true
}
```

or

```json
{
  "liked": false
}
```

---

#### Repost/Unrepost Post (WIP!)

Toggle repost on a post.

**Endpoint:** `POST /api/posts/:id/repost`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "repost_count": 5,
  "is_reposted": true
}
```

---

#### Get Post Comments

Get all comments for a post.

**Endpoint:** `GET /api/posts/:id/comments`

**Authentication:** Optional (User JWT for `is_liked` status)

**Response:**

```json
[
  {
    "comment_id": "comment_id",
    "post_id": "post_id",
    "user_id": "user_id",
    "content": "Comment text",
    "created_at": "2024-01-01T00:00:00Z",
    "parent_comment_id": null,
    "profile": {
      "user_id": "user_id",
      "display_name": "User Name",
      "avatar_url": "https://avatar.url"
    },
    "like_count": 0,
    "is_liked": false
  }
]
```

---

#### Create Comment

Add a comment to a post.

**Endpoint:** `POST /api/posts/:id/comments`

**Authentication:** Required (User JWT)

**Request Body:**

```json
{
  "content": "Comment text"
}
```

**Response:**

```json
{
  "comment_id": "comment_id",
  "post_id": "post_id",
  "user_id": "user_id",
  "content": "Comment text",
  "created_at": "2024-01-01T00:00:00Z",
  "profile": {
    "user_id": "user_id",
    "display_name": "User Name",
    "avatar_url": "https://avatar.url"
  }
}
```

---

### Users

#### Get User Profile

Get a user's profile information.

**Endpoint:** `GET /api/users/:id`

**Authentication:** Not required

**Response:**

```json
{
  "user_id": "user_id",
  "username": "username",
  "display_name": "User Name",
  "avatar_url": "https://avatar.url",
  "bio": "User bio",
  "is_pro": false
}
```

---

#### Update Current User Profile

Update the authenticated user's profile.

**Endpoint:** `PUT /api/users/me`

**Authentication:** Required (User JWT)

**Request Body:**

```json
{
  "display_name": "New Display Name",
  "bio": "New bio text",
  "avatar_url": "https://new-avatar.url"
}
```

**Response:**

```json
{
  "success": true
}
```

---

#### Upload Avatar

Upload a new avatar image for the current user.

**Endpoint:** `POST /api/users/me/avatar`

**Authentication:** Required (User JWT)

**Request:** Multipart form data

- `file`: Image file (jpg, png, etc.)

**Response:**

```
"https://storage.url/path/to/avatar.jpg"
```

Returns the public URL of the uploaded avatar.

---

#### Get User Posts

Get all posts by a specific user.

**Endpoint:** `GET /api/users/:id/posts`

**Authentication:** Optional (User JWT for `isLiked` and `isReposted`)

**Query Parameters:**

- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 10)

**Response:**

```json
[
  {
    "id": "post_id",
    "user_id": "user_id",
    "song_id": "song_id",
    "content": "Post content",
    "created_at": "2024-01-01T00:00:00Z",
    "profiles": [...],
    "songs": [...],
    "likes": 5,
    "reposts": 2,
    "commentCount": 3,
    "isLiked": false,
    "isReposted": false
  }
]
```

---

#### Get User Stats

Get statistics for a user.

**Endpoint:** `GET /api/users/:id/stats`

**Authentication:** Optional (User JWT for `isFollowing`)

**Response:**

```json
{
  "followers": 100,
  "following": 50,
  "posts": 25,
  "likes": 200,
  "isFollowing": false
}
```

---

#### Follow/Unfollow User

Toggle follow status for a user.

**Endpoint:** `POST /api/users/:id/follow`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "isFollowing": true
}
```

or

```json
{
  "isFollowing": false
}
```

---

#### Get User Followers

Get list of users following a specific user.

**Endpoint:** `GET /api/users/:id/followers`

**Authentication:** Optional (User JWT for `isFollowing` status)

**Response:**

```json
[
  {
    "user_id": "user_id",
    "display_name": "Follower Name",
    "avatar_url": "https://avatar.url",
    "isFollowing": false
  }
]
```

---

#### Get User Following

Get list of users that a specific user is following.

**Endpoint:** `GET /api/users/:id/following`

**Authentication:** Optional (User JWT for `isFollowing` status)

**Response:**

```json
[
  {
    "user_id": "user_id",
    "display_name": "Following Name",
    "avatar_url": "https://avatar.url",
    "isFollowing": true
  }
]
```

---

#### Get User Likes

Get posts liked by a user.

**Endpoint:** `GET /api/users/:id/likes`

**Authentication:** Optional

**Response:**

```json
[
  {
    "id": "post_id",
    "user_id": "user_id",
    "song_id": "song_id",
    "content": "Post content",
    "created_at": "2024-01-01T00:00:00Z",
    "profiles": [...],
    "songs": [...],
    "likes": 5,
    "reposts": 2,
    "commentCount": 3,
    "isLiked": true,
    "isReposted": false
  }
]
```

---

#### Search Users

Search for users by username.

**Endpoint:** `GET /api/users/search`

**Authentication:** Required (App Secret)

**Headers:**

```
x-app-secret: <app_secret>
```

**Query Parameters:**

- `q` (required): Search query

**Response:**

```json
{
  "users": [
    {
      "user_id": "user_id",
      "username": "username",
      "display_name": "Display Name",
      "avatar_url": "https://avatar.url",
      "is_pro": false
    }
  ]
}
```

---

### Comments

#### Like/Unlike Comment

Toggle like on a comment.

**Endpoint:** `POST /api/comments/:id/like`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "like_count": 5,
  "is_liked": true
}
```

---

#### Reply to Comment

Add a reply to a comment.

**Endpoint:** `POST /api/comments/:id/reply`

**Authentication:** Required (User JWT)

**Request Body:**

```json
{
  "postId": "post_id",
  "content": "Reply text"
}
```

**Response:**

```json
{
  "comment_id": "comment_id",
  "post_id": "post_id",
  "user_id": "user_id",
  "content": "Reply text",
  "created_at": "2024-01-01T00:00:00Z",
  "parent_comment_id": "parent_comment_id",
  "profile": {
    "user_id": "user_id",
    "display_name": "User Name",
    "avatar_url": "https://avatar.url"
  },
  "like_count": 0,
  "is_liked": false
}
```

---

#### Delete Comment

Delete a comment (only by the comment owner).

**Endpoint:** `DELETE /api/comments/:id`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "success": true
}
```

---

### Bookmarks

#### Get Bookmarks

Get all bookmarks for a user.

**Endpoint:** `GET /api/bookmarks`

**Authentication:** Required (User JWT)

**Query Parameters:**

- `userId` (optional): User ID (defaults to authenticated user)

**Response:**

```json
[
  {
    "id": "bookmark_id",
    "name": "Bookmark Name",
    "user_id": "user_id",
    "songs": [
      {
        "song_id": "song_id",
        "title": "Song Title",
        "artist": "Artist Name",
        "thumbnailUrl": "https://image.url",
        "linksByPlatform": {
          "spotify": "https://open.spotify.com/track/..."
        }
      }
    ]
  }
]
```

---

#### Create Bookmark

Create a new bookmark.

**Endpoint:** `POST /api/bookmarks`

**Authentication:** Required (User JWT)

**Request Body:**

```json
{
  "name": "Bookmark Name"
}
```

**Response:**

```json
{
  "id": "bookmark_id",
  "name": "Bookmark Name",
  "user_id": "user_id",
  "songs": []
}
```

---

#### Update Bookmark

Update a bookmark's name.

**Endpoint:** `PUT /api/bookmarks/:id`

**Authentication:** Required (User JWT)

**Request Body:**

```json
{
  "name": "Updated Bookmark Name"
}
```

**Response:**

```json
{
  "id": "bookmark_id",
  "name": "Updated Bookmark Name",
  "user_id": "user_id"
}
```

---

#### Delete Bookmark

Delete a bookmark.

**Endpoint:** `DELETE /api/bookmarks/:id`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "success": true
}
```

---

#### Add Song to Bookmark

Add a song to a bookmark.

**Endpoint:** `POST /api/bookmarks/:id/songs`

**Authentication:** Required (User JWT)

**Request Body:**

```json
{
  "songId": "song_id"
}
```

**Response:**

```json
{
  "success": true
}
```

---

#### Remove Song from Bookmark

Remove a song from a bookmark.

**Endpoint:** `DELETE /api/bookmarks/:id/songs/:songId`

**Authentication:** Required (User JWT)

**Response:**

```json
{
  "success": true
}
```

---

### Feed

#### Get Feed

Get the main feed of posts.

**Endpoint:** `GET /api/feed`

**Authentication:** Optional (User JWT for `isLiked` and `isReposted`)

**Query Parameters:**

- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 10)

**Response:**

```json
{
  "posts": [
    {
      "id": "post_id",
      "user_id": "user_id",
      "song_id": "song_id",
      "content": "Post content",
      "created_at": "2024-01-01T00:00:00Z",
      "profiles": [...],
      "songs": [...],
      "likes": 5,
      "reposts": 2,
      "commentCount": 3,
      "isLiked": false,
      "isReposted": false
    }
  ],
  "total": 100
}
```

---

### Songs

#### Get Song Data

Fetch song data from a URL.

**Endpoint:** `GET /api/song`

**Authentication:** Required (App Secret)

**Headers:**

```
x-app-secret: <app_secret>
```

**Query Parameters:**

- `url` (required): Song URL from any supported platform

**Response:**

```json
{
  "title": "Song Title",
  "artist": "Artist Name",
  "thumbnailUrl": "https://image.url",
  "linksByPlatform": {
    "spotify": "https://open.spotify.com/track/...",
    "youtube": "https://youtube.com/watch?v=..."
  }
}
```

---

#### Get Song Detail (Multi-Platform) THIS API IS A FALLBACK TO /api/song - we may switch to this one in future but for now we are not using this!

Fetch detailed song data from multiple platforms.

**Endpoint:** `GET /api/song-detail`

**Authentication:** Required (App Secret)

**Headers:**

```
x-app-secret: <app_secret>
```

**Query Parameters:**

- `url` (required): Song URL

**Response:**

```json
{
  "title": "Song Title",
  "artist": "Artist Name",
  "thumbnailUrl": "https://image.url",
  "linksByPlatform": {
    "spotify": "https://open.spotify.com/track/...",
    "youtube": "https://youtube.com/watch?v=...",
    "apple": "https://music.apple.com/...",
    "soundcloud": "https://soundcloud.com/...",
    "deezer": "https://deezer.com/track/..."
  }
}
```

---

#### Get Song Preview

Get preview URL for a Deezer track.

**Endpoint:** `GET /api/preview`

**Authentication:** Required (App Secret)

**Headers:**

```
x-app-secret: <app_secret>
```

**Query Parameters:**

- `songId` (required): Deezer track ID

**Response:**

```json
{
  "previewUrl": "https://cdns-preview-...mp3"
}
```

---

### Newsletter

#### Subscribe to Newsletter

Subscribe an email to the newsletter.

**Endpoint:** `POST /api/newsletter/subscribe`

**Authentication:** Not required

**Request Body:**

```json
{
  "email": "user@example.com"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Successfully subscribed to newsletter"
}
```

**Error Responses:**

- `409`: Email already subscribed
- `400`: Invalid email format

---

#### Unsubscribe from Newsletter

Unsubscribe an email from the newsletter.

**Endpoint:** `POST /api/newsletter/unsubscribe`

**Authentication:** Not required

**Request Body:**

```json
{
  "email": "user@example.com"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Successfully unsubscribed from newsletter"
}
```

---

## Data Models

### Post Object

```json
{
  "id": "string",
  "user_id": "string",
  "song_id": "string",
  "content": "string",
  "created_at": "ISO 8601 datetime",
  "profiles": [Profile],
  "songs": [Song],
  "likes": "number",
  "reposts": "number",
  "commentCount": "number",
  "isLiked": "boolean",
  "isReposted": "boolean"
}
```

### Profile Object

```json
{
  "user_id": "string",
  "username": "string",
  "display_name": "string",
  "avatar_url": "string",
  "bio": "string",
  "is_pro": "boolean"
}
```

### Song Object

```json
{
  "song_id": "string",
  "title": "string",
  "artist": "string",
  "thumbnailUrl": "string",
  "linksByPlatform": {
    "spotify": "string",
    "youtube": "string",
    "apple": "string",
    "soundcloud": "string",
    "deezer": "string"
  }
}
```

### Comment Object

```json
{
  "comment_id": "string",
  "post_id": "string",
  "user_id": "string",
  "content": "string",
  "created_at": "ISO 8601 datetime",
  "parent_comment_id": "string | null",
  "profile": Profile,
  "like_count": "number",
  "is_liked": "boolean"
}
```

---

## Rate Limiting

Currently, there are no explicit rate limits. However, please implement reasonable rate limiting on the client side to avoid overwhelming the server.

---

## Best Practices

1. **Token Management:**

   - Store Supabase access tokens securely
   - Refresh tokens before they expire
   - Handle token refresh errors gracefully

2. **Error Handling:**

   - Always check HTTP status codes
   - Display user-friendly error messages
   - Implement retry logic for network errors

3. **Pagination:**

   - Use pagination for feed and list endpoints
   - Implement infinite scroll or "Load More" functionality
   - Default page size is 10 items

4. **Image Handling:**

   - Cache avatar and thumbnail images
   - Use appropriate image sizes for mobile
   - Handle image loading errors

5. **Offline Support:**
   - Cache frequently accessed data
   - Queue actions when offline
   - Sync when connection is restored

---

## Support

For questions or issues, please contact hesam.sameni@gmail.com

---

**Last Updated:** 19th November 2025
**API Version:** 1.0
