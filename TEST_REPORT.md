# 📋 Test Execution Report — TheCatAPI

**Project:** TheCatAPI API Testing Assessment  
**Tester:** Sirajul Islam  
**Tool:** Postman  
**Base URL:** `https://api.thecatapi.com/v1`  
**Date:** April 2026

---

## PART B — Test Case Design & Execution

---

### TC_001 — GET /images/search?limit=2&mime_types=jpg

| Field | Value |
|-------|-------|
| Test Case ID | TC_001 |
| Category | Positive Test Case |
| HTTP Method | GET |
| Endpoint | /images/search |
| Parameters | limit=2, mime_types=jpg |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Status | ✅ PASS (with observation) |

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

> ⚠️ **Observation / Bug:** `limit=2` was passed but 10 images were returned. The API did not enforce the limit parameter. See BUG-001 in README.

---

### TC_002 — GET /breeds/search?q=abcxyz

| Field | Value |
|-------|-------|
| Test Case ID | TC_002 |
| Category | Negative Test Case |
| HTTP Method | GET |
| Endpoint | /breeds/search |
| Parameters | q=abcxyz |
| Expected Status | 200 OK or 404 |
| Actual Status | 200 OK |
| Status | ✅ PASS |

**Actual Response:**
```json
[]
```

> ✅ API correctly returns an empty array for a non-existent breed query. This is expected behavior — no server error, no crash.

---

### TC_003 — POST /favourites

| Field | Value |
|-------|-------|
| Test Case ID | TC_003 |
| Category | Positive — Authenticated |
| HTTP Method | POST |
| Endpoint | /favourites |
| Headers | x-api-key: {{api_key}}, Content-Type: application/json |
| Request Body | {"image_id": "O1X8xKt_H"} |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Status | ✅ PASS |

**Actual Response:**
```json
{
  "message": "SUCCESS",
  "id": 232566368
}
```

---

### TC_004 — DELETE /favourites/{favourite_id}

| Field | Value |
|-------|-------|
| Test Case ID | TC_004 |
| Category | Positive — Authenticated |
| HTTP Method | DELETE |
| Endpoint | /favourites/232566368 |
| Headers | x-api-key: {{api_key}} |
| Expected Status | 200 OK |
| Actual Status | 200 OK |
| Status | ✅ PASS |

**Actual Response:**
```json
{
  "message": "SUCCESS"
}
```

---

## PART C — Execution & Output

---

### Q8 — GET /breeds/search?q=sib

**Status Code:** 200 OK  
**Result:** ✅ PASS

**Validations:**
- ✅ Status code = 200
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

### Q9 — POST /votes

**Status Code:** 200 OK  
**Result:** ✅ PASS

**Q9a — Get Image ID:**
```
GET https://api.thecatapi.com/v1/images/search?limit=1
```
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

**Q9b — POST /votes:**
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

### Q10 — GET /images/search?mime_types=gif&limit=1

**Status Code:** 200 OK  
**Result:** ✅ PASS

**Postman Test Script:**
```javascript
pm.test("Status code is 200", function () {
  pm.response.to.have.status(200);
});

pm.test("Response returns exactly 1 result", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData.length).to.equal(1);
});

pm.test("Response is an array", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData).to.be.an('array');
});

pm.test("Image URL ends with .gif", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData[0].url).to.include('.gif');
});

pm.test("Image has required fields", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData[0]).to.have.any.keys('id', 'url', 'width', 'height');
});
```

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

**Test Results:**
```
✅ Status code is 200
✅ Response returns exactly 1 result
✅ Response is an array
✅ Image URL ends with .gif
✅ Image has required fields

5/5 tests passed
```

---

### Q11 — GET /images/search?limit=1000 (Boundary Test)

**Status Code:** 200 OK  
**Images Returned:** 10  
**Result:** 🐛 BUG FOUND

| Scenario | Expected | Actual | Verdict |
|----------|----------|--------|---------|
| limit=1000 | 1000 images OR 400 error | 10 images, 200 OK | ❌ No input validation |

**Actual Response (first 3 of 10):**
```json
[
  {"id": "2kl", "url": "https://cdn2.thecatapi.com/images/2kl.jpg", "width": 500, "height": 334},
  {"id": "7gd", "url": "https://cdn2.thecatapi.com/images/7gd.jpg", "width": 500, "height": 334},
  {"id": "bf7", "url": "https://cdn2.thecatapi.com/images/bf7.jpg", "width": 500, "height": 333}
  // ... 7 more
]
```

**Conclusion:** The API silently ignores out-of-range `limit` values and defaults to returning 10 results. This behavior is consistent with TC_001 (limit=2 also returned 10). The API has a hardcoded default of 10 with no input validation on the limit parameter. Better API design would either enforce the limit or return a 400 error with a message like `"limit must be between 1 and 100"`.

---

## 📊 Final Summary

| TC | Endpoint | Method | Type | Result |
|----|----------|--------|------|--------|
| TC_001 | /images/search?limit=2&mime_types=jpg | GET | Positive | ⚠️ PASS + BUG |
| TC_002 | /breeds/search?q=abcxyz | GET | Negative | ✅ PASS |
| TC_003 | /favourites | POST | Authenticated | ✅ PASS |
| TC_004 | /favourites/{id} | DELETE | Authenticated | ✅ PASS |
| Q8 | /breeds/search?q=sib | GET | Positive | ✅ PASS |
| Q9 | /votes | POST | Authenticated | ✅ PASS |
| Q10 | /images/search?mime_types=gif&limit=1 | GET | Positive + Script | ✅ PASS |
| Q11 | /images/search?limit=1000 | GET | Boundary | 🐛 BUG |

**Bugs Found: 1 (BUG-001 — limit parameter not enforced)**