# 📋 Test Execution Report — TheCatAPI

**Project:** TheCatAPI REST API Testing  
**Tester:** Sirajul Islam  
**Tool:** Postman  
**Base URL:** `https://api.thecatapi.com/v1`  
**Date:** April 2026

---

## TC_001 — Search Images by MIME Type with Limit

| Field | Detail |
|-------|--------|
| Method | GET |
| Endpoint | /images/search?limit=2&mime_types=jpg |
| Auth | Not required |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Result | ⚠️ PASS + BUG-001 |

**Actual Response:**
```json
[
  {"id": "2hp", "url": "https://cdn2.thecatapi.com/images/2hp.jpg", "width": 500, "height": 276},
  {"id": "6lh", "url": "https://cdn2.thecatapi.com/images/6lh.jpg", "width": 500, "height": 335},
  {"id": "a1o", "url": "https://cdn2.thecatapi.com/images/a1o.jpg", "width": 446, "height": 597},
  {"id": "a9o", "url": "https://cdn2.thecatapi.com/images/a9o.jpg", "width": 560, "height": 536},
  {"id": "b8d", "url": "https://cdn2.thecatapi.com/images/b8d.jpg", "width": 500, "height": 375},
  {"id": "blp", "url": "https://cdn2.thecatapi.com/images/blp.jpg", "width": 1936, "height": 2592},
  {"id": "bq6", "url": "https://cdn2.thecatapi.com/images/bq6.jpg", "width": 800, "height": 600},
  {"id": "MTY0NjA3Mg", "url": "https://cdn2.thecatapi.com/images/MTY0NjA3Mg.jpg", "width": 640, "height": 425},
  {"id": "mZZzMlywy", "url": "https://cdn2.thecatapi.com/images/mZZzMlywy.jpg", "width": 950, "height": 950},
  {"id": "4Q5bRarQO", "url": "https://cdn2.thecatapi.com/images/4Q5bRarQO.jpg", "width": 1600, "height": 1067}
]
```

> ⚠️ `limit=2` was requested but 10 images were returned. API does not enforce the limit parameter. → **BUG-001**

---

## TC_002 — Search Breeds with Invalid Query (Negative Test)

| Field | Detail |
|-------|--------|
| Method | GET |
| Endpoint | /breeds/search?q=abcxyz |
| Auth | Not required |
| Expected Status | 200 OK or 404 |
| Actual Status | 200 OK |
| Result | ✅ PASS |

**Actual Response:**
```json
[]
```

> ✅ API correctly returns an empty array for a non-existent breed. No crash, no server error — expected behavior for a negative test.

---

## TC_003 — Search Breeds with Valid Query

| Field | Detail |
|-------|--------|
| Method | GET |
| Endpoint | /breeds/search?q=sib |
| Auth | Not required |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Result | ✅ PASS |

**Validations:**
- ✅ Status = 200
- ✅ Response is a valid JSON array
- ✅ Breed name contains "Siberian"
- ✅ Required fields present: id, name, temperament, origin, life_span
- ✅ Response time < 2 seconds

**Actual Response:**
```json
[
  {
    "id": "sibe",
    "name": "Siberian",
    "origin": "Russia",
    "temperament": "Curious, Intelligent, Loyal, Sweet, Agile, Playful, Affectionate",
    "life_span": "12 - 15",
    "description": "The Siberians dog like temperament and affection makes the ideal lap cat and will live quite happily indoors. Very agile and powerful, the Siberian cat can easily leap and reach high places, including the tops of refrigerators and even doors.",
    "hypoallergenic": 1,
    "weight": {"imperial": "8 - 16", "metric": "4 - 7"},
    "adaptability": 5,
    "affection_level": 5,
    "intelligence": 5,
    "energy_level": 5
  }
]
```

---

## TC_004 — Add Image to Favourites (Authenticated)

| Field | Detail |
|-------|--------|
| Method | POST |
| Endpoint | /favourites |
| Auth | x-api-key required |
| Request Body | `{"image_id": "O1X8xKt_H"}` |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Result | ✅ PASS |

**Actual Response:**
```json
{
  "message": "SUCCESS",
  "id": 232566368
}
```

---

## TC_005 — Delete a Favourite (Authenticated)

| Field | Detail |
|-------|--------|
| Method | DELETE |
| Endpoint | /favourites/232566368 |
| Auth | x-api-key required |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Result | ✅ PASS |

**Actual Response:**
```json
{
  "message": "SUCCESS"
}
```

---

## TC_006 — Vote on an Image (Authenticated)

| Field | Detail |
|-------|--------|
| Method | POST |
| Endpoint | /votes |
| Auth | x-api-key required |
| Request Body | `{"image_id": "aq5", "value": 1}` |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Result | ✅ PASS |

**Step 1 — Get image ID first:**
```json
[
  {
    "id": "aq5",
    "url": "https://cdn2.thecatapi.com/images/aq5.gif",
    "width": 500,
    "height": 281
  }
]
```

**Step 2 — POST /votes response:**
```json
{
  "message": "SUCCESS",
  "id": 1358932,
  "image_id": "aq5",
  "value": 1,
  "country_code": "BD"
}
```

---

## TC_007 — GIF Image Search with Postman Test Scripts

| Field | Detail |
|-------|--------|
| Method | GET |
| Endpoint | /images/search?mime_types=gif&limit=1 |
| Auth | Not required |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Result | ✅ PASS — 5/5 scripts passed |

**Actual Response:**
```json
[
  {
    "id": "5tg",
    "url": "https://cdn2.thecatapi.com/images/5tg.gif",
    "width": 350,
    "height": 167
  }
]
```

**Postman Test Results:**
```
✅ Status code is 200
✅ Response returns exactly 1 result
✅ Response is an array
✅ Image URL ends with .gif
✅ Image has required fields

5/5 passed
```

---

## TC_008 — Boundary Test: limit=1000

| Field | Detail |
|-------|--------|
| Method | GET |
| Endpoint | /images/search?limit=1000 |
| Auth | Not required |
| Expected | 1000 images OR 400 error with validation message |
| Actual Status | 200 OK |
| Images Returned | 10 |
| Result | 🐛 BUG-001 confirmed |

**Actual Response (first 3 of 10):**
```json
[
  {"id": "2kl", "url": "https://cdn2.thecatapi.com/images/2kl.jpg", "width": 500, "height": 334},
  {"id": "7gd", "url": "https://cdn2.thecatapi.com/images/7gd.jpg", "width": 500, "height": 334},
  {"id": "bf7", "url": "https://cdn2.thecatapi.com/images/bf7.jpg", "width": 500, "height": 333}
]
```

**Conclusion:** The API silently ignores the `limit` value and always defaults to 10 results — consistent across limit=2 and limit=1000. No validation error is returned. The API should either enforce the limit or return a `400 Bad Request` with a descriptive error message.

---

## Summary

| TC | Endpoint | Method | Type | Result |
|----|----------|--------|------|--------|
| TC_001 | /images/search?limit=2&mime_types=jpg | GET | Positive | ⚠️ PASS + BUG |
| TC_002 | /breeds/search?q=abcxyz | GET | Negative | ✅ PASS |
| TC_003 | /breeds/search?q=sib | GET | Positive | ✅ PASS |
| TC_004 | /favourites | POST | Authenticated | ✅ PASS |
| TC_005 | /favourites/{id} | DELETE | Authenticated | ✅ PASS |
| TC_006 | /votes | POST | Authenticated | ✅ PASS |
| TC_007 | /images/search?mime_types=gif&limit=1 | GET | Scripted | ✅ 5/5 |
| TC_008 | /images/search?limit=1000 | GET | Boundary | 🐛 BUG |

**Total: 8 test cases | 7 passed | 1 bug found (BUG-001)**