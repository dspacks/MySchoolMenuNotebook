# MySchoolMenuNotebook

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dspacks/MySchoolMenuNotebook/blob/main/school_menus_notebook.ipynb)

A Python-based Jupyter notebook that converts school lunch and breakfast menus into importable calendar files (.ics format). This is a reimplementation of [andrewdefilippis/my-school-menus](https://github.com/andrewdefilippis/my-school-menus) designed to run in Google Colab, making it easily accessible to students and parents without requiring any software installation.

## 📋 Overview

MySchoolMenuNotebook fetches school menu data from HealthePro and MySchoolMenus APIs and converts them into RFC 5545-compliant iCalendar files that can be imported into any calendar application (Google Calendar, Outlook, Apple Calendar, etc.). This allows students and parents to see daily meal options directly in their calendars.

## ✨ Features

- **Simple URL-based interface** - Just paste your school menu URL from your browser
- **Multiple meal type support** - Breakfast, lunch, snack, and dinner menus
- **Flexible scheduling** - Configure custom times for each meal type
- **RFC 5545 compliant** - Generates standard iCalendar (.ics) format files
- **Multiple export formats** - Both calendar (.ics) and CSV formats
- **Detailed menu information** - Includes main dishes, categories, and all menu items
- **No installation required** - Runs entirely in Google Colab
- **Multi-school support** - Can handle menus from different schools/districts
- **Preview functionality** - View menu data before exporting

## 🚀 Getting Started

### Prerequisites

No local installation required! Just open the notebook in Google Colab:

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dspacks/MySchoolMenuNotebook/blob/main/school_menus_notebook.ipynb)

### Usage

1. **Open the notebook** in Google Colab using the badge above
2. **Run the first cell** to install/import required libraries
3. **Find your menu URL**:
   - Visit your school menu website (healthepro.com or myschoolmenus.com)
   - Navigate to your school's menu page
   - Click on a specific menu (Breakfast, Lunch, etc.)
   - Copy the URL from your browser's address bar
4. **Paste the URL** in the "Step 1: Enter Menu URLs" cell
5. **Configure meal times** in Step 2 (default: 8:00 AM for breakfast, 12:00 PM for lunch)
6. **Run all cells** to generate your calendar
7. **Download the .ics file** from the Files sidebar (left panel)
8. **Import into your calendar** application

### Example URLs

The tool works with URLs in this format:

```
https://menus.healthepro.com/organizations/2230/sites/14066/menus/102948
https://www.myschoolmenus.com/organizations/2230/sites/14066/menus/65638
```

## 🏗️ Technical Architecture

### Core Components

#### 1. URL Parser (`parse_menu_url`)
Extracts organization ID, site ID, and menu ID from menu URLs using regex pattern matching.

```python
parse_menu_url("https://menus.healthepro.com/organizations/2230/sites/14066/menus/102948")
# Returns: {'district_id': 2230, 'site_id': 14066, 'menu_id': 102948}
```

#### 2. Data Models

- **`MenuType`** (Enum): BREAKFAST, LUNCH, SNACK, DINNER
- **`MenuItem`** (Dataclass): Date, main dish, categories, all items, menu type
- **`CalendarEvent`** (Dataclass): RFC 5545-compliant calendar event with proper formatting

#### 3. MenuClient (API Client)

Fetches menu data from multiple base URLs for reliability:
- `https://menus.healthepro.com/api`
- `https://www.myschoolmenus.com/api`
- `https://myschoolmenus.com/api`

**API Endpoint Pattern:**
```
/organizations/{district_id}/menus/{menu_id}/year/{year}/month/{month}/date_overwrites
```

Features:
- Automatic retry across multiple endpoints
- Flexible JSON response parsing (handles lists, nested data structures)
- Extracts recipe items and categories
- Filters out non-school days (breaks, holidays)

#### 4. CalendarGenerator

Converts menu items to RFC 5545-compliant iCalendar format:
- Supports both timed events and all-day events
- Proper text escaping for special characters
- Line folding per RFC 5545 specification
- Configurable event duration (30 min breakfast, 45 min lunch)

### Data Flow

```
User Input (URL)
  → URL Parser (extract IDs)
  → MenuClient (fetch from API)
  → MenuItem objects
  → CalendarGenerator
  → iCalendar (.ics) file
  → Import to calendar app
```

## 📦 Dependencies

All dependencies are automatically installed when you run the first cell in Colab:

- `requests` - HTTP requests for API calls
- `json` - JSON parsing
- `re` - Regular expressions for URL parsing
- `uuid` - Unique identifier generation
- `datetime` - Date/time handling
- `dataclasses` - Data structure definitions
- `pandas` - Data display and CSV export

## 📄 Output Formats

### iCalendar (.ics)
Standard calendar format that can be imported into:
- Google Calendar
- Microsoft Outlook
- Apple Calendar
- Any RFC 5545-compliant calendar application

### CSV
Tabular format with columns:
- Date
- Menu (with prefix B: or L:)
- Description preview

### Example Calendar Entry

```
Event: L: 5" Deep Dish Turkey Pepperoni Pizza
Time: 12:00 PM (45 minutes)
Description:
  Lunch Entree
  5" Deep Dish Turkey Pepperoni Pizza
  5" Deep Dish Cheese Pizza
  Yogurt/String Cheese/ Bread Meal
  Vegetables
  Baby Carrots
  ...
```

## 🔧 Configuration

### Meal Times

Configure meal times in Step 2 of the notebook:

```python
breakfast_time_str = "08:00 AM"  # or "8:00" (24-hour format)
lunch_time_str = "12:00 PM"      # or "12:00"
```

Supported formats:
- 24-hour: `HH:MM` (e.g., "08:00", "14:30")
- 12-hour: `HH:MM AM/PM` (e.g., "8:00 AM", "2:30 PM")

### Multiple Menus

You can configure multiple menus from different schools or meal types:

```python
url_breakfast = "https://menus.healthepro.com/organizations/2230/sites/14066/menus/102948"
url_lunch = "https://menus.healthepro.com/organizations/2230/sites/14066/menus/102946"
```

## 🐛 Troubleshooting

### "Could not parse URL" Error
- Ensure your URL follows the format: `/organizations/{id}/sites/{id}/menus/{id}`
- Check that you copied the complete URL from your browser

### "No calendar events generated"
- Verify the menu URLs are correct
- Check that the menu has data for the current month
- Look for warning messages about API failures

### "Failed to fetch menu from all base URLs"
- Check your internet connection
- The school district's API might be temporarily unavailable
- Try again in a few minutes

### No items showing for certain dates
- Schools typically don't publish menus for holidays/breaks
- Check the warnings in the output - they indicate "NO SCHOOL" days

## 🤝 Contributing

Contributions are welcome! Here are some ways you can help:

1. **Report bugs** - Open an issue with details about the problem
2. **Suggest features** - Share ideas for improvements
3. **Submit pull requests** - Fix bugs or add new features
4. **Improve documentation** - Help make the docs clearer
5. **Test with your school** - Try it with different school districts

### Development Setup

1. Fork the repository
2. Clone your fork locally
3. Make your changes
4. Test in Google Colab
5. Submit a pull request

## 📖 API Documentation

### MenuClient Methods

```python
client = MenuClient()
items = client.fetch_menu(url, menu_type=MenuType.LUNCH)
# Returns: List[MenuItem]
```

### CalendarGenerator Methods

```python
calendar = CalendarGenerator("School Menu Calendar")
calendar.add_menu_item(item, event_time=time(12, 0))
calendar.add_menu_items(items, event_time=time(12, 0))
ics_content = calendar.to_ics()
calendar.save("output.ics")
```

## 🎯 Use Cases

- **Parents** - See what their children will eat each day
- **Students** - Plan ahead for favorite meals
- **Dietary planning** - Coordinate meals around menu offerings
- **Food allergies** - Review ingredients in advance
- **Meal prep** - Decide which days to pack lunch vs. buy

## 📜 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) file for details.

Copyright © 2025 dspacks

## 🙏 Acknowledgments

- Based on [andrewdefilippis/my-school-menus](https://github.com/andrewdefilippis/my-school-menus)
- Menu data provided by HealthePro and MySchoolMenus APIs
- Built for the benefit of students and parents everywhere

## 📞 Support

- **Issues**: Report bugs or request features via [GitHub Issues](https://github.com/dspacks/MySchoolMenuNotebook/issues)
- **Discussions**: Ask questions in [GitHub Discussions](https://github.com/dspacks/MySchoolMenuNotebook/discussions)

## 🔗 Related Resources

- [RFC 5545 - iCalendar Specification](https://tools.ietf.org/html/rfc5545)
- [Google Colab Documentation](https://colab.research.google.com/)
- [HealthePro School Menus](https://www.healthepro.com/)
- [MySchoolMenus](https://www.myschoolmenus.com/)

---

**Made with ❤️ for students, parents, and school communities**
