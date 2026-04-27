# 🐱 TheCatAPI — REST API Testing Project

A hands-on API testing project covering real-world validation techniques including positive/negative testing, authentication, boundary analysis, and automated test scripting with Postman.

**API Under Test:** [TheCatAPI](https://thecatapi.com/) — a public REST API serving cat images and breed data.

---

## 🛠 Tools & Technologies

| Tool | Purpose |
|------|---------|
| Postman | Request execution, test scripting, collection management |
| TheCatAPI | Target API (public + authenticated endpoints) |

---

## 📁 Project Structure

```
thecatapi-testing/
├── README.md
├── TEST_REPORT.md
└── TheCatAPI_WithVariables.postman_collection.json
```

---

## 🧪 Endpoints Tested

| Method | Endpoint | Auth | Type |
|--------|----------|------|------|
| GET | /images/search | ❌ | Positive, Boundary |
| GET | /breeds/search | ❌ | Positive, Negative |
| POST | /favourites | ✅ | Authenticated |
| DELETE | /favourites/{id} | ✅ | Authenticated |
| POST | /votes | ✅ | Authenticated |

---

## 🐛 Bug Found

### BUG-001 — `limit` Parameter Not Enforced

| Field | Detail |
|-------|--------|
| Endpoint | GET /images/search |
| Parameter | `limit` |
| Tested Values | `limit=2`, `limit=1000` |
| Expected | Return exactly N images as requested |
| Actual | Returns 10 images regardless of value |
| Severity | Medium |

The API silently ignores out-of-range or specific `limit` values and always defaults to 10 results with a 200 OK response — no validation error is returned. Better API design would either enforce the limit strictly or return a `400 Bad Request` with a message like `"limit must be between 1 and 100"`.

---

## ✅ Automated Test Scripts (Postman)

Written for `GET /images/search?mime_types=gif&limit=1`:

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

**Result: 5/5 tests passed ✅**

---

## 🚀 How to Run

1. Import `TheCatAPI_WithVariables.postman_collection.json` into Postman
2. Create a Postman environment with:
   - `base_url` = `https://api.thecatapi.com/v1`
   - `api_key` = your free API key from [thecatapi.com](https://thecatapi.com/)
3. Run requests in order — some depend on responses from previous requests
4. For the GIF search request, check the **Test Results** tab after sending

---

## 📊 Test Summary

| Method | Endpoint | Result |
|--------|----------|--------|
| GET | /images/search?limit=2&mime_types=jpg | ⚠️ PASS + BUG-001 |
| GET | /breeds/search?q=abcxyz | ✅ PASS |
| GET | /breeds/search?q=sib | ✅ PASS |
| GET | /images/search?mime_types=gif&limit=1 | ✅ PASS (5/5 scripts) |
| GET | /images/search?limit=1000 | 🐛 BUG-001 confirmed |
| POST | /favourites | ✅ PASS |
| DELETE | /favourites/{id} | ✅ PASS |
| POST | /votes | ✅ PASS |