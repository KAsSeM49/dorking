python
import requests
import re
import time

GitHub API URL
GITHUB_API_URL = "https://api.github.com/search/code"

Common secrets patterns
SECRET_PATTERNS = [
    r'(?i)api[\s_-]?key\s*[:=]\s*[a-zA-Z0-9_-]{32,}',        # API key pattern
    r'(?i)access[\s_-]?token\s*[:=]\s*[a-zA-Z0-9_-]{40,}',    # Access token pattern
    r'(?i)client[\s_-]?secret\s*[:=]\s*[a-zA-Z0-9_-]{32,}',   # Client secret pattern
    r'(?i)password\s*[:=]\s*[a-zA-Z0-9_-]{8,}',               # Password pattern
    r'(?i)secret\s*[:=]\s*[a-zA-Z0-9_-]{32,}',                # Generic secret pattern
]

GitHub search query for the dorking
QUERY = "secret OR key OR token OR password"

GitHub token for authentication (optional, for higher rate limits)
GITHUB_TOKEN = 'your_github_token_here'

Headers for authentication (if necessary)
HEADERS = {
    "Authorization": f"token {GITHUB_TOKEN}" if GITHUB_TOKEN else ""
}

Function to search GitHub for sensitive data
def search_github(query, page=1):
    params = {
        "q": query,
        "page": page,
        "per_page": 100  # Max results per page
    }

    response = requests.get(GITHUB_API_URL, headers=HEADERS, params=params)

    if response.status_code == 200:
        return response.json()
    else:
        print(f"Error: {response.status_code} - {response.text}")
        return None
Function to check if content matches any secret patterns
def check_for_secrets(content):
    secrets_found = []
    for pattern in SECRET_PATTERNS:
        matches = re.findall(pattern, content)
        if matches:
            secrets_found.extend(matches)
    return secrets_found

Function to search for secrets in GitHub repositories
def dork_github_for_secrets(query):
    page = 1
    while True:
        print(f"Searching page {page}...")
        results = search_github(query, page)
        if not results or 'items' not in results:
            break

        for item in results['items']:
            file_name = item['name']
            repo_name = item['repository']['full_name']
            file_url = item['html_url']
            file_content_url = item['url']

            # Get file content to check for secrets
            file_response = requests.get(file_content_url, headers=HEADERS)
            if file_response.status_code == 200:
                content = file_response.text
                secrets = check_for_secrets(content)
                if secrets:
                    print(f"Secrets found in {repo_name} -> {file_name}:")
                    print(f"URL: {file_url}")
                    print(f"Secrets: {secrets}")
                    print("-" * 80)
            else:
print(f"Failed to retrieve content for {file_url}")

        # Go to the next page
        page += 1
        time.sleep(1)  # To prevent hitting rate limits

Main function to start the search
if _name_ == "_main_":
    print("Starting GitHub dorking for secrets...")
    dork_github_for_secrets(QUERY)
