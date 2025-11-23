# 🗳️ **Election API Documentation (Django REST Framework)**

This API powers the student election system. It allows students to view elections, check eligibility, apply for positions, vote, and view results.

---

# **📌 Base URL**

```
https://portal.cbtq.app/evoting/
```

---

# 1️⃣ **Get All Active Elections**

### **GET** `/get_student_active_elections/`

Returns a list of all published **current-session SUG elections**.

### **Auth Required:** ✅ Yes

---

### ✔️ **Sample Response**

```json
[
  {
    "id": 3,
    "title": "SUG Election",
    "session": "2024/2025",
    "start_date": "2025-01-10",
    "end_date": "2025-01-15",
    "end_time": "17:00:00",
    "application_deadline": "2025-01-05",
    "status": "ongoing",
    "num_of_positions": 12,
    "num_of_candidates": 45,
    "num_of_voters": 2300
  }
]
```

---

# 2️⃣ **Get Single Election**

### **GET** `/get_student_active_election/{pk}/`

Retrieve details for **one election**.

### **Auth Required:** ✅ Yes

---

### ✔️ **URL Params**

| Param | Type    | Required | Description |
| ----- | ------- | -------- | ----------- |
| `pk`  | integer | ✅        | Election ID |

---

### ✔️ **Sample Response**

```json
{
  "id": 3,
  "title": "SUG Election",
  "session": "2024/2025",
  "start_date": "2025-01-10",
  "end_date": "2025-01-15",
  "end_time": "17:00:00",
  "application_deadline": "2025-01-05",
  "status": "upcoming",
  "num_of_positions": 12,
  "num_of_candidates": 45,
  "num_of_voters": 2300
}
```

---

# 3️⃣ **Check Student Eligibility for an Election**

### **GET** `/get_eligibility_criteria/{pk}/`

Checks whether the authenticated student is eligible to vote.

### **Auth Required:** ✅ Yes

---

### ✔️ **URL Params**

| Param | Type    | Required | Description |
| ----- | ------- | -------- | ----------- |
| `pk`  | integer | ✅        | Election ID |

---

### ✔️ **Success Response (Eligible)**

```json
{
  "message": "You are eligible to vote",
  "criteria": [
    {
      "criteria": "Active Student Status",
      "sub_text": "Must be currently enrolled in the school",
      "status": "active"
    },
    {
      "criteria": "Level Requirement",
      "sub_text": "You have to be in the eligible levels",
      "status": "eligible"
    },
    {
      "criteria": "Good Academic Standing",
      "sub_text": "Your CGPA must be at least 3.0",
      "status": true,
      "value": 3.52
    },
    {
      "criteria": "Disciplinary Issues",
      "sub_text": "You must not have any active disciplinary case",
      "status": true
    }
  ]
}
```

---

### ❌ **Failure Example**

```json
{
  "error": "You are not eligible to vote, your academic standing is poor",
  "criteria": [
    ...
  ]
}
```

---

# 4️⃣ **Get Positions in an Election**

### **GET** `/get_election_positions/{pk}/`

Returns all contestable positions for an election.

### **Auth Required:** ✅ Yes

### ✔️ **Sample Response**

```json
[
  { "id": 1, "name": "President" },
  { "id": 2, "name": "Vice President" },
  { "id": 3, "name": "Secretary General" }
]
```

---

# 5️⃣ **Get Eligible Levels for an Election**

### **GET** `/get_election_levels/{pk}/`

Returns the academic levels allowed to participate.

### **Auth Required:** ✅ Yes

### ✔️ **Sample Response**

```json
[
  { "id": 1, "name": "200 Level" },
  { "id": 2, "name": "300 Level" }
]
```

---

# 6️⃣ **Apply for Election Position**

### **POST** `/create_election_application/`

Allows a student to apply for a position in an election.

### **Auth Required:** ✅ Yes

---

### ✔️ **Payload**

```json
{
  "position_id": 5
}
```

---

### ✔️ **Success Response**

```json
{
  "id": 14,
  "position": "President",
  "student": {
    "id": 2034,
    "registration_number": "201812345",
    "full_name": "John Doe",
    "level": "300",
    "faculty": "Social Sciences",
    "department": "Political Science",
    "image": "https://nau-slc.s3.eu-west-2.amazonaws.com/media/student_images/201812345.png"
  },
  "status": "pending"
}
```

---

### ❌ **Error Example**

Student already applied:

```json
{
  "error": "You have already applied for this session's election"
}
```

---

# 7️⃣ **Vote for Multiple Candidates**

### **POST** `/vote_multiple_candidates/`

Allows a student to vote for **multiple positions in one request**, but **one vote per position**.

### **Auth Required:** ✅ Yes

---

### ✔️ **Payload**

```json
{
  "candidates": [12, 34, 56]
}
```

---

### ✔️ **Success Response**

```json
{
  "success": [
    {
      "id": 701,
      "voter": { ... },
      "candidate": { ... },
      "position": { "id": 1, "name": "President" },
      "session": { "id": 5, "year": "2024/2025", "current_session": true },
      "timestamp": "2025-01-20T12:21:05Z"
    }
  ],
  "failed": [
    {
      "candidate_id": 34,
      "position": "President",
      "error": "You have already voted for this position"
    }
  ]
}
```

---

### ❌ **Error Example**

```json
{
  "error": "Candidates must be a non-empty list"
}
```

---

# 8️⃣ **View My Votes**

### **GET** `/my_votes/`

Returns all votes cast by the logged-in student in the current session.

### **Auth Required:** ✅ Yes

---

### ✔️ **Sample Response**

```json
[
  {
    "id": 1,
    "candidate": "John Doe",
    "position": "President"
  },
  {
    "id": 2,
    "candidate": "Mary Okafor",
    "position": "Secretary General"
  }
]
```

---


# 🗳️ **9️⃣ View Election Results **

### **GET** `/view_election_results/`

Returns the full election result breakdown, **position by position**, including:

* Candidate info
* Total votes per candidate
* A boolean indicating whether the candidate is the **winner**

### **Auth Required:** ✅ Yes

---

## **🔍 Query Parameters**

| Param         | Required   | Description                                                                  |
| ------------- | ---------- | ---------------------------------------------------------------------------- |
| `election_id` | ❌ optional | ID of a specific election. If not provided, the latest SUG election is used. |

---

## **✔️ Sample Response (Updated)**

```json
{
  "election": "SUG Election 2024/2025",
  "positions": [
    {
      "id": 1,
      "name": "President",
      "candidates": [
        {
          "id": 14,
          "student": {
            "id": 2034,
            "registration_number": "201812345",
            "full_name": "John Doe",
            "level": "300",
            "faculty": "Social Sciences",
            "department": "Political Science",
            "image": "https://nau-slc.s3.eu-west-2.amazonaws.com/media/student_images/201812345.png"
          },
          "vote_count": 1200,
          "winner": true
        },
        {
          "id": 15,
          "student": {
            "id": 2110,
            "registration_number": "201845612",
            "full_name": "Mary Okafor",
            "level": "300",
            "faculty": "Law",
            "department": "Public Law",
            "image": "https://nau-slc.s3.eu-west-2.amazonaws.com/media/student_images/201845612.png"
          },
          "vote_count": 850,
          "winner": false
        }
      ]
    },
    {
      "id": 2,
      "name": "Vice President",
      "candidates": [
        {
          "id": 32,
          "student": {
            "id": 3001,
            "registration_number": "201733400",
            "full_name": "Kelvin James",
            "level": "400",
            "faculty": "Education",
            "department": "Educational Management",
            "image": "https://nau-slc.s3.eu-west-2.amazonaws.com/media/student_images/201733400.png"
          },
          "vote_count": 950,
          "winner": true
        }
      ]
    }
  ]
}
```

---

# 🔍 **CandidateResultSerializer — What Frontend Receives**

```json
{
  "id": 14,
  "student": {
    "id": 2034,
    "registration_number": "201812345",
    "full_name": "John Doe",
    "level": "300",
    "faculty": "Social Sciences",
    "department": "Political Science",
    "image": "https://nau-slc.s3.eu-west-2.amazonaws.com/media/student_images/201812345.png"
  },
  "vote_count": 1200,
  "winner": true
}
```

### **Field Explanation**

| Field        | Type    | Description                                     |
| ------------ | ------- | ----------------------------------------------- |
| `id`         | integer | Candidate ID                                    |
| `student`    | object  | Full student info (name, dept, image, etc.)     |
| `vote_count` | integer | Number of votes the candidate received          |
| `winner`     | boolean | `true` only if they have the highest vote count |




