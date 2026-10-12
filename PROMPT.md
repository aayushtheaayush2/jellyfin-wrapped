# Jellyfin Wrapped: Modern Jellyfin (10.9+) Compatibility Fixes

## Objective
Update the backend codebase of `jellyfin-wrapped` to support modern Jellyfin server releases (.NET 8 / Jellyfin 10.9+), resolve CORS domain lockouts, decouple frontend URL validation from SSO/OIDC, and modernize API token authorization headers.

---

## 1. Modernize User Authentication Header

### Target File
`backend/src/routes/auth.ts` (or `backend/src/services/auth.ts`)

### Problem
Jellyfin 10.9+ strictly enforces ASP.NET Core authentication middleware on `/Users/AuthenticateByName`. The legacy `X-Emby-Authorization` header alone causes Jellyfin to drop client identification, throwing `ArgumentException (Parameter 'App')` and returning `400 Error processing request`.

### Required Change
In the `fetch` or `axios` call targeting `${jellyfinServerUrl}/Users/AuthenticateByName`, add the standard `Authorization` header alongside `X-Emby-Authorization`:

```typescript
// Before:
headers: {
  'Content-Type': 'application/json',
  'X-Emby-Authorization': `MediaBrowser Client="Jellyfin-Wrapped-Backend", Device="Server", DeviceId="jellyfin-wrapped-backend", Version="1.0.0"`,
}

// After:
headers: {
  'Content-Type': 'application/json',
  'Authorization': `MediaBrowser Client="Jellyfin-Wrapped-Backend", Device="Server", DeviceId="jellyfin-wrapped-backend", Version="1.0.0"`,
  'X-Emby-Authorization': `MediaBrowser Client="Jellyfin-Wrapped-Backend", Device="Server", DeviceId="jellyfin-wrapped-backend", Version="1.0.0"`,
}