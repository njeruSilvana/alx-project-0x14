# CineSeek - Movie Discovery Application

## API Overview
The MoviesDatabase API is a comprehensive REST API that provides access to an extensive database of movies and TV shows. It offers powerful search capabilities, detailed movie information, and supports filtering by various parameters including year, genre, and title type. The API is designed for developers building movie discovery applications, recommendation systems, or entertainment platforms.

## Version
API Version: 1.0

## Available Endpoints

### 1. `/titles` - Get Movies/TV Shows
Retrieves a list of titles with support for filtering and pagination.
- **Method**: GET
- **Purpose**: Fetch movies and TV shows with optional filters

### 2. `/titles/search/{title}` - Search by Title
Search for specific titles by name.
- **Method**: GET
- **Purpose**: Find movies/shows by their title

### 3. `/titles/{id}` - Get Title Details
Retrieve detailed information about a specific title.
- **Method**: GET
- **Purpose**: Get comprehensive details for a single movie/show

## Request and Response Format

### Request Structure
```http
GET /titles?year=2024&limit=12&page=1 HTTP/1.1
Host: moviesdatabase.p.rapidapi.com
x-rapidapi-host: moviesdatabase.p.rapidapi.com
x-rapidapi-key: YOUR_API_KEY
```

**Query Parameters**:
- `year` (optional): Filter by release year
- `genre` (optional): Filter by genre (e.g., Animation, Comedy)
- `limit` (optional): Number of results per page (default: 10, max: 50)
- `page` (optional): Page number for pagination
- `sort` (optional): Sort order (e.g., year.decr, year.incr)

### Response Structure
```json
{
  "page": 1,
  "next": "/titles?page=2",
  "entries": 12,
  "results": [
    {
      "id": "tt1234567",
      "primaryImage": {
        "url": "https://image-url.jpg",
        "width": 1000,
        "height": 1500
      },
      "titleText": {
        "text": "Movie Title"
      },
      "releaseYear": {
        "year": 2024
      },
      "titleType": {
        "text": "Movie"
      }
    }
  ]
}
```

## Authentication

The API uses **API Key Authentication** via HTTP headers.

### Required Headers:
```javascript
{
  "x-rapidapi-host": "moviesdatabase.p.rapidapi.com",
  "x-rapidapi-key": "YOUR_API_KEY_HERE"
}
```

### Getting Your API Key:
1. Sign up at [RapidAPI](https://rapidapi.com/)
2. Subscribe to the MoviesDatabase API
3. Copy your API key from the dashboard
4. Store it securely in environment variables

### Security Best Practices:
- **Never** commit API keys to version control
- Store keys in `.env.local` file
- Use server-side API routes to hide keys from client
- Add `.env.local` to `.gitignore`

## Error Handling

### Common Error Responses:

**401 Unauthorized**
```json
{
  "message": "Invalid API Key"
}
```
**Solution**: Verify your API key is correct and active

**429 Too Many Requests**
```json
{
  "message": "Rate limit exceeded"
}
```
**Solution**: Implement request throttling and respect rate limits

**404 Not Found**
```json
{
  "message": "Title not found"
}
```
**Solution**: Validate the title ID before making requests

**500 Internal Server Error**
```json
{
  "message": "Server error"
}
```
**Solution**: Implement retry logic with exponential backoff

### Error Handling in Code:
```typescript
try {
  const response = await fetch(apiUrl, options);
  
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  
  const data = await response.json();
  return data;
} catch (error) {
  console.error('API Error:', error);
  // Show user-friendly error message
  return null;
}
```

## Usage Limits and Best Practices

### Rate Limits:
- **Free Tier**: 500 requests/month
- **Basic Plan**: 10,000 requests/month
- **Pro Plan**: 100,000 requests/month

### Best Practices:

1. **Implement Pagination**
   - Use `limit` parameter to control response size
   - Request only the data you need
   - Cache results to reduce API calls

2. **Cache Responses**
   - Store frequently accessed data locally
   - Implement client-side caching for static data
   - Use Next.js API routes for server-side caching

3. **Optimize Requests**
   - Batch requests when possible
   - Avoid redundant API calls
   - Implement debouncing for search functionality

4. **Error Recovery**
   - Implement exponential backoff for retries
   - Provide fallback UI for failed requests
   - Log errors for debugging

5. **Security**
   - Always use HTTPS
   - Validate and sanitize user inputs
   - Keep API keys server-side only

6. **Performance**
   - Minimize payload size with specific queries
   - Use appropriate `limit` values
   - Implement loading states for better UX

### Example Implementation:
```typescript
// Rate limiting with exponential backoff
async function fetchWithRetry(url: string, options: RequestInit, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url, options);
      if (response.ok) return response;
      
      if (response.status === 429) {
        await new Promise(resolve => setTimeout(resolve, Math.pow(2, i) * 1000));
        continue;
      }
      
      throw new Error(`HTTP ${response.status}`);
    } catch (error) {
      if (i === retries - 1) throw error;
    }
  }
}
```
