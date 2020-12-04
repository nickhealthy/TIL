# Today I Learned

* Python JSON 모듈로 JSON 파일 생성

---



## Python JSON 모듈로 JSON 파일 생성

```python
import json

file_path = "./sample.json"

data = {}
data['posts'] = []
data['posts'].append({
    "title": "How to get stroage size",
    "url": "https://codechacha.com/ko/get-free-and-total-size-of-volumes-in-android/",
    "draft": "false"
})
data['posts'].append({
    "title": "Android Q, Scoped Storage",
    "url": "https://codechacha.com/ko/android-q-scoped-storage/",
    "draft": "false"
})
print(data)

with open(file_path, 'w') as outfile:
    json.dump(data, outfile, indent=4, ensure_ascii = False)
```

* `json.dump()`사용시  파라미터로 `ensure_ascii = False` 설정해줘야 한글이 안깨짐