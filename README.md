# Face Search AI

![Face Search AI Logo](https://www.facesearchai.com/FaceSeachLogo.svg)



## About Face Search AI

Face Search AI is a cutting-edge platform that enables users to find images of individuals, identify their sources, and extract detailed information such as names and descriptions. It offers tools to enhance privacy and protect personal images.


### Supported Platforms

- Telegram Bot  
- iOS and Android Apps  
- Web Application  

### Features

- **Image-Based Search**: Upload an image to find related images and their sources.  
- **Information Extraction**: Retrieve details like the name and description of the person in the image.  
- **Image Protection**: File DMCA takedown requests for unauthorized use of your images.  

Face Search AI combines innovation and privacy protection, shaping the future of face search technology.

![Face Search AI](https://cdn.dribbble.com/users/1116528/screenshots/6783134/_3)

---

## For Developers

FaceSearchAI offers a powerful API for integrating image-based search and privacy protection tools into your applications.

---

### API Documentation

#### Requirements

- Python 3  
- `requests` library  

---

### API Endpoints

#### 1. **Search API**

Use the Search API to upload an image and retrieve related images along with their sources.

- **Endpoint**: `https://facesearchai.com/api/search`  
- **Method**: `POST`  
- **Headers**:  
  - `api-key`: Your API key (contact `contact@facesearchai.com` to obtain one).  
  - `accept`: `application/json`  
- **Payload**:  
  - Upload a file (formats: jpg, jpeg, png, webp).  
  - File size limit: 10 MB.  

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

#### 2. **Profile Info API**

Use the Profile Info API to retrieve details about your account, such as available credits and subscription information.

- **Endpoint**: `https://facesearchai.com/api/info`  
- **Method**: `GET`  
- **Headers**:  
  - `api-key`: Your API key.  

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

### Error Handling

- **401 Unauthorized**: Verify your API key.  
- **413 Payload Too Large**: Ensure the file size is below 10 MB.  
- **415 Unsupported Media Type**: Use only supported file formats (jpg, jpeg, png, webp).  
- **429 Too Many Requests**: Monitor your daily or monthly credit usage.  

---



Face Search AI empowers developers and users with innovative image search and privacy protection tools. Integrate today and revolutionize the way you manage and protect your images.   


## ✨ Key Features



# Face Search AI

## 🤖 Advanced Facial Recognition Technology

High-accuracy facial recognition and matching

Real-time image processing

Multi-format image support (jpg, jpeg, png, webp)

API key-based secure authentication



## 🧠 Adaptive Search

Tracks user search history.

Provides search results



## 🔒 Security & Compliance

API key authentication for secure access

Data encryption and anonymization

---

## 🌐 Webs

Official Website: https://www.facesearchai.com

Twitter: https://x.com/facesearchai

Facebook: https://www.facebook.com/people/Face-Search-AI/61567500925978/

Instagram: https://www.instagram.com/facesearch1/

LinkedIn: https://www.linkedin.com/company/facesearch-ai/

Support Email: support@facesearch.com


### Contact

For support or API key requests, email: `contact@facesearchai.com`.  

---

### License

This API is proprietary. Unauthorized use is strictly prohibited.  

--- 



