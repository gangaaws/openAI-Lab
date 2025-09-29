# OpenAI ChatGPT 5 Web Search Tools Lab Guide

## Lab Overview
**Duration:** 15-20 minutes  
**Platform:** [Google Colab](https://colab.research.google.com)  
**Prerequisites:** Basic Python knowledge, Google account  
**Objective:** Learn to use ChatGPT 5's web search tool for real-time information

---

## Learning Objectives
By the end of this lab, you will be able to:
- Enable web search in ChatGPT 5 responses
- Get real-time information for business decisions
- Research competitors and market trends
- Build automated research workflows

---

## Part 1: Setup (2 minutes)

### Step 1: Create Your Notebook
1. Go to [https://colab.research.google.com](https://colab.research.google.com)
2. Click **"New Notebook"**
3. Rename to `ChatGPT_5_Web_Search_Lab`

### Step 2: Install and Import
```python
# Install OpenAI library
!pip install openai --quiet

# Import what we need
from openai import OpenAI
from getpass import getpass
from datetime import datetime
```

### Step 3: Initialize Client
```python
# Enter your API key securely
api_key = getpass('Enter your OpenAI API key: ')

# Create the client
client = OpenAI(api_key=api_key)

print("✅ Ready to search the web with ChatGPT 5!")
```

---

## Part 2: Basic Web Search (5 minutes)

### Step 4: Your First Web Search
Let's start with a simple search for current information:

```python
# Basic web search example
response = client.responses.create(
    model="gpt-5",
    tools=[{"type": "web_search"}],
    input="What was a positive business news story from today?"
)

print("📰 Today's Positive Business News:")
print(response.output_text)
print("\n" + "="*50 + "\n")
```

**What's happening:**
- `tools=[{"type": "web_search"}]` enables web search
- ChatGPT 5 searches the internet for current information
- Returns real-time results, not just training data

### Step 5: Get Current Market Information
Search for real-time market data:

```python
# Get current market information
response = client.responses.create(
    model="gpt-5",
    tools=[{"type": "web_search"}],
    input="What are the current stock market indices (DOW, S&P 500, NASDAQ) and how are they performing today?"
)

print("📈 Current Market Status:")
print(response.output_text)
print("\n" + "="*50 + "\n")
```

### Step 6: Research Specific Companies
Get the latest information about any company:

```python
def research_company(company_name):
    """
    Get current information about a company
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""Find the latest information about {company_name}:
        - Recent news or announcements
        - Stock price movement today
        - Any major developments
        Keep it concise - 3-4 bullet points."""
    )
    return response.output_text

# Try it with different companies
companies = ["Apple", "Tesla", "Microsoft"]

for company in companies:
    print(f"🏢 Latest on {company}:")
    print(research_company(company))
    print("-" * 40)

print("\n" + "="*50 + "\n")
```

---

## Part 3: Business Research Applications (5 minutes)

### Step 7: Competitor Analysis
Monitor what competitors are doing:

```python
# Research competitor activity
def check_competitor(competitor_name):
    """
    Get latest competitor information
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""What has {competitor_name} announced or done in the last 7 days?
        Include:
        - Product launches
        - Business changes
        - Partnerships
        - Any other significant news
        If nothing significant, say so."""
    )
    return response.output_text

# Monitor a competitor
competitor_update = check_competitor("Amazon")
print("🎯 Competitor Intelligence - Amazon:")
print(competitor_update)
print("\n" + "="*50 + "\n")
```

### Step 8: Industry Trend Research
Stay updated on industry trends:

```python
# Research industry trends
def research_trend(industry, topic):
    """
    Research current trends in an industry
    """
    query = f"What are the latest developments in {industry} regarding {topic}? Focus on news from the last 30 days."
    
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=query
    )
    return response.output_text

# Research different trends
trends = [
    ("retail", "AI adoption"),
    ("finance", "cryptocurrency regulation"),
    ("healthcare", "telemedicine growth")
]

for industry, topic in trends:
    print(f"🔍 Trend Analysis - {industry.title()} + {topic}:")
    result = research_trend(industry, topic)
    print(result)
    print("\n" + "-"*40 + "\n")

print("="*50 + "\n")
```

### Step 9: Real-Time Price Checking
Get current prices for anything:

```python
# Check current prices
def check_price(item):
    """
    Get current price information
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"What is the current price of {item}? Provide the most recent price you can find with the source."
    )
    return response.output_text

# Check various prices
items = [
    "Bitcoin",
    "Gold per ounce",
    "Oil per barrel (WTI)",
    "Tesla stock"
]

print("💰 Current Prices:")
for item in items:
    print(f"\n{item}:")
    print(check_price(item))
    print("-" * 30)

print("\n" + "="*50 + "\n")
```

---

## Part 4: Automated Research Assistant (4 minutes)

### Step 10: Daily Business Briefing
Create an automated morning briefing:

```python
def morning_briefing():
    """
    Generate a morning business briefing
    """
    current_date = datetime.now().strftime("%B %d, %Y")
    
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""Create a morning business briefing for {current_date}:
        
        1. Top 3 business headlines
        2. Stock market opening status
        3. One major deal or merger announced
        4. One technology news item
        5. Economic indicator of the day
        
        Keep each item to 1-2 sentences."""
    )
    return response.output_text

# Generate your briefing
print(f"☀️ Morning Business Briefing - {datetime.now().strftime('%B %d, %Y')}")
print("=" * 50)
print(morning_briefing())
print("\n" + "="*50 + "\n")
```

### Step 11: Custom Research Reports
Build focused research on any topic:

```python
def research_report(topic, questions):
    """
    Create a research report on any topic
    
    Args:
        topic: What to research
        questions: List of specific questions to answer
    """
    
    # Format questions
    questions_text = "\n".join([f"{i+1}. {q}" for i, q in enumerate(questions)])
    
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""Research Report: {topic}
        
        Please research and answer these specific questions:
        {questions_text}
        
        Use current information from the web. Cite sources when possible."""
    )
    return response.output_text

# Create a custom research report
topic = "Electric Vehicle Market 2024"
questions = [
    "What is the current market share of top 3 EV manufacturers?",
    "What new EV models were announced this month?",
    "What is the average EV price in the US currently?",
    "What are the latest government incentives for EVs?"
]

report = research_report(topic, questions)
print(f"📊 Research Report: {topic}")
print("=" * 50)
print(report)
print("\n" + "="*50 + "\n")
```

---

## Part 5: Practical Business Tools (4 minutes)

### Step 12: Due Diligence Helper
Quick research for business decisions:

```python
def quick_due_diligence(company_name):
    """
    Perform quick due diligence research
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""Perform quick due diligence on {company_name}:
        
        1. Is the company publicly traded? If yes, stock symbol?
        2. Recent financial performance (last reported quarter)
        3. Any recent legal issues or controversies?
        4. Major competitors
        5. Recent leadership changes
        6. Overall reputation/sentiment
        
        Provide factual information only."""
    )
    return response.output_text

# Research a company
company_dd = quick_due_diligence("OpenAI")
print("🔎 Due Diligence Report - OpenAI:")
print(company_dd)
print("\n" + "="*50 + "\n")
```

---

## 🎯 Challenge: Build a Market Monitor

Create a simple tool that monitors multiple aspects of the market.

### Requirements:
1. Check 3 different stocks
2. Get one piece of economic news
3. Find one merger/acquisition announcement
4. Format results nicely

### Starter Code:
```python
def market_monitor(stock_symbols, sectors):
    """
    Monitor market conditions
    
    Args:
        stock_symbols: List of stock symbols to check
        sectors: List of sectors to monitor
    
    Returns:
        Formatted market update
    """
    
    # TODO: Get current prices for stocks
    # Hint: Use web search for each stock
    
    # TODO: Get sector news
    # Hint: Search for "latest news in [sector] sector today"
    
    # TODO: Find M&A activity
    # Hint: Search for "mergers and acquisitions announced today"
    
    # TODO: Format everything nicely
    
    pass  # Replace with your code

# Test your monitor
# stocks = ["AAPL", "GOOGL", "MSFT"]
# sectors = ["technology", "healthcare"]
# update = market_monitor(stocks, sectors)
# print(update)
```

### 💡 Hints:
- Make multiple web search calls
- Format output with headers and bullets
- Add timestamps to your report
- Handle cases where no news is found

---

## Complete Solution Code

```python
# ============================================
# COMPLETE CODE FOR CHATGPT 5 WEB SEARCH LAB
# ============================================

# SETUP
# ---------------------------------------------
# !pip install openai --quiet

from openai import OpenAI
from getpass import getpass
from datetime import datetime

# Initialize client
api_key = getpass('Enter your OpenAI API key: ')
client = OpenAI(api_key=api_key)
print("✅ Ready to search the web with ChatGPT 5!")

# BASIC WEB SEARCH
# ---------------------------------------------

# Simple search
response = client.responses.create(
    model="gpt-5",
    tools=[{"type": "web_search"}],
    input="What was a positive business news story from today?"
)
print("📰 Today's Positive Business News:")
print(response.output_text)
print("\n" + "="*50 + "\n")

# Market information
response = client.responses.create(
    model="gpt-5",
    tools=[{"type": "web_search"}],
    input="What are the current stock market indices (DOW, S&P 500, NASDAQ) and how are they performing today?"
)
print("📈 Current Market Status:")
print(response.output_text)
print("\n" + "="*50 + "\n")

# BUSINESS RESEARCH FUNCTIONS
# ---------------------------------------------

def research_company(company_name):
    """Get current information about a company"""
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""Find the latest information about {company_name}:
        - Recent news or announcements
        - Stock price movement today
        - Any major developments
        Keep it concise - 3-4 bullet points."""
    )
    return response.output_text

def check_competitor(competitor_name):
    """Get latest competitor information"""
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""What has {competitor_name} announced or done in the last 7 days?
        Include: Product launches, Business changes, Partnerships, Any other significant news"""
    )
    return response.output_text

def research_trend(industry, topic):
    """Research current trends in an industry"""
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"What are the latest developments in {industry} regarding {topic}? Focus on news from the last 30 days."
    )
    return response.output_text

def check_price(item):
    """Get current price information"""
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"What is the current price of {item}? Provide the most recent price you can find with the source."
    )
    return response.output_text

def morning_briefing():
    """Generate a morning business briefing"""
    current_date = datetime.now().strftime("%B %d, %Y")
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""Create a morning business briefing for {current_date}:
        1. Top 3 business headlines
        2. Stock market opening status
        3. One major deal or merger announced
        4. One technology news item
        5. Economic indicator of the day
        Keep each item to 1-2 sentences."""
    )
    return response.output_text

def quick_due_diligence(company_name):
    """Perform quick due diligence research"""
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input=f"""Perform quick due diligence on {company_name}:
        1. Is the company publicly traded? If yes, stock symbol?
        2. Recent financial performance (last reported quarter)
        3. Any recent legal issues or controversies?
        4. Major competitors
        5. Recent leadership changes
        6. Overall reputation/sentiment
        Provide factual information only."""
    )
    return response.output_text

# CHALLENGE SOLUTION
# ---------------------------------------------

def market_monitor(stock_symbols, sectors):
    """
    Monitor market conditions
    """
    results = []
    
    # Header
    results.append(f"📊 MARKET MONITOR - {datetime.now().strftime('%B %d, %Y %H:%M')}")
    results.append("=" * 50)
    
    # Stock prices
    results.append("\n📈 STOCK PRICES:")
    for symbol in stock_symbols:
        response = client.responses.create(
            model="gpt-5",
            tools=[{"type": "web_search"}],
            input=f"What is the current price of {symbol} stock and its change today?"
        )
        results.append(f"• {symbol}: {response.output_text}")
    
    # Sector news
    results.append("\n🏢 SECTOR NEWS:")
    for sector in sectors:
        response = client.responses.create(
            model="gpt-5",
            tools=[{"type": "web_search"}],
            input=f"One important news item from the {sector} sector today. Keep to 1 sentence."
        )
        results.append(f"• {sector.title()}: {response.output_text}")
    
    # M&A Activity
    results.append("\n🤝 M&A ACTIVITY:")
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input="Find one merger or acquisition announced in the last 24 hours. If none, say 'No major M&A activity today.'"
    )
    results.append(response.output_text)
    
    # Economic news
    results.append("\n💼 ECONOMIC UPDATE:")
    response = client.responses.create(
        model="gpt-5",
        tools=[{"type": "web_search"}],
        input="One important economic news item or indicator released today."
    )
    results.append(response.output_text)
    
    return "\n".join(results)

# Test everything
print("Testing Company Research...")
print(research_company("Apple"))
print("\n" + "="*50 + "\n")

print("Testing Market Monitor...")
stocks = ["AAPL", "GOOGL", "MSFT"]
sectors = ["technology", "healthcare"]
print(market_monitor(stocks, sectors))

print("\n✅ All functions tested successfully!")
```

---

## Troubleshooting

**No results returned:**
- Make sure you included `tools=[{"type": "web_search"}]`
- Check your API key has access to tools

**Outdated information:**
- Be specific about timeframes ("today", "last 24 hours", "this week")
- Ask for "latest" or "current" information explicitly

**Rate limits:**
- Add delays between requests if making many calls
- Batch related questions into single prompts when possible

---

## Resources

- **OpenAI Platform**: [https://platform.openai.com](https://platform.openai.com)
- **API Documentation**: [https://platform.openai.com/docs](https://platform.openai.com/docs)
- **Tools Documentation**: [https://platform.openai.com/docs/guides/tools](https://platform.openai.com/docs/guides/tools)
- **API Keys**: [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Google Colab**: [https://colab.research.google.com](https://colab.research.google.com)

---

## Lab Checklist

- [ ] Set up OpenAI client
- [ ] Performed basic web search
- [ ] Got current market information
- [ ] Researched specific companies
- [ ] Built research functions
- [ ] Created market monitor tool
- [ ] Tested all functions

---

## Key Takeaways

You've learned to:
- 🔍 Enable web search in ChatGPT 5
- 📰 Get real-time news and information
- 📈 Research stocks and markets
- 🏢 Monitor competitors
- 📊 Build automated research tools

**Great job!** 🎉 You can now build AI assistants that access current information from the web!