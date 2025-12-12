# Laravel API - Image to Prompt Generator

A RESTful API built with Laravel 12 that provides authentication, post management, and AI-powered image-to-prompt generation using OpenAI's Vision API.

## Features

- **Authentication System** - Complete auth with Sanctum (register, login, password reset)
- **Post Management** - Full CRUD operations for posts with author relationships
- **AI Image Analysis** - Generate descriptive prompts from images using OpenAI GPT-4 Vision
- **API Documentation** - Auto-generated with Scramble
- **Comprehensive Testing** - Pest PHP test suite included
- **Security** - Rate limiting, CORS, validation, and authorization

## Tech Stack

- **Framework**: Laravel 12.x
- **PHP**: ^8.3
- **Database**: supports MySQL
- **Authentication**: Laravel Sanctum
- **AI Integration**: OpenAI PHP Client
- **API Documentation**: Scramble
- **Testing**: Pest PHP
- **Queue System**: Database queue driver

## Requirements

- PHP 8.3 or higher
- Composer
- MySQL
- OpenAI API Key

## Installation

1. **Clone the repository**

```bash
git clone <repository-url>
cd <project-directory>
```

2. **Install dependencies**

```bash
composer install
```

3. **Environment setup**

```bash
cp .env.example .env
php artisan key:generate
```

4. **Configure environment variables**

Edit `.env` file and add your OpenAI API key:

```env
APP_NAME="Laravel API"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=sqlite

OPENAI_API_KEY=your_openai_api_key_here
```

5. **Create database and run migrations**

```bash
touch database/database.sqlite
php artisan migrate
```

6. **Create storage symlink**

```bash
php artisan storage:link
```

7. **Seed the database (optional)**

```bash
php artisan db:seed
```

## Running the Application

### Development Server

```bash
php artisan serve
```

The API will be available at `http://localhost:8000`

### With Queue Worker (for async jobs)

```bash
php artisan queue:work
```

### Development with all services

```bash
composer dev
```

This runs:

- Laravel server
- Queue listener
- Log viewer (Pail)
- Vite dev server

## API Documentation

Once the application is running, visit:

```
http://localhost:8000/docs/api
```

Documentation is auto-generated using Scramble and includes all available endpoints.

## API Endpoints

### Authentication

- `POST /api/register` - Register new user
- `POST /api/login` - Login user
- `POST /api/logout` - Logout user
- `POST /api/forgot-password` - Request password reset
- `POST /api/reset-password` - Reset password

### User

- `GET /api/user` - Get authenticated user details

### Posts (Authenticated)

- `GET /api/v1/posts` - List all posts (paginated)
- `POST /api/v1/posts` - Create new post
- `GET /api/v1/posts/{id}` - Get specific post
- `PUT /api/v1/posts/{id}` - Update post
- `DELETE /api/v1/posts/{id}` - Delete post

### Image-to-Prompt Generation (Authenticated)

- `GET /api/v1/prompt-generations` - List all generations (with search & sort)
- `POST /api/v1/prompt-generations` - Generate prompt from image

## Usage Examples

### Register a User

```bash
curl -X POST http://localhost:8000/api/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123",
    "password_confirmation": "password123"
  }'
```

### Login

```bash
curl -X POST http://localhost:8000/api/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'
```

Response:

```json
{
    "user": {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com"
    },
    "token": "1|xxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

### Create a Post

```bash
curl -X POST http://localhost:8000/api/v1/posts \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "My First Post",
    "body": "This is the content of my post."
  }'
```

### Generate Prompt from Image

```bash
curl -X POST http://localhost:8000/api/v1/prompt-generations \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "image=@/path/to/your/image.jpg"
```

Response:

```json
{
    "data": {
        "id": 1,
        "image_url": "http://localhost:8000/storage/uploads/images/...",
        "generated_prompt": "A detailed description of the image...",
        "original_filename": "image.jpg",
        "file_size": 245678,
        "mime_type": "image/jpeg",
        "created_at": "2025-12-12 10:30:00",
        "updated_at": "2025-12-12 10:30:00"
    }
}
```

### List Prompt Generations with Search

```bash
# Search by prompt content
curl -X GET "http://localhost:8000/api/v1/prompt-generations?search=landscape" \
  -H "Authorization: Bearer YOUR_TOKEN"

# Sort by creation date (descending)
curl -X GET "http://localhost:8000/api/v1/prompt-generations?sort=-created_at" \
  -H "Authorization: Bearer YOUR_TOKEN"

# Combine search and sort
curl -X GET "http://localhost:8000/api/v1/prompt-generations?search=portrait&sort=-file_size&per_page=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Testing

Run the test suite:

```bash
php artisan test
```

Or with Pest directly:

```bash
./vendor/bin/pest
```

Run specific test:

```bash
php artisan test --filter=LoginApiTest
```

## Project Structure

```
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Api/V1/
│   │   │   │   ├── PostController.php
│   │   │   │   └── PromptGenerationController.php
│   │   │   └── Auth/
│   │   ├── Requests/
│   │   └── Resources/
│   ├── Models/
│   │   ├── User.php
│   │   ├── Post.php
│   │   └── ImageGeneration.php
│   └── Services/
│       └── OpenAiService.php
├── config/
├── database/
│   ├── migrations/
│   └── seeders/
├── routes/
│   ├── api.php
│   ├── auth.php
│   └── web.php
└── tests/
    ├── Feature/
    └── Unit/
```

## Image Upload Specifications

- **Supported formats**: JPEG, JPG, PNG, GIF, SVG
- **Max file size**: 10MB
- **Min dimensions**: 100x100 pixels
- **Max dimensions**: 10000x10000 pixels

## Security Features

- API rate limiting (60 requests/minute per user/IP)
- Login rate limiting (5 attempts per minute)
- CORS enabled for all origins (configurable)
- Token-based authentication with Sanctum
- Authorization checks on all protected routes
- File validation and sanitization

## Configuration

Key configuration files:

- `config/services.php` - OpenAI API configuration
- `config/sanctum.php` - Authentication settings
- `config/filesystems.php` - Storage configuration
- `config/cors.php` - CORS settings

## Error Handling

The API returns standard HTTP status codes:

- `200` - Success
- `201` - Created
- `204` - No Content (delete successful)
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `422` - Validation Error
- `429` - Too Many Requests
- `500` - Server Error
