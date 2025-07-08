
   mkdir file-server-project
   cd file-server-project
   ```

2. **Create virtual environment**
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables**
   Create a `.env` file with:
   ```
   GOOGLE_CLOUD_PROJECT_ID=your-project-id
   GOOGLE_CLOUD_BUCKET_NAME=your-bucket-name
   GOOGLE_APPLICATION_CREDENTIALS=./fileserver-credentials.json
   PORT=8080
   NODE_ENV=development
   ```

5. **Set up Google Cloud Storage**
   - Create bucket with required directory structure
   - Place credentials JSON file in project root

## 🔧 Configuration

### Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `GOOGLE_CLOUD_PROJECT_ID` | Your GCP project ID | `file-server-garcia-juan` |
| `GOOGLE_CLOUD_BUCKET_NAME` | Storage bucket name | `fileserver-garcia-jg` |
| `GOOGLE_APPLICATION_CREDENTIALS` | Path to credentials file | `./fileserver-credentials.json` |
| `PORT` | Server port | `8080` |
| `NODE_ENV` | Environment mode | `development` |

### API Keys

The system uses simple API key authentication. Default keys:

| API Key | Permissions | Description |
|---------|-------------|-------------|
| `test-key-123` | read, write, delete | Full access user |
| `read-only-456` | read | Read-only user |

> **Note**: In production, store API keys in a secure database with hashing.

## 🚦 Usage

### Starting the Server

```bash
python app.py
```

The server will start on `http://localhost:8080`

### API Endpoints

#### Authentication
All API endpoints (except `/` and `/health`) require authentication via the `X-API-Key` header.

#### Available Endpoints

| Method | Endpoint | Description | Permissions Required |
|--------|----------|-------------|---------------------|
| GET | `/` | API information | None |
| GET | `/health` | Health check | None |
| POST | `/api/files/upload` | Upload file | write |
| GET | `/api/files` | List files | read |
| GET | `/api/files/` | Download file | read |
| DELETE | `/api/files/` | Delete file | delete |

### Examples

#### Upload File
```bash
curl -H "X-API-Key: test-key-123" \
     -X POST \
     -F "file=@example.txt" \
     http://localhost:8080/api/files/upload
```

#### List Files
```bash
curl -H "X-API-Key: test-key-123" \
     http://localhost:8080/api/files
```

#### Download File
```bash
curl -H "X-API-Key: test-key-123" \
     -O -J \
     http://localhost:8080/api/files/file-id-here
```

#### Delete File
```bash
curl -H "X-API-Key: test-key-123" \
     -X DELETE \
     http://localhost:8080/api/files/file-id-here
```

## 🔒 Security Features

### Rate Limiting
- **Global**: 100 requests per hour per IP
- **Upload**: 10 requests per minute
- **Download**: 20 requests per minute
- **Delete**: 5 requests per minute

### File Security
- **Type Validation**: Only allowed file extensions
- **Size Limit**: 16MB maximum file size
- **Secure Filenames**: Sanitized using Werkzeug
- **Unique IDs**: UUID4 for each uploaded file

### Access Control
- API key authentication required
- Permission-based authorization (read/write/delete)
- Proper HTTP status codes (401, 403, 404)

## 📁 Project Structure

```
file-server-project/
├── app.py                 # Main application file
├── requirements.txt       # Python dependencies
├── .env                  # Environment variables
├── README.md             # This documentation
├── fileserver-credentials.json  # GCP credentials
├── app/                  # Application modules (future)
├── templates/            # HTML templates (future)
├── static/              # Static files (future)
├── tests/               # Test files (future)
└── venv/                # Virtual environment
```

## 🧪 Testing

### Basic Health Check
```bash
curl http://localhost:8080/health
```

### Authentication Test
```bash
# Should fail (401)
curl http://localhost:8080/api/files

# Should succeed
curl -H "X-API-Key: test-key-123" http://localhost:8080/api/files
```

## 📊 API Response Examples

### Successful Upload
```json
{
  "message": "File uploaded successfully",
  "file": {
    "file_id": "550e8400-e29b-41d4-a716-446655440000",
    "original_name": "example.txt",
    "size": 1024,
    "content_type": "text/plain",
    "upload_date": "2025-07-03T12:00:00.000000"
  }
}
```

### File List Response
```json
{
  "files": [
    {
      "file_id": "550e8400-e29b-41d4-a716-446655440000",
      "original_name": "example.txt",
      "size": 1024,
      "content_type": "text/plain",
      "upload_date": "2025-07-03T12:00:00.000000",
      "download_url": "/api/files/550e8400-e29b-41d4-a716-446655440000"
    }
  ],
  "pagination": {
    "page": 1,
    "per_page": 10,
    "total": 1,
    "pages": 1
  }
}
```

### Error Response
```json
{
  "error": "Invalid or missing API key"
}
```

## 🚨 Troubleshooting

### Common Issues

1. **Authentication Errors**
   - Ensure X-API-Key header is included
   - Verify API key is valid
   - Check user permissions for the operation

2. **File Upload Issues**
   - Check file size (max 16MB)
   - Verify file type is allowed
   - Ensure sufficient permissions

3. **Google Cloud Errors**
   - Verify credentials file exists
   - Check bucket name and permissions
   - Ensure Storage API is enabled

4. **Rate Limiting**
   - Wait for rate limit reset
   - Implement exponential backoff
   - Check rate limit headers

## 📈 Future Enhancements

- [ ] Database integration for API key management
- [ ] JWT token authentication
- [ ] File versioning system
- [ ] Batch file operations
- [ ] File metadata search
- [ ] WebSocket support for real-time updates
- [ ] Docker containerization
- [ ] Automated testing suite
- [ ] API documentation with Swagger
- [ ] Monitoring and logging

## 👥 Contributing

1. Fork the project
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

## 📄 License

This project is part of the Data Engineering course at Universidad Politécnica de Yucatán.

## 👤 Author

**M. Sc. Jorge J. Pedrozo Romero**  
*Data Engineering Course*  
Universidad Politécnica de Yucatán

---

**Course Information:**
- Subject: Server Architecture
- Module: Google Cloud Configuration
- Semester: 5th Quadrimester
- Period: May - August 2025
