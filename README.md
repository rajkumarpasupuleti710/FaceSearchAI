# FaceSearchAI API - README

## Overview

Face Search AI Face Search AI is an advanced facial recognition platform that allows users to search for images based on facial attributes. This innovative platform provides an efficient way to find images across the web, leveraging cutting-edge AI technology. Face Search AI is dedicated to maintaining user privacy and security, ensuring all data is handled responsibly.Face Search AI aims to provide a seamless and intuitive experience for users and developers alike. With a strong focus on security, privacy, and efficiency, Face Search AI continues to push the boundaries of what's possible in the world of facial recognition technology.
---

## Requirements

- Python 3
- `requests` library

---


## Developer Section

## API Endpoints

### 1. **Search API**

- **Endpoint**: `https://facesearchai.com/api/search`
- **Method**: `POST`
- **Headers**:
  - `api-key`: Your API key (contact `contact@facesearchai.com` to obtain one)
  - `accept`: `application/json`
- **Payload**:
  - File upload (accepted formats: jpg, jpeg, png, webp)
  - File size limit: 10 MB

**Example Code**:

```python
import requests

url = "https://facesearchai.com/api/search"
file_path = "path/to/your/image.jpg"

files = [
    ('file', (file_path, open(file_path, 'rb'), 'application/octet-stream'))
]

headers = {
    'api-key': 'your_api_key_here',
    'accept': 'application/json'
}

response = requests.post(url, headers=headers, files=files)

if response.status_code == 200:
    print(response.json())
else:
    print(f"Error: {response.status_code}")
```

**Sample Response**:

```json
{
    "UST_time": 1735498671,
    "results": [
        {
            "adultContent": false,
            "sourceUrl": "https://example.com/article1",
            "imageUrl": "https://example.com/image1.png"
        },
        {
            "adultContent": false,
            "sourceUrl": "https://example.com/article2",
            "imageUrl": "https://example.com/image2.jpg"
        }
    ]
}
```

---

### 2. **Profile Info API**

- **Endpoint**: `https://facesearchai.com/api/info`
- **Method**: `GET`
- **Headers**:
  - `api-key`: Your API key
- **Description**: Fetch account information like daily/ monthly credits and subscription date.

**Example Code**:

```python
import requests

url = "https://facesearchai.com/api/info"

headers = {
    'api-key': 'your_api_key_here'
}

response = requests.get(url, headers=headers)

if response.status_code == 200:
    print(response.json())
else:
    print(f"Error: {response.status_code}")
```

**Sample Response**:

```json
{
    "email": "developer@domain.com",
    "monthly_credits": 20000,
    "daily_credits": 4000,
    "daily_searches_used": 1000,
    "monthly_searches_used": 10000,
    "subscribe_date": "2024-12-29T12:42:47.104536Z"
}
```

---

## Error Handling

- **401 Unauthorized**: Check your API key.
- **413 Payload Too Large**: Ensure the uploaded file is under 10 MB.
- **415 Unsupported Media Type**: Verify file format (jpg, jpeg, png, webp).
- **429 Too Many Requests**: Check your daily or monthly credit usage.

---

## Contact

For support or API key requests, email: `contact@facesearchai.com`

---

## License

This API is proprietary. Unauthorized use is prohibited.




##✨ Key Features



# Face Search AI

## 🤖 Advanced Facial Recognition Technology

High-accuracy facial recognition and matching

Real-time image processing

Multi-format image support (jpg, jpeg, png, webp)

API key-based secure authentication




## 🧠 Contextual Understanding & Memory

Tracks user search history and preferences

Cross-session context retention

Provides personalized search results

Official Website: https://www.facesearchai.com/



## 🔒 Security & Compliance

API key authentication for secure access

Regular security audits to ensure data protection

Data encryption and anonymization



## 🌐 Webs

Official Website: https://www.facesearchai.com
Twitter: https://x.com/facesearchai
Facebook: https://www.facebook.com/people/Face-Search-AI/61567500925978/
Instagram: https://www.instagram.com/facesearch1/
LinkedIn: https://www.linkedin.com/company/facesearch-ai/

Support Email: support@facesearch.com


