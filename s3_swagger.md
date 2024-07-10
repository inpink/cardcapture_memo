다음은 위의 Spring Boot 컨트롤러에 대한 Swagger 문서를 한국어로 마크다운 형식으로 작성한 것입니다.

```markdown
# S3 컨트롤러 API 문서

## 개요
S3 컨트롤러는 Amazon S3에서 파일을 업로드, 삭제, 업데이트 및 조회하기 위한 API를 제공합니다. 각 엔드포인트는 특정 방식으로 S3와 상호 작용하여 다양한 파일 작업을 수행할 수 있도록 설계되었습니다.

## 엔드포인트

### 1. 파일 업로드
#### 엔드포인트
```
POST /s3/upload
```

#### 설명
S3에 파일을 업로드합니다. 파일 URL 형식은 `https://{aws s3 버킷 도메인}/{디렉토리 이름}/{파일 이름}`입니다. 요청 본문에 `dirName`과 `file`을 제공하여 파일을 업로드할 수 있습니다.

#### 파라미터
- `file` (MultipartFile): 업로드할 파일.
- `dirName` (String): 파일을 업로드할 디렉토리 이름.

#### 응답
- `200 OK`: 업로드된 파일의 URL을 반환합니다.
- `500 Internal Server Error`: 업로드 실패 시 오류 메시지를 반환합니다.

#### 요청 예시
```
curl -X POST "http://localhost:8080/s3/upload" -F "file=@path/to/your/file.png" -F "dirName=test"
```

#### 응답 예시
```json
{
  "url": "https://your-s3-bucket.s3.amazonaws.com/test/uuid_filename.png"
}
```

### 2. 프리사인 URL 발급
#### 엔드포인트
```
POST /s3/generate-presigned-url
```

#### 설명
S3에 파일을 업로드할 수 있는 프리사인 URL을 발급합니다. 이 URL을 사용하여 HTTP PUT 요청으로 파일을 직접 S3에 업로드할 수 있습니다. 파일 확장자(png 등)를 지정해야 합니다.

#### 파라미터
- `dirName` (String): 파일을 업로드할 디렉토리 이름.
- `fileName` (String): 파일의 기본 이름.
- `extension` (String): 파일 확장자 (예: png).

#### 응답
- `200 OK`: 파일 업로드용 프리사인 URL을 반환합니다.
- `500 Internal Server Error`: URL 생성 실패 시 오류 메시지를 반환합니다.

#### 요청 예시
```json
{
  "dirName": "test",
  "fileName": "testFile",
  "extension": "png"
}
```

#### 응답 예시
```json
{
  "url": "https://your-s3-bucket.s3.amazonaws.com/test/uuid_testFile.png?AWSAccessKeyId=AKIAIOSFODNN7EXAMPLE&Expires=1609459200&Signature=EXAMPLESIGNATURE"
}
```

### 3. 파일 삭제
#### 엔드포인트
```
DELETE /s3/delete
```

#### 설명
S3에서 파일을 삭제합니다. 삭제할 파일의 전체 URL을 제공합니다.

#### 파라미터
- `fileUrl` (String): 삭제할 파일의 전체 URL.

#### 응답
- `200 OK`: 파일이 삭제되었음을 확인하는 메시지를 반환합니다.
- `500 Internal Server Error`: 삭제 실패 시 오류 메시지를 반환합니다.

#### 요청 예시
```
curl -X DELETE "http://localhost:8080/s3/delete?fileUrl=https://your-s3-bucket.s3.amazonaws.com/test/uuid_testFile.png"
```

#### 응답 예시
```json
{
  "message": "파일이 삭제되었습니다."
}
```

### 4. 파일 업데이트
#### 엔드포인트
```
POST /s3/update
```

#### 설명
S3에서 기존 파일을 삭제하고 새 파일을 업로드합니다. 기존 파일의 전체 URL과 새 파일, 디렉토리 이름을 제공합니다.

#### 파라미터
- `newFile` (MultipartFile): 업로드할 새 파일.
- `fileUrl` (String): 삭제할 기존 파일의 전체 URL.
- `dirName` (String): 새 파일을 업로드할 디렉토리 이름.

#### 응답
- `200 OK`: 업데이트된 파일의 URL을 반환합니다.
- `500 Internal Server Error`: 업데이트 실패 시 오류 메시지를 반환합니다.

#### 요청 예시
```
curl -X POST "http://localhost:8080/s3/update" -F "newFile=@path/to/your/newFile.png" -F "fileUrl=https://your-s3-bucket.s3.amazonaws.com/test/uuid_oldFile.png" -F "dirName=test"
```

#### 응답 예시
```json
{
  "url": "https://your-s3-bucket.s3.amazonaws.com/test/uuid_newFile.png"
}
```

### 5. 파일 URL 조회
#### 엔드포인트
```
GET /s3/view
```

#### 설명
S3에서 파일의 URL을 조회합니다. 파일이 존재한다면 파일의 경로를 반환합니다.

#### 파라미터
- `fileUrl` (String): 조회할 파일의 전체 URL.

#### 응답
- `200 OK`: 파일의 URL을 반환합니다.
- `500 Internal Server Error`: 조회 실패 시 오류 메시지를 반환합니다.

#### 요청 예시
```
curl -X GET "http://localhost:8080/s3/view?fileUrl=https://your-s3-bucket.s3.amazonaws.com/test/uuid_testFile.png"
```

#### 응답 예시
```json
{
  "url": "https://your-s3-bucket.s3.amazonaws.com/test/uuid_testFile.png"
}
```

## 참고 사항
- AWS S3 버킷 정책 및 IAM 역할이 이러한 작업을 허용하도록 올바르게 구성되었는지 확인하십시오.
- `generate-presigned-url` 엔드포인트는 AWS 자격 증명을 노출하지 않고 파일을 S3에 안전하게 업로드할 수 있도록 도와줍니다.

## 오류 처리
각 엔드포인트는 작업 실패 시 적절한 오류 메시지와 함께 `500 Internal Server Error` 응답을 반환합니다. 자세한 오류 로그는 문제 해결을 위해 사용할 수 있습니다.
```

이 문서는 각 엔드포인트에 대한 설명, 파라미터, 요청 및 응답 예시를 제공하여 API를 사용하는 개발자들이 쉽게 이해하고 활용할 수 있도록 합니다.
