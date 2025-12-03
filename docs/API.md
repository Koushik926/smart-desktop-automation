# Smart Desktop Automation - API Reference

## Overview

Smart Desktop Automation provides a comprehensive REST API and SDK for integrating automation capabilities into your applications. Built with Kiro, the API enables seamless automation of desktop tasks.

## Authentication

All API requests must include an authentication token:

```bash
Authorization: Bearer YOUR_API_TOKEN
Content-Type: application/json
```

## Base URL

```
https://api.desktop-automation.kiro.ai/v1
```

## Core Endpoints

### 1. File Organization

#### Organize Files
```http
POST /automations/file-organize
Content-Type: application/json

{
  "path": "/path/to/directory",
  "recursive": true,
  "dry_run": false,
  "strategy": "content_based",
  "create_backups": true
}
```

**Response:**
```json
{
  "task_id": "task_12345",
  "status": "processing",
  "files_processed": 150,
  "files_moved": 145,
  "errors": 0,
  "duration_ms": 5234
}
```

### 2. Batch Renaming

#### Rename Files
```http
POST /automations/batch-rename

{
  "path": "/path/to/directory",
  "pattern": "${category}_${date}_${index}",
  "recursive": true,
  "preview": true
}
```

**Response:**
```json
{
  "previews": [
    {
      "old_name": "old_file.txt",
      "new_name": "document_2025-12-03_001.txt"
    }
  ],
  "total_files": 50,
  "ready_to_apply": true
}
```

### 3. System Cleanup

#### Cleanup System
```http
POST /automations/cleanup

{
  "targets": ["temp", "cache", "logs"],
  "dry_run": true,
  "backup": true
}
```

**Response:**
```json
{
  "task_id": "task_67890",
  "space_freed_mb": 2500,
  "files_deleted": 1235,
  "status": "completed"
}
```

### 4. Task Management

#### Get Task Status
```http
GET /tasks/{task_id}
```

**Response:**
```json
{
  "task_id": "task_12345",
  "status": "completed",
  "progress_percent": 100,
  "start_time": "2025-12-03T15:30:00Z",
  "end_time": "2025-12-03T15:35:30Z",
  "result": {
    "success": true,
    "items_processed": 150,
    "items_failed": 0
  }
}
```

#### Cancel Task
```http
DELETE /tasks/{task_id}
```

## Workflows API

### Execute Workflow
```http
POST /workflows/execute

{
  "workflow_name": "file-organization",
  "parameters": {
    "path": "/path/to/directory",
    "strategy": "intelligent"
  }
}
```

### List Available Workflows
```http
GET /workflows
```

**Response:**
```json
{
  "workflows": [
    {
      "name": "file-organization",
      "version": "1.0.0",
      "description": "Intelligent file organization"
    },
    {
      "name": "batch-renaming",
      "version": "1.0.0",
      "description": "Batch file renaming"
    }
  ]
}
```

## Error Handling

### Error Response Format
```json
{
  "error": {
    "code": "INVALID_PATH",
    "message": "Provided path does not exist",
    "details": "Path '/nonexistent' not found",
    "timestamp": "2025-12-03T15:35:00Z"
  }
}
```

### Common Error Codes

| Code | Status | Description |
|------|--------|-------------|
| INVALID_PATH | 400 | Provided path is invalid |
| PERMISSION_DENIED | 403 | Insufficient permissions |
| FILE_NOT_FOUND | 404 | Specified file not found |
| TASK_NOT_FOUND | 404 | Task ID not found |
| RATE_LIMIT | 429 | API rate limit exceeded |
| SERVER_ERROR | 500 | Internal server error |

## Python SDK Example

```python
from kiro_automation import DesktopAutomation

# Initialize client
client = DesktopAutomation(
    api_token="YOUR_TOKEN",
    base_url="https://api.desktop-automation.kiro.ai/v1"
)

# Organize files
result = client.file_organize(
    path="/path/to/directory",
    strategy="content_based",
    dry_run=False
)

print(f"Files processed: {result['files_processed']}")
print(f"Files moved: {result['files_moved']}")

# Monitor task
status = client.get_task_status(result['task_id'])
while status['status'] != 'completed':
    print(f"Progress: {status['progress_percent']}%")
    status = client.get_task_status(result['task_id'])
```

## JavaScript SDK Example

```javascript
import DesktopAutomation from 'kiro-automation';

const client = new DesktopAutomation({
  apiToken: 'YOUR_TOKEN',
  baseUrl: 'https://api.desktop-automation.kiro.ai/v1'
});

// Organize files
const result = await client.fileOrganize({
  path: '/path/to/directory',
  strategy: 'content_based',
  dryRun: false
});

console.log(`Task ID: ${result.task_id}`);
```

## Rate Limiting

- **Rate Limit**: 1000 requests per hour
- **Burst Limit**: 100 requests per minute
- **Headers**:
  - `X-RateLimit-Limit`: Total requests allowed
  - `X-RateLimit-Remaining`: Remaining requests
  - `X-RateLimit-Reset`: Reset timestamp

## Webhooks

Subscribe to task completion events:

```http
POST /webhooks/subscribe

{
  "event": "task.completed",
  "url": "https://your-api.com/webhook",
  "active": true
}
```

**Webhook Payload:**
```json
{
  "event": "task.completed",
  "timestamp": "2025-12-03T15:35:00Z",
  "task_id": "task_12345",
  "result": {
    "success": true,
    "summary": "150 files processed"
  }
}
```

## Versioning

API versions follow semantic versioning. Current version: **v1**

## Support

For API support:
- Email: api-support@desktop-automation.kiro.ai
- Documentation: https://docs.desktop-automation.kiro.ai
- Status Page: https://status.desktop-automation.kiro.ai
