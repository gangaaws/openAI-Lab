# OpenAI ChatGPT 5 Function Calling Lab Guide

## Lab Overview
**Duration:** 15-20 minutes  
**Platform:** [Google Colab](https://colab.research.google.com)  
**Prerequisites:** Basic Python knowledge, Google account  
**Objective:** Learn to extend ChatGPT 5 with custom functions for business automation

---

## Learning Objectives
By the end of this lab, you will be able to:
- Define custom functions for ChatGPT 5 to call
- Handle function responses and integrate with APIs
- Build business automation tools
- Create interactive AI assistants with real capabilities

---

## Part 1: Setup (2 minutes)

### Step 1: Create Your Notebook
1. Go to [https://colab.research.google.com](https://colab.research.google.com)
2. Click **"New Notebook"**
3. Rename to `ChatGPT_5_Functions_Lab`

### Step 2: Install and Import
```python
# Install required libraries
!pip install openai yfinance --quiet

# Import what we need
from openai import OpenAI
from getpass import getpass
import json
from datetime import datetime
import random
import yfinance as yf
```

### Step 3: Initialize Client
```python
# Enter your API key securely
api_key = getpass('Enter your OpenAI API key: ')

# Create the client
client = OpenAI(api_key=api_key)

print("✅ Ready to use function calling with ChatGPT 5!")
```

---

## Part 2: Basic Function Calling (5 minutes)

### Step 4: Your First Function
Let's start with a simple example - getting weather:

```python
# Define a simple weather function (simulated)
def get_weather(location):
    """
    Simulated weather function - returns fake weather data
    In production, this would call a real weather API
    """
    # Simulate weather data
    temp = random.randint(60, 85)
    conditions = random.choice(["Sunny", "Cloudy", "Rainy", "Partly Cloudy"])
    
    return {
        "location": location,
        "temperature": f"{temp}°F",
        "conditions": conditions,
        "humidity": f"{random.randint(30, 70)}%"
    }

# Define the function schema for ChatGPT
tools = [
    {
        "type": "function",
        "name": "get_weather",
        "description": "Get current weather for a given location.",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "City and country e.g. New York, USA",
                }
            },
            "required": ["location"],
            "additionalProperties": False,
        },
        "strict": True,
    }
]

# Test the function calling
response = client.responses.create(
    model="gpt-5",
    input=[
        {"role": "user", "content": "What's the weather like in Paris, France today?"},
    ],
    tools=tools,
)

# Check if function was called
if hasattr(response, 'tool_calls'):
    for tool_call in response.tool_calls:
        if tool_call.function.name == "get_weather":
            # Parse the arguments
            args = json.loads(tool_call.function.arguments)
            # Call our actual function
            weather_data = get_weather(args['location'])
            print(f"🌤️ Weather in {args['location']}:")
            print(json.dumps(weather_data, indent=2))

print("\n" + "="*50 + "\n")
```

### Step 5: Business Function - Stock Price Checker
Create a function to get real stock prices:

```python
# Real stock price function using yfinance
def get_stock_price(symbol):
    """
    Get actual stock price using yfinance
    """
    try:
        stock = yf.Ticker(symbol)
        info = stock.info
        current_price = info.get('currentPrice', info.get('regularMarketPrice', 'N/A'))
        
        return {
            "symbol": symbol,
            "company": info.get('longName', symbol),
            "price": current_price,
            "currency": info.get('currency', 'USD'),
            "change": info.get('regularMarketChangePercent', 'N/A'),
            "timestamp": datetime.now().isoformat()
        }
    except Exception as e:
        return {
            "error": f"Could not fetch data for {symbol}: {str(e)}"
        }

# Define the function schema
stock_tools = [
    {
        "type": "function",
        "name": "get_stock_price",
        "description": "Get current stock price for a company",
        "parameters": {
            "type": "object",
            "properties": {
                "symbol": {
                    "type": "string",
                    "description": "Stock ticker symbol (e.g., AAPL for Apple, MSFT for Microsoft)",
                }
            },
            "required": ["symbol"],
            "additionalProperties": False,
        },
        "strict": True,
    }
]

# Test it
response = client.responses.create(
    model="gpt-5",
    input=[
        {"role": "user", "content": "What's the current price of Apple stock?"},
    ],
    tools=stock_tools,
)

# Handle the function call
if hasattr(response, 'tool_calls'):
    for tool_call in response.tool_calls:
        if tool_call.function.name == "get_stock_price":
            args = json.loads(tool_call.function.arguments)
            stock_data = get_stock_price(args['symbol'])
            print(f"📈 Stock Information:")
            print(json.dumps(stock_data, indent=2))

print("\n" + "="*50 + "\n")
```

### Step 6: Multiple Functions
Let ChatGPT choose between different functions:

```python
# Define multiple business functions
def calculate_discount(original_price, discount_percentage):
    """Calculate discounted price"""
    discount_amount = original_price * (discount_percentage / 100)
    final_price = original_price - discount_amount
    return {
        "original_price": original_price,
        "discount_percentage": discount_percentage,
        "discount_amount": round(discount_amount, 2),
        "final_price": round(final_price, 2)
    }

def calculate_tax(amount, tax_rate):
    """Calculate tax on an amount"""
    tax_amount = amount * (tax_rate / 100)
    total_with_tax = amount + tax_amount
    return {
        "amount": amount,
        "tax_rate": tax_rate,
        "tax_amount": round(tax_amount, 2),
        "total_with_tax": round(total_with_tax, 2)
    }

def convert_currency(amount, from_currency, to_currency):
    """Convert currency (simulated rates)"""
    # Simulated exchange rates
    rates = {
        "USD_EUR": 0.92,
        "USD_GBP": 0.79,
        "EUR_USD": 1.09,
        "GBP_USD": 1.27,
        "USD_JPY": 150.5,
        "EUR_GBP": 0.86
    }
    
    key = f"{from_currency}_{to_currency}"
    if key in rates:
        converted = amount * rates[key]
        return {
            "amount": amount,
            "from": from_currency,
            "to": to_currency,
            "converted_amount": round(converted, 2),
            "rate": rates[key]
        }
    else:
        return {"error": f"Exchange rate not available for {from_currency} to {to_currency}"}

# Define all function schemas
business_tools = [
    {
        "type": "function",
        "name": "calculate_discount",
        "description": "Calculate the discounted price of an item",
        "parameters": {
            "type": "object",
            "properties": {
                "original_price": {
                    "type": "number",
                    "description": "Original price of the item"
                },
                "discount_percentage": {
                    "type": "number",
                    "description": "Discount percentage (e.g., 20 for 20% off)"
                }
            },
            "required": ["original_price", "discount_percentage"],
            "additionalProperties": False,
        },
        "strict": True,
    },
    {
        "type": "function",
        "name": "calculate_tax",
        "description": "Calculate tax amount and total with tax",
        "parameters": {
            "type": "object",
            "properties": {
                "amount": {
                    "type": "number",
                    "description": "Base amount before tax"
                },
                "tax_rate": {
                    "type": "number",
                    "description": "Tax rate percentage (e.g., 8.5 for 8.5%)"
                }
            },
            "required": ["amount", "tax_rate"],
            "additionalProperties": False,
        },
        "strict": True,
    },
    {
        "type": "function",
        "name": "convert_currency",
        "description": "Convert amount from one currency to another",
        "parameters": {
            "type": "object",
            "properties": {
                "amount": {
                    "type": "number",
                    "description": "Amount to convert"
                },
                "from_currency": {
                    "type": "string",
                    "description": "Source currency code (e.g., USD, EUR, GBP)"
                },
                "to_currency": {
                    "type": "string",
                    "description": "Target currency code (e.g., USD, EUR, GBP)"
                }
            },
            "required": ["amount", "from_currency", "to_currency"],
            "additionalProperties": False,
        },
        "strict": True,
    }
]

# Test with different queries
test_queries = [
    "What's the price of a $500 item with a 25% discount?",
    "How much tax do I pay on $1000 with 8.5% tax rate?",
    "Convert 1000 USD to EUR"
]

for query in test_queries:
    print(f"❓ Query: {query}")
    
    response = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": query}],
        tools=business_tools,
    )
    
    # Handle function calls
    if hasattr(response, 'tool_calls'):
        for tool_call in response.tool_calls:
            func_name = tool_call.function.name
            args = json.loads(tool_call.function.arguments)
            
            # Call the appropriate function
            if func_name == "calculate_discount":
                result = calculate_discount(args['original_price'], args['discount_percentage'])
            elif func_name == "calculate_tax":
                result = calculate_tax(args['amount'], args['tax_rate'])
            elif func_name == "convert_currency":
                result = convert_currency(args['amount'], args['from_currency'], args['to_currency'])
            
            print(f"📊 Result:")
            print(json.dumps(result, indent=2))
    
    print("-" * 40)

print("\n" + "="*50 + "\n")
```

---

## Part 3: Interactive Assistant (5 minutes)

### Step 7: Build a Complete Assistant
Create an assistant that can handle multiple requests:

```python
def process_with_functions(user_input, tools, function_map):
    """
    Process user input with function calling
    
    Args:
        user_input: The user's question
        tools: List of tool definitions
        function_map: Dictionary mapping function names to actual functions
    """
    # Get ChatGPT's response
    response = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": user_input}],
        tools=tools,
    )
    
    results = []
    
    # Check if functions were called
    if hasattr(response, 'tool_calls'):
        for tool_call in response.tool_calls:
            func_name = tool_call.function.name
            args = json.loads(tool_call.function.arguments)
            
            # Call the function if it exists
            if func_name in function_map:
                result = function_map[func_name](**args)
                results.append({
                    "function": func_name,
                    "arguments": args,
                    "result": result
                })
    
    return results

# Create function map
function_map = {
    "calculate_discount": calculate_discount,
    "calculate_tax": calculate_tax,
    "convert_currency": convert_currency,
    "get_stock_price": get_stock_price
}

# Combine all tools
all_tools = business_tools + stock_tools

# Interactive assistant
print("💼 Business Assistant Ready!")
print("Ask me about discounts, taxes, currency conversion, or stock prices.")
print("Type 'quit' to exit.\n")

# Simulate a few interactions
sample_questions = [
    "What's Microsoft stock price?",
    "Calculate 30% off of $250",
    "What's the tax on $5000 at 7% rate?",
    "Convert 2000 EUR to USD"
]

for question in sample_questions:
    print(f"You: {question}")
    results = process_with_functions(question, all_tools, function_map)
    
    for result in results:
        print(f"Assistant: Using {result['function']}...")
        print(f"Result: {json.dumps(result['result'], indent=2)}")
    
    print("-" * 40)

print("\n" + "="*50 + "\n")
```

### Step 8: Database Operations
Simulate database functions for business data:

```python
# Simulate a customer database
customer_db = {
    "CUST001": {"name": "Acme Corp", "balance": 15000, "credit_limit": 50000},
    "CUST002": {"name": "TechStart Inc", "balance": 8500, "credit_limit": 25000},
    "CUST003": {"name": "Global Retail", "balance": 32000, "credit_limit": 100000}
}

def check_customer_credit(customer_id):
    """Check customer credit status"""
    if customer_id in customer_db:
        customer = customer_db[customer_id]
        available_credit = customer["credit_limit"] - customer["balance"]
        return {
            "customer_id": customer_id,
            "name": customer["name"],
            "current_balance": customer["balance"],
            "credit_limit": customer["credit_limit"],
            "available_credit": available_credit,
            "status": "Good Standing" if available_credit > 0 else "Over Limit"
        }
    else:
        return {"error": f"Customer {customer_id} not found"}

def process_payment(customer_id, amount):
    """Process a payment for a customer"""
    if customer_id in customer_db:
        customer_db[customer_id]["balance"] -= amount
        new_balance = customer_db[customer_id]["balance"]
        return {
            "customer_id": customer_id,
            "payment_amount": amount,
            "new_balance": new_balance,
            "status": "Payment Processed"
        }
    else:
        return {"error": f"Customer {customer_id} not found"}

# Define database function schemas
db_tools = [
    {
        "type": "function",
        "name": "check_customer_credit",
        "description": "Check a customer's credit limit and balance",
        "parameters": {
            "type": "object",
            "properties": {
                "customer_id": {
                    "type": "string",
                    "description": "Customer ID (e.g., CUST001)"
                }
            },
            "required": ["customer_id"],
            "additionalProperties": False,
        },
        "strict": True,
    },
    {
        "type": "function",
        "name": "process_payment",
        "description": "Process a payment for a customer",
        "parameters": {
            "type": "object",
            "properties": {
                "customer_id": {
                    "type": "string",
                    "description": "Customer ID"
                },
                "amount": {
                    "type": "number",
                    "description": "Payment amount"
                }
            },
            "required": ["customer_id", "amount"],
            "additionalProperties": False,
        },
        "strict": True,
    }
]

# Test database functions
db_queries = [
    "Check the credit status for customer CUST001",
    "Process a payment of $5000 for customer CUST002"
]

for query in db_queries:
    print(f"📝 Query: {query}")
    
    response = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": query}],
        tools=db_tools,
    )
    
    if hasattr(response, 'tool_calls'):
        for tool_call in response.tool_calls:
            func_name = tool_call.function.name
            args = json.loads(tool_call.function.arguments)
            
            if func_name == "check_customer_credit":
                result = check_customer_credit(args['customer_id'])
            elif func_name == "process_payment":
                result = process_payment(args['customer_id'], args['amount'])
            
            print(f"💾 Database Result:")
            print(json.dumps(result, indent=2))
    
    print("-" * 40)

print("\n" + "="*50 + "\n")
```

---

## Part 4: Error Handling (4 minutes)

### Step 9: Handle Errors Gracefully
Add proper error handling to functions:

```python
def safe_function_call(func_name, args, function_map):
    """
    Safely call a function with error handling
    """
    try:
        if func_name not in function_map:
            return {"error": f"Function {func_name} not found"}
        
        result = function_map[func_name](**args)
        return {"success": True, "data": result}
    
    except TypeError as e:
        return {"error": f"Invalid arguments: {str(e)}"}
    except Exception as e:
        return {"error": f"Function failed: {str(e)}"}

# Test with error handling
def divide_numbers(numerator, denominator):
    """Divide two numbers"""
    if denominator == 0:
        raise ValueError("Cannot divide by zero")
    return numerator / denominator

# Add to function map
function_map["divide_numbers"] = divide_numbers

# Define the tool
divide_tool = {
    "type": "function",
    "name": "divide_numbers",
    "description": "Divide two numbers",
    "parameters": {
        "type": "object",
        "properties": {
            "numerator": {"type": "number"},
            "denominator": {"type": "number"}
        },
        "required": ["numerator", "denominator"],
        "additionalProperties": False,
    },
    "strict": True,
}

# Test error handling
test_cases = [
    "Divide 100 by 5",
    "Divide 50 by 0"  # This will cause an error
]

for test in test_cases:
    print(f"Test: {test}")
    
    response = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": test}],
        tools=[divide_tool],
    )
    
    if hasattr(response, 'tool_calls'):
        for tool_call in response.tool_calls:
            args = json.loads(tool_call.function.arguments)
            result = safe_function_call(tool_call.function.name, args, function_map)
            print(f"Result: {json.dumps(result, indent=2)}")
    
    print("-" * 30)

print("\n" + "="*50 + "\n")
```

---

## 🎯 Challenge: Build a Sales Assistant

Create an AI sales assistant that can handle customer inquiries and orders.

### Requirements:
1. Check product availability
2. Calculate order totals with discounts
3. Check customer credit
4. Process orders

### Starter Code:
```python
# Product catalog
products = {
    "PROD001": {"name": "Laptop", "price": 999, "stock": 50},
    "PROD002": {"name": "Mouse", "price": 25, "stock": 200},
    "PROD003": {"name": "Keyboard", "price": 75, "stock": 150}
}

def check_product_availability(product_id):
    """Check if a product is in stock"""
    # TODO: Implement this function
    # Return product details and stock level
    pass

def calculate_order_total(items, discount_percent=0):
    """Calculate total for an order"""
    # TODO: Implement this function
    # items should be a list of {product_id, quantity}
    # Apply discount if provided
    pass

def create_order(customer_id, items):
    """Create a new order"""
    # TODO: Implement this function
    # Check customer credit
    # Check product availability
    # Calculate total
    # Return order confirmation or error
    pass

# TODO: Define tool schemas for these functions

# TODO: Test with sample queries like:
# "Is the laptop in stock?"
# "What's the total for 2 laptops and 3 mice with a 10% discount?"
# "Create an order for customer CUST001: 1 laptop and 2 keyboards"
```

### 💡 Hints:
- Check stock levels before confirming orders
- Validate customer credit before processing
- Return clear error messages for out-of-stock items
- Format order confirmations nicely

---

## Complete Solution Code

```python
# ============================================
# COMPLETE CODE FOR CHATGPT 5 FUNCTION CALLING LAB
# ============================================

# SETUP
# ---------------------------------------------
# !pip install openai yfinance --quiet

from openai import OpenAI
from getpass import getpass
import json
from datetime import datetime
import random
import yfinance as yf

# Initialize client
api_key = getpass('Enter your OpenAI API key: ')
client = OpenAI(api_key=api_key)
print("✅ Ready to use function calling with ChatGPT 5!")

# BASIC FUNCTIONS
# ---------------------------------------------

def get_weather(location):
    """Simulated weather function"""
    temp = random.randint(60, 85)
    conditions = random.choice(["Sunny", "Cloudy", "Rainy", "Partly Cloudy"])
    return {
        "location": location,
        "temperature": f"{temp}°F",
        "conditions": conditions,
        "humidity": f"{random.randint(30, 70)}%"
    }

def get_stock_price(symbol):
    """Get actual stock price"""
    try:
        stock = yf.Ticker(symbol)
        info = stock.info
        current_price = info.get('currentPrice', info.get('regularMarketPrice', 'N/A'))
        return {
            "symbol": symbol,
            "company": info.get('longName', symbol),
            "price": current_price,
            "currency": info.get('currency', 'USD'),
            "timestamp": datetime.now().isoformat()
        }
    except Exception as e:
        return {"error": f"Could not fetch data for {symbol}"}

def calculate_discount(original_price, discount_percentage):
    """Calculate discounted price"""
    discount_amount = original_price * (discount_percentage / 100)
    final_price = original_price - discount_amount
    return {
        "original_price": original_price,
        "discount_percentage": discount_percentage,
        "discount_amount": round(discount_amount, 2),
        "final_price": round(final_price, 2)
    }

def calculate_tax(amount, tax_rate):
    """Calculate tax on an amount"""
    tax_amount = amount * (tax_rate / 100)
    total_with_tax = amount + tax_amount
    return {
        "amount": amount,
        "tax_rate": tax_rate,
        "tax_amount": round(tax_amount, 2),
        "total_with_tax": round(total_with_tax, 2)
    }

def convert_currency(amount, from_currency, to_currency):
    """Convert currency"""
    rates = {
        "USD_EUR": 0.92,
        "USD_GBP": 0.79,
        "EUR_USD": 1.09,
        "GBP_USD": 1.27,
    }
    key = f"{from_currency}_{to_currency}"
    if key in rates:
        converted = amount * rates[key]
        return {
            "amount": amount,
            "from": from_currency,
            "to": to_currency,
            "converted_amount": round(converted, 2),
            "rate": rates[key]
        }
    else:
        return {"error": f"Rate not available for {from_currency} to {to_currency}"}

# DATABASE FUNCTIONS
# ---------------------------------------------

customer_db = {
    "CUST001": {"name": "Acme Corp", "balance": 15000, "credit_limit": 50000},
    "CUST002": {"name": "TechStart Inc", "balance": 8500, "credit_limit": 25000},
    "CUST003": {"name": "Global Retail", "balance": 32000, "credit_limit": 100000}
}

def check_customer_credit(customer_id):
    """Check customer credit status"""
    if customer_id in customer_db:
        customer = customer_db[customer_id]
        available_credit = customer["credit_limit"] - customer["balance"]
        return {
            "customer_id": customer_id,
            "name": customer["name"],
            "current_balance": customer["balance"],
            "credit_limit": customer["credit_limit"],
            "available_credit": available_credit,
            "status": "Good Standing" if available_credit > 0 else "Over Limit"
        }
    else:
        return {"error": f"Customer {customer_id} not found"}

# CHALLENGE SOLUTION - SALES ASSISTANT
# ---------------------------------------------

products = {
    "PROD001": {"name": "Laptop", "price": 999, "stock": 50},
    "PROD002": {"name": "Mouse", "price": 25, "stock": 200},
    "PROD003": {"name": "Keyboard", "price": 75, "stock": 150}
}

def check_product_availability(product_id):
    """Check if a product is in stock"""
    if product_id in products:
        product = products[product_id]
        return {
            "product_id": product_id,
            "name": product["name"],
            "price": product["price"],
            "stock": product["stock"],
            "available": product["stock"] > 0
        }
    else:
        return {"error": f"Product {product_id} not found"}

def calculate_order_total(items, discount_percent=0):
    """Calculate total for an order"""
    subtotal = 0
    order_details = []
    
    for item in items:
        product_id = item.get("product_id")
        quantity = item.get("quantity", 1)
        
        if product_id in products:
            product = products[product_id]
            item_total = product["price"] * quantity
            subtotal += item_total
            order_details.append({
                "product": product["name"],
                "quantity": quantity,
                "unit_price": product["price"],
                "total": item_total
            })
    
    discount_amount = subtotal * (discount_percent / 100)
    total = subtotal - discount_amount
    
    return {
        "items": order_details,
        "subtotal": round(subtotal, 2),
        "discount_percent": discount_percent,
        "discount_amount": round(discount_amount, 2),
        "total": round(total, 2)
    }

def create_order(customer_id, items):
    """Create a new order"""
    # Check customer credit
    credit_check = check_customer_credit(customer_id)
    if "error" in credit_check:
        return credit_check
    
    # Calculate order total
    order_total = calculate_order_total(items)
    
    # Check if customer has enough credit
    if credit_check["available_credit"] < order_total["total"]:
        return {
            "error": "Insufficient credit",
            "available_credit": credit_check["available_credit"],
            "order_total": order_total["total"]
        }
    
    # Check product availability
    for item in items:
        product_id = item.get("product_id")
        quantity = item.get("quantity", 1)
        if product_id in products:
            if products[product_id]["stock"] < quantity:
                return {
                    "error": f"Insufficient stock for {products[product_id]['name']}",
                    "requested": quantity,
                    "available": products[product_id]["stock"]
                }
    
    # Process order (update stock and customer balance)
    for item in items:
        product_id = item.get("product_id")
        quantity = item.get("quantity", 1)
        products[product_id]["stock"] -= quantity
    
    customer_db[customer_id]["balance"] += order_total["total"]
    
    return {
        "order_id": f"ORD{random.randint(1000, 9999)}",
        "customer": credit_check["name"],
        "order_total": order_total["total"],
        "status": "Order Confirmed",
        "items": order_total["items"]
    }

# Define all tool schemas
all_tools = [
    {
        "type": "function",
        "name": "check_product_availability",
        "description": "Check if a product is in stock",
        "parameters": {
            "type": "object",
            "properties": {
                "product_id": {
                    "type": "string",
                    "description": "Product ID (e.g., PROD001)"
                }
            },
            "required": ["product_id"],
            "additionalProperties": False,
        },
        "strict": True,
    },
    {
        "type": "function",
        "name": "calculate_order_total",
        "description": "Calculate total for an order with optional discount",
        "parameters": {
            "type": "object",
            "properties": {
                "items": {
                    "type": "array",
                    "description": "List of items with product_id and quantity",
                    "items": {
                        "type": "object",
                        "properties": {
                            "product_id": {"type": "string"},
                            "quantity": {"type": "integer"}
                        },
                        "required": ["product_id", "quantity"]
                    }
                },
                "discount_percent": {
                    "type": "number",
                    "description": "Discount percentage (0-100)"
                }
            },
            "required": ["items"],
            "additionalProperties": False,
        },
        "strict": True,
    },
    {
        "type": "function",
        "name": "create_order",
        "description": "Create a new order for a customer",
        "parameters": {
            "type": "object",
            "properties": {
                "customer_id": {
                    "type": "string",
                    "description": "Customer ID"
                },
                "items": {
                    "type": "array",
                    "items": {
                        "type": "object",
                        "properties": {
                            "product_id": {"type": "string"},
                            "quantity": {"type": "integer"}
                        },
                        "required": ["product_id", "quantity"]
                    }
                }
            },
            "required": ["customer_id", "items"],
            "additionalProperties": False,
        },
        "strict": True,
    }
]

# Function map
function_map = {
    "check_product_availability": check_product_availability,
    "calculate_order_total": calculate_order_total,
    "create_order": create_order,
    "calculate_discount": calculate_discount,
    "calculate_tax": calculate_tax,
    "get_stock_price": get_stock_price,
    "check_customer_credit": check_customer_credit
}

# Test the sales assistant
test_queries = [
    "Is the laptop in stock?",
    "Calculate total for 2 laptops and 3 mice with 10% discount",
    "Create order for CUST001: 1 laptop and 2 keyboards"
]

print("🛍️ SALES ASSISTANT DEMO")
print("=" * 50)

for query in test_queries:
    print(f"\n❓ {query}")
    
    response = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": query}],
        tools=all_tools,
    )
    
    if hasattr(response, 'tool_calls'):
        for tool_call in response.tool_calls:
            func_name = tool_call.function.name
            args = json.loads(tool_call.function.arguments)
            
            if func_name in function_map:
                result = function_map[func_name](**args)
                print(f"📊 Result:")
                print(json.dumps(result, indent=2))

print("\n✅ All functions tested successfully!")
```

---

## Troubleshooting

**Function not being called:**
- Make sure function name and description are clear
- Check that parameters match what ChatGPT expects
- Verify `"strict": True` is set for structured output

**Arguments parsing error:**
- Use `json.loads()` to parse function arguments
- Handle both missing and extra arguments gracefully

**Function execution fails:**
- Add try/except blocks around function calls
- Return error messages in a consistent format

---

## Resources

- **OpenAI Platform**: [https://platform.openai.com](https://platform.openai.com)
- **Function Calling Guide**: [https://platform.openai.com/docs/guides/function-calling](https://platform.openai.com/docs/guides/function-calling)
- **API Reference**: [https://platform.openai.com/docs/api-reference](https://platform.openai.com/docs/api-reference)
- **Google Colab**: [https://colab.research.google.com](https://colab.research.google.com)

---

## Lab Checklist

- [ ] Set up OpenAI client
- [ ] Created first function (weather)
- [ ] Built business functions (stock, discount, tax)
- [ ] Handled multiple functions in one request
- [ ] Simulated database operations
- [ ] Added error handling
- [ ] Built sales assistant challenge
- [ ] Tested all functions

---

## Key Takeaways

You've learned to:
- 🔧 Define custom functions for ChatGPT 5
- 📊 Process function calls and return results
- 💼 Build business automation tools
- 🏪 Create interactive sales assistants
- ⚡ Handle errors gracefully

**Excellent work!** 🎉 You can now build powerful AI assistants that interact with your business systems!