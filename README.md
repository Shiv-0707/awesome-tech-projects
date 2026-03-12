# awesome-tech-projects
Collection of the best tech projects with Python code examples, implementations, and documentation

## 📚 Featured Projects

### 1. Hello World in Python

```python
# Simple hello world program
def greet(name):
    """Print a greeting message"""
    return f"Hello, {name}!"

if __name__ == "__main__":
    print(greet("World"))
```

### 2. Data Processing with Python

```python
import json
from typing import List, Dict

class DataProcessor:
    """Process and analyze data from various sources"""
    
    def __init__(self, data: List[Dict]):
        self.data = data
    
    def filter_by_key(self, key: str, value: str) -> List[Dict]:
        """Filter data by key-value pair"""
        return [item for item in self.data if item.get(key) == value]
    
    def get_statistics(self, numeric_key: str) -> Dict:
        """Calculate statistics for numeric data"""
        values = [item[numeric_key] for item in self.data if numeric_key in item]
        if not values:
            return {}
        
        return {
            'count': len(values),
            'sum': sum(values),
            'average': sum(values) / len(values),
            'min': min(values),
            'max': max(values)
        }

# Example usage
if __name__ == "__main__":
    sample_data = [
        {'name': 'Alice', 'score': 85},
        {'name': 'Bob', 'score': 92},
        {'name': 'Charlie', 'score': 78}
    ]
    
    processor = DataProcessor(sample_data)
    stats = processor.get_statistics('score')
    print(json.dumps(stats, indent=2))
```

### 3. Web Scraping Example

```python
import requests
from bs4 import BeautifulSoup
from typing import List

class WebScraper:
    """Scrape data from websites"""
    
    def __init__(self, url: str):
        self.url = url
        self.session = requests.Session()
    
    def fetch_page(self) -> str:
        """Fetch webpage content"""
        try:
            response = self.session.get(self.url, timeout=10)
            response.raise_for_status()
            return response.text
        except requests.RequestException as e:
            print(f"Error fetching page: {e}")
            return None
    
    def extract_links(self, html_content: str) -> List[str]:
        """Extract all links from HTML"""
        soup = BeautifulSoup(html_content, 'html.parser')
        links = []
        for link in soup.find_all('a', href=True):
            links.append(link['href'])
        return links

# Example usage
if __name__ == "__main__":
    scraper = WebScraper('https://example.com')
    html = scraper.fetch_page()
    if html:
        links = scraper.extract_links(html)
        print(f"Found {len(links)} links")
```

### 4. REST API Handler

```python
import requests
from typing import Any, Dict, Optional

class APIClient:
    """Client for interacting with REST APIs"""
    
    def __init__(self, base_url: str, headers: Optional[Dict] = None):
        self.base_url = base_url
        self.headers = headers or {}
    
    def get(self, endpoint: str, params: Optional[Dict] = None) -> Dict:
        """Make GET request"""
        url = f"{self.base_url}/{endpoint}"
        try:
            response = requests.get(url, params=params, headers=self.headers)
            response.raise_for_status()
            return response.json()
        except requests.RequestException as e:
            return {'error': str(e)}
    
    def post(self, endpoint: str, data: Dict) -> Dict:
        """Make POST request"""
        url = f"{self.base_url}/{endpoint}"
        try:
            response = requests.post(url, json=data, headers=self.headers)
            response.raise_for_status()
            return response.json()
        except requests.RequestException as e:
            return {'error': str(e)}

# Example usage
if __name__ == "__main__":
    client = APIClient('https://api.example.com')
    result = client.get('users', params={'id': 1})
    print(result)
```

### 5. File Handler Utility

```python
import os
import json
from pathlib import Path
from typing import Any, Dict

class FileHandler:
    """Handle file operations"""
    
    @staticmethod
    def read_json(filepath: str) -> Dict:
        """Read JSON file"""
        try:
            with open(filepath, 'r', encoding='utf-8') as f:
                return json.load(f)
        except (FileNotFoundError, json.JSONDecodeError) as e:
            print(f"Error reading file: {e}")
            return {}
    
    @staticmethod
    def write_json(filepath: str, data: Dict) -> bool:
        """Write data to JSON file"""
        try:
            Path(filepath).parent.mkdir(parents=True, exist_ok=True)
            with open(filepath, 'w', encoding='utf-8') as f:
                json.dump(data, f, indent=2)
            return True
        except Exception as e:
            print(f"Error writing file: {e}")
            return False
    
    @staticmethod
    def list_files(directory: str, extension: str = None) -> list:
        """List files in directory"""
        files = []
        for item in Path(directory).iterdir():
            if item.is_file():
                if extension is None or item.suffix == extension:
                    files.append(str(item))
        return files

# Example usage
if __name__ == "__main__":
    data = {'name': 'Awesome Tech', 'projects': 5}
    FileHandler.write_json('data.json', data)
    result = FileHandler.read_json('data.json')
    print(result)
```

### 6. Database Manager

```python
import sqlite3
from typing import List, Tuple, Optional
from contextlib import contextmanager

class DatabaseManager:
    """Manage SQLite database operations"""
    
    def __init__(self, db_path: str):
        self.db_path = db_path
    
    @contextmanager
    def get_connection(self):
        """Context manager for database connection"""
        conn = sqlite3.connect(self.db_path)
        try:
            yield conn
        finally:
            conn.close()
    
    def create_table(self, table_name: str, schema: str) -> bool:
        """Create database table"""
        try:
            with self.get_connection() as conn:
                cursor = conn.cursor()
                cursor.execute(f"CREATE TABLE IF NOT EXISTS {table_name} ({schema})")
                conn.commit()
            return True
        except sqlite3.Error as e:
            print(f"Database error: {e}")
            return False
    
    def insert_data(self, table_name: str, data: dict) -> bool:
        """Insert data into table"""
        try:
            columns = ', '.join(data.keys())
            placeholders = ', '.join(['?' for _ in data])
            with self.get_connection() as conn:
                cursor = conn.cursor()
                cursor.execute(f"INSERT INTO {table_name} ({columns}) VALUES ({placeholders})",
                             tuple(data.values()))
                conn.commit()
            return True
        except sqlite3.Error as e:
            print(f"Database error: {e}")
            return False
    
    def query_data(self, query: str) -> List[Tuple]:
        """Execute SELECT query"""
        try:
            with self.get_connection() as conn:
                cursor = conn.cursor()
                cursor.execute(query)
                return cursor.fetchall()
        except sqlite3.Error as e:
            print(f"Database error: {e}")
            return []

# Example usage
if __name__ == "__main__":
    db = DatabaseManager('awesome.db')
    schema = 'id INTEGER PRIMARY KEY, name TEXT, description TEXT'
    db.create_table('projects', schema)
    db.insert_data('projects', {'name': 'Project 1', 'description': 'First project'})
    results = db.query_data('SELECT * FROM projects')
    print(results)
```

### 7. Utility Functions

```python
from datetime import datetime
import hashlib
from typing import List, Any

class UtilityFunctions:
    """Common utility functions"""
    
    @staticmethod
    def hash_string(text: str) -> str:
        """Generate SHA256 hash of string"""
        return hashlib.sha256(text.encode()).hexdigest()
    
    @staticmethod
    def get_timestamp() -> str:
        """Get current timestamp"""
        return datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    
    @staticmethod
    def flatten_list(nested_list: List[Any]) -> List[Any]:
        """Flatten nested list"""
        result = []
        for item in nested_list:
            if isinstance(item, list):
                result.extend(UtilityFunctions.flatten_list(item))
            else:
                result.append(item)
        return result
    
    @staticmethod
    def remove_duplicates(items: List[Any]) -> List[Any]:
        """Remove duplicates while maintaining order"""
        seen = set()
        result = []
        for item in items:
            if item not in seen:
                seen.add(item)
                result.append(item)
        return result

# Example usage
if __name__ == "__main__":
    utils = UtilityFunctions()
    text_hash = utils.hash_string("awesome-tech-projects")
    timestamp = utils.get_timestamp()
    flattened = utils.flatten_list([[1, 2], [3, [4, 5]]])
    unique_items = utils.remove_duplicates([1, 2, 2, 3, 3, 3])
    
    print(f"Hash: {text_hash}")
    print(f"Timestamp: {timestamp}")
    print(f"Flattened: {flattened}")
    print(f"Unique: {unique_items}")
```

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/Shiv-0707/awesome-tech-projects.git

# Navigate to project directory
cd awesome-tech-projects

# Install dependencies
pip install -r requirements.txt

# Run Python scripts
python script_name.py
```

## 📋 Requirements

```
requests>=2.28.0
beautifulsoup4>=4.11.0
```

## 📝 License

MIT License - Feel free to use these projects for learning and development.

## 👨‍💻 Author

**Shiv Pratap Singh** - [@Shiv-0707](https://github.com/Shiv-0707)

---

*Last Updated: March 13, 2026*
