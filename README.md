# LinkedIn Profile Scraper

## Overview
This Python script uses Selenium and BeautifulSoup to automate the process of extracting LinkedIn profile data based on search inputs (first name and last name). The extracted information includes:
- Profile Name
- URL
- Headline
- Location
- About Section
- Education Details

The script saves the scraped data into a CSV file for further analysis.

## Prerequisites
Before running the script, ensure the following dependencies and tools are installed:

### Requirements:
1. **Python**: Version 3.6 or above.
2. **Google Chrome**: Ensure the latest version is installed.
3. **ChromeDriver**: Match the version with your installed Chrome browser.
4. **Python Libraries**:
   - `selenium`
   - `bs4` (BeautifulSoup)
   - `dotenv`
   - `csv`
   - `os`

Install the required Python packages using:
```sh
pip install selenium beautifulsoup4 python-dotenv
```

5. **LinkedIn Account**: A LinkedIn account with login credentials is required.

## Environment Setup
### 1. Environment Variables
- Create a `.env` file in the project directory.
- Add your LinkedIn credentials to the file:

```sh
EMAIL=your_email_here
PASSWORD=your_password_here
```

### 2. ChromeDriver
- Download the correct version of ChromeDriver from [here](https://sites.google.com/chromium.org/driver/).
- Add the downloaded `chromedriver` to your system PATH or specify its path in the script.

## Script Workflow
### 1. Initializing the WebDriver
- The script initializes the Selenium WebDriver to automate Chrome.
- The LinkedIn login page is loaded, and credentials are input using environment variables.

### 2. Search for Profiles
- The user is prompted to input the first and last names.
- A LinkedIn search URL is constructed dynamically using the input values.
- The script navigates to the search results page.

### 3. Extract Profile Data
For each profile in the search results:
- The script clicks on the profile link and extracts the following information using BeautifulSoup:
  - Name
  - Profile URL
  - Headline
  - Location
  - About Section
  - Education Details (college, degree, and duration)
- Data is stored in a dictionary and appended to a list of profiles.

### 4. Save to CSV
- All collected profile data is saved into a CSV file (`linkedin_profiles.csv`).
- The education details are flattened into a single string for each profile.

## Code Highlights
### Login Automation
```python
# Open LinkedIn Login Page
driver.get('https://www.linkedin.com/login')
sleep(2)
# Login to LinkedIn
email_field = driver.find_element(By.ID, 'username')
email_field.send_keys(EMAIL)
password_field = driver.find_element(By.ID, 'password')
password_field.send_keys(PASSWORD)
password_field.submit()
sleep(3)
```

### Profile Data Extraction
```python
for list in range(1, 6):
    profile_data = {}
    try:
        profile_link = driver.find_element(
            By.XPATH, f"//ul/li[{list}]//a[contains(@href, '/in/')]")
        profile_link.click()
        sleep(3)
        
        # Parse profile details using BeautifulSoup
        page_source = driver.page_source
        soup = BeautifulSoup(page_source, 'lxml')
        
        # Extract name
        name = soup.find('h1', {'class': 'inline t-24 ...'})
        profile_data['name'] = name.get_text().strip() if name else 'N/A'
        
        # Extract profile URL
        url_element = soup.find('a', {'class': 'ember-view ...'})
        profile_data['url'] = url_element['href'] if url_element else 'N/A'
        
        # Extract other details (headline, location, etc.)
        ...
        
        all_profiles.append(profile_data)
    except Exception as e:
        print(f"Error occurred: {e}")
        driver.back()
        sleep(3)
```

### Save Data to CSV
```python
with open("linkedin_profiles.csv", mode="w", newline="", encoding="utf-8") as file:
    writer = csv.DictWriter(file, fieldnames=['name', 'url', 'headline', 'location', 'about', 'education'])
    writer.writeheader()
    
    for profile in all_profiles:
        profile['education'] = '; '.join(
            [f"{edu['college']} ({edu['degree']} - {edu['duration']})" for edu in profile['education']]
        )
        writer.writerow(profile)
```

## Error Handling
### 1. `NoSuchElementException`
Occurs if an element is not found on the page.
```python
try:
    element = driver.find_element(By.XPATH, "...path...")
except NoSuchElementException:
    print("Element not found.")
```

### 2. General Exception
Catches unexpected errors and logs them.
```python
except Exception as e:
    print(f"Error occurred: {e}")
```

## Best Practices
1. **Respect LinkedIn's Terms of Service**: Use the script responsibly and avoid excessive requests.
2. **Rate Limiting**: Add delays (`sleep`) between requests to prevent IP bans.
3. **Error Logging**: Log errors to a file for debugging.
4. **Update XPath Selectors**: LinkedIn's UI may change, requiring updates to the XPath selectors.

## Improvements & Future Work
1. **Headless Browsing**: Use headless mode for Selenium to run the scraper in the background.
2. **Proxy & User-Agent Rotation**: Implement proxies and rotate user agents to reduce the risk of being blocked.
3. **Additional Data Fields**: Extend the scraper to collect work experience, skills, or endorsements.
4. **Pagination**: Add functionality to navigate through multiple search result pages.

## Conclusion
This script demonstrates the automation of data extraction from LinkedIn using Selenium and BeautifulSoup. While it serves as a robust foundation, adherence to ethical scraping practices and continuous maintenance is critical for long-term success.
