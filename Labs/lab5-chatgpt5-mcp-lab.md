# OpenAI ChatGPT 5 MCP Servers Lab Guide

## Lab Overview
**Duration:** 15-20 minutes  
**Platform:** [Google Colab](https://colab.research.google.com)  
**Prerequisites:** Basic Python knowledge, Google account  
**Objective:** Learn to connect ChatGPT 5 to MCP (Model Context Protocol) servers for extended functionality

---

## Learning Objectives
By the end of this lab, you will be able to:
- Understand what MCP servers are and their business applications
- Connect ChatGPT 5 to external MCP servers
- Use MCP servers for business calculations and automation
- Build workflows that leverage external server capabilities

---

## What are MCP Servers?
MCP (Model Context Protocol) servers are external services that extend ChatGPT's capabilities by providing:
- Specialized calculations and processing
- Access to external databases and APIs
- Custom business logic execution
- Real-time data processing

Think of them as "plugins" that give ChatGPT superpowers for specific tasks!

---

## Part 1: Setup (2 minutes)

### Step 1: Create Your Notebook
1. Go to [https://colab.research.google.com](https://colab.research.google.com)
2. Click **"New Notebook"**
3. Rename to `ChatGPT_5_MCP_Servers_Lab`

### Step 2: Install and Import
```python
# Install OpenAI library
!pip install openai --quiet

# Import what we need
from openai import OpenAI
from getpass import getpass
import json
```

### Step 3: Initialize Client
```python
# Enter your API key securely
api_key = getpass('Enter your OpenAI API key: ')

# Create the client
client = OpenAI(api_key=api_key)

print("✅ Ready to connect to MCP servers!")
```

---

## Part 2: Basic MCP Server Connection (5 minutes)

### Step 4: Your First MCP Connection
Let's start with a simple dice roller MCP server (useful for probability calculations):

```python
# Connect to a dice rolling MCP server
response = client.responses.create(
    model="gpt-5",
    tools=[
        {
            "type": "mcp",
            "server_label": "dmcp",
            "server_description": "A probability and randomization server for business simulations",
            "server_url": "https://dmcp-server.deno.dev/sse",
            "require_approval": "never",
        },
    ],
    input="Roll 2d6 to simulate two market scenarios"
)

print("🎲 Market Scenario Simulation:")
print(response.output_text)
print("\n" + "="*50 + "\n")
```

**What's happening:**
- `type: "mcp"` specifies we're using an MCP server
- `server_url` points to the external service
- `require_approval: "never"` means automatic execution
- The server processes the request and returns results

### Step 5: Business Probability Calculations
Use the dice server for business probability scenarios:

```python
def simulate_sales_scenarios(num_scenarios=3):
    """
    Simulate different sales outcome scenarios
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Probability simulation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Roll 3d20 {num_scenarios} times to simulate sales performance scenarios.
        
        Interpret the results as:
        - 3-20: Poor performance (below target)
        - 21-40: Average performance (meets target)  
        - 41-60: Excellent performance (exceeds target)
        
        Provide a brief analysis of each scenario."""
    )
    return response.output_text

# Run sales simulations
print("📊 Sales Performance Scenarios:")
print(simulate_sales_scenarios())
print("\n" + "="*50 + "\n")
```

---

## Part 3: Business Applications with MCP (5 minutes)

### Step 6: Risk Assessment Calculator
Create a risk assessment tool using probability calculations:

```python
def assess_project_risk(num_risks, complexity_level):
    """
    Assess project risk using probability simulations
    
    Args:
        num_risks: Number of risk factors
        complexity_level: Project complexity (1-10)
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Risk calculation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Assess project risk:
        - Roll {num_risks}d10 for individual risk factors
        - Add {complexity_level} to the total
        
        Interpret the total score:
        - Below 20: Low risk
        - 20-40: Medium risk
        - 41-60: High risk
        - Above 60: Critical risk
        
        Provide risk mitigation recommendations based on the result."""
    )
    return response.output_text

# Assess a complex project
print("⚠️ Project Risk Assessment:")
print(assess_project_risk(num_risks=5, complexity_level=7))
print("\n" + "="*50 + "\n")
```

### Step 7: A/B Testing Simulator
Simulate A/B test results for marketing campaigns:

```python
def simulate_ab_test(test_name, sample_size_factor=100):
    """
    Simulate A/B test results for business decisions
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "A/B testing simulation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Simulate an A/B test for: {test_name}
        
        1. Roll 1d100 for Version A conversion rate
        2. Roll 1d100 for Version B conversion rate
        3. Roll 2d20 for confidence level
        
        Calculate:
        - Which version performed better
        - The percentage difference
        - Statistical significance (confidence > 30 = significant)
        - Business recommendation"""
    )
    return response.output_text

# Test different marketing campaigns
campaigns = [
    "Email subject lines",
    "Landing page designs",
    "Pricing strategies"
]

for campaign in campaigns:
    print(f"🔬 A/B Test: {campaign}")
    print(simulate_ab_test(campaign))
    print("-" * 40)

print("\n" + "="*50 + "\n")
```

---

## Part 4: Advanced MCP Server Usage (4 minutes)

### Step 8: Monte Carlo Simulations
Use MCP for complex business simulations:

```python
def monte_carlo_revenue_forecast(base_revenue, num_simulations=5):
    """
    Run Monte Carlo simulation for revenue forecasting
    
    Args:
        base_revenue: Current monthly revenue
        num_simulations: Number of scenarios to run
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Monte Carlo simulation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Run Monte Carlo simulation for revenue forecasting:
        
        Base monthly revenue: ${base_revenue:,}
        
        For {num_simulations} simulations:
        1. Roll 1d20-10 for market growth factor (%)
        2. Roll 1d10-5 for operational efficiency change (%)
        3. Roll 1d6-3 for competitive impact (%)
        
        Calculate projected revenue for each simulation.
        Provide:
        - Best case scenario
        - Worst case scenario
        - Most likely outcome
        - Recommended actions"""
    )
    return response.output_text

# Forecast revenue
print("📈 Monte Carlo Revenue Forecast:")
print(monte_carlo_revenue_forecast(base_revenue=250000))
print("\n" + "="*50 + "\n")
```

### Step 9: Decision Support System
Build a decision-making tool using probability:

```python
def make_business_decision(decision_type, options):
    """
    Help make business decisions using weighted probability
    
    Args:
        decision_type: Type of decision being made
        options: List of options to consider
    """
    options_text = "\n".join([f"- Option {i+1}: {opt}" for i, opt in enumerate(options)])
    
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Decision analysis server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Help make a {decision_type} decision:
        
        Options to evaluate:
        {options_text}
        
        For each option, roll:
        - 1d20 for potential ROI
        - 1d20 for implementation ease
        - 1d20 for risk level (lower is better)
        - 1d20 for strategic alignment
        
        Calculate a weighted score and recommend the best option with justification."""
    )
    return response.output_text

# Make a strategic decision
decision = make_business_decision(
    decision_type="Technology Investment",
    options=[
        "Upgrade existing CRM system",
        "Implement new AI chatbot",
        "Build custom analytics platform"
    ]
)

print("💡 Strategic Decision Analysis:")
print(decision)
print("\n" + "="*50 + "\n")
```

### Step 10: Custom Business Metrics
Create custom metric calculations:

```python
def calculate_business_kpis():
    """
    Calculate various business KPIs with variability
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "KPI calculation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input="""Calculate this quarter's projected KPIs with market variability:
        
        Roll the following to simulate market conditions:
        1. Customer Acquisition: Roll 3d20+40 (target: 100 new customers)
        2. Customer Retention: Roll 1d20+80 (percentage)
        3. Revenue per Customer: Roll 2d100+500 (dollars)
        4. Operating Margin: Roll 1d20+15 (percentage)
        5. Employee Satisfaction: Roll 1d20+70 (score out of 100)
        
        Provide:
        - Each KPI result
        - Overall business health assessment
        - Areas needing attention
        - Quick wins to pursue"""
    )
    return response.output_text

# Calculate KPIs
print("📊 Quarterly KPI Dashboard:")
print(calculate_business_kpis())
print("\n" + "="*50 + "\n")
```

---

## Part 5: MCP Server Configuration (4 minutes)

### Step 11: Server with Approval Requirements
Some MCP servers require approval for sensitive operations:

```python
def sensitive_operation_example():
    """
    Example of MCP server with approval requirements
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "secure_dmcp",
                "server_description": "Secure calculation server for sensitive operations",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "always",  # Requires approval for each operation
            },
        ],
        input="Calculate risk scores for confidential merger scenarios (roll 3d20 for each)"
    )
    return response.output_text

# Note: In production, this would prompt for user approval
print("🔒 Secure Operation Example:")
print("(In production, this would require user approval)")
# print(sensitive_operation_example())  # Uncomment to test
print("\n" + "="*50 + "\n")
```

### Step 12: Multiple MCP Servers
Connect to multiple servers simultaneously:

```python
def multi_server_analysis():
    """
    Use multiple MCP servers for comprehensive analysis
    """
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "prob_server",
                "server_description": "Probability calculations",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
            # In production, you could add more servers here
            # {
            #     "type": "mcp",
            #     "server_label": "data_server",
            #     "server_description": "Data analysis server",
            #     "server_url": "https://data-mcp.example.com/sse",
            #     "require_approval": "never",
            # },
        ],
        input="""Perform comprehensive business analysis:
        1. Use probability server to simulate 3 market scenarios (roll 3d20 each)
        2. Analyze the implications of each scenario
        3. Provide strategic recommendations"""
    )
    return response.output_text

print("🔄 Multi-Server Analysis:")
print(multi_server_analysis())
print("\n" + "="*50 + "\n")
```

---

## 🎯 Challenge: Connect Your Own MCP Server

Find and connect to a different MCP server for your specific business needs.

### Requirements:
1. Research available MCP servers (hint: check GitHub for "mcp-server" repositories)
2. Connect to a new MCP server
3. Create a business use case for it
4. Build a function that uses the server
5. Test with real business scenarios

### Starter Code:
```python
def connect_custom_mcp_server(server_url, server_label, description):
    """
    Connect to a custom MCP server
    
    Args:
        server_url: URL of the MCP server
        server_label: Short label for the server
        description: What the server does
    
    Returns:
        Response from the server
    """
    
    # TODO: Create the MCP server configuration
    # Hint: Look at the structure from previous examples
    
    # TODO: Send a test request to verify connection
    
    # TODO: Create a business use case
    
    # TODO: Return formatted results
    
    pass  # Replace with your code

# Popular MCP servers to try:
# - Weather MCP: https://weather-mcp.example.com/sse
# - Database MCP: https://db-mcp.example.com/sse
# - Calculation MCP: https://calc-mcp.example.com/sse
# - GitHub search for "mcp-server" for more options

# Test your connection
# custom_server = connect_custom_mcp_server(
#     server_url="YOUR_SERVER_URL",
#     server_label="custom_server",
#     description="Your server description"
# )
# print(custom_server)
```

### 💡 Hints:
- Search GitHub for "mcp-server" repositories
- Look for servers with `/sse` endpoints
- Check the server's documentation for input format
- Consider servers for: calculations, data fetching, or API integrations
- Test with simple requests first, then build complexity

### Resources for Finding MCP Servers:
- **GitHub Search**: [https://github.com/search?q=mcp-server](https://github.com/search?q=mcp-server)
- **MCP Protocol Docs**: [https://modelcontextprotocol.io](https://modelcontextprotocol.io)
- **Community Servers**: Search for "Model Context Protocol servers"

---

## Complete Solution Code

```python
# ============================================
# COMPLETE CODE FOR CHATGPT 5 MCP SERVERS LAB
# ============================================

# SETUP
# ---------------------------------------------
# !pip install openai --quiet

from openai import OpenAI
from getpass import getpass
import json

# Initialize client
api_key = getpass('Enter your OpenAI API key: ')
client = OpenAI(api_key=api_key)
print("✅ Ready to connect to MCP servers!")

# BASIC MCP CONNECTION
# ---------------------------------------------

# First MCP connection
response = client.responses.create(
    model="gpt-5",
    tools=[
        {
            "type": "mcp",
            "server_label": "dmcp",
            "server_description": "A probability and randomization server for business simulations",
            "server_url": "https://dmcp-server.deno.dev/sse",
            "require_approval": "never",
        },
    ],
    input="Roll 2d6 to simulate two market scenarios"
)
print("🎲 Market Scenario Simulation:")
print(response.output_text)
print("\n" + "="*50 + "\n")

# BUSINESS FUNCTIONS
# ---------------------------------------------

def simulate_sales_scenarios(num_scenarios=3):
    """Simulate different sales outcome scenarios"""
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Probability simulation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Roll 3d20 {num_scenarios} times to simulate sales performance scenarios.
        Interpret the results as:
        - 3-20: Poor performance (below target)
        - 21-40: Average performance (meets target)  
        - 41-60: Excellent performance (exceeds target)
        Provide a brief analysis of each scenario."""
    )
    return response.output_text

def assess_project_risk(num_risks, complexity_level):
    """Assess project risk using probability simulations"""
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Risk calculation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Assess project risk:
        - Roll {num_risks}d10 for individual risk factors
        - Add {complexity_level} to the total
        Interpret the total score:
        - Below 20: Low risk
        - 20-40: Medium risk
        - 41-60: High risk
        - Above 60: Critical risk
        Provide risk mitigation recommendations based on the result."""
    )
    return response.output_text

def simulate_ab_test(test_name, sample_size_factor=100):
    """Simulate A/B test results for business decisions"""
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "A/B testing simulation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Simulate an A/B test for: {test_name}
        1. Roll 1d100 for Version A conversion rate
        2. Roll 1d100 for Version B conversion rate
        3. Roll 2d20 for confidence level
        Calculate which version performed better, percentage difference, and business recommendation."""
    )
    return response.output_text

def monte_carlo_revenue_forecast(base_revenue, num_simulations=5):
    """Run Monte Carlo simulation for revenue forecasting"""
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Monte Carlo simulation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Run Monte Carlo simulation for revenue forecasting:
        Base monthly revenue: ${base_revenue:,}
        For {num_simulations} simulations:
        1. Roll 1d20-10 for market growth factor (%)
        2. Roll 1d10-5 for operational efficiency change (%)
        3. Roll 1d6-3 for competitive impact (%)
        Calculate projected revenue for each simulation and provide analysis."""
    )
    return response.output_text

def make_business_decision(decision_type, options):
    """Help make business decisions using weighted probability"""
    options_text = "\n".join([f"- Option {i+1}: {opt}" for i, opt in enumerate(options)])
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "Decision analysis server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input=f"""Help make a {decision_type} decision:
        Options to evaluate:
        {options_text}
        For each option, roll dice for ROI, implementation ease, risk, and strategic alignment.
        Calculate weighted score and recommend the best option."""
    )
    return response.output_text

def calculate_business_kpis():
    """Calculate various business KPIs with variability"""
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "dmcp",
                "server_description": "KPI calculation server",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input="""Calculate this quarter's projected KPIs with market variability:
        Roll dice for: Customer Acquisition, Retention, Revenue per Customer, Operating Margin, Employee Satisfaction.
        Provide each KPI result and overall business health assessment."""
    )
    return response.output_text

def multi_server_analysis():
    """Use multiple MCP servers for comprehensive analysis"""
    response = client.responses.create(
        model="gpt-5",
        tools=[
            {
                "type": "mcp",
                "server_label": "prob_server",
                "server_description": "Probability calculations",
                "server_url": "https://dmcp-server.deno.dev/sse",
                "require_approval": "never",
            },
        ],
        input="""Perform comprehensive business analysis:
        1. Use probability server to simulate 3 market scenarios (roll 3d20 each)
        2. Analyze the implications of each scenario
        3. Provide strategic recommendations"""
    )
    return response.output_text

# CHALLENGE SOLUTION
# ---------------------------------------------

def connect_custom_mcp_server(server_url, server_label, description):
    """Connect to a custom MCP server"""
    try:
        # Create the MCP server configuration
        response = client.responses.create(
            model="gpt-5",
            tools=[
                {
                    "type": "mcp",
                    "server_label": server_label,
                    "server_description": description,
                    "server_url": server_url,
                    "require_approval": "never",
                },
            ],
            input="Test connection: perform a simple calculation or operation"
        )
        
        # Return formatted results
        return f"✅ Successfully connected to {server_label}\n{response.output_text}"
    
    except Exception as e:
        return f"❌ Failed to connect: {str(e)}"

# TEST ALL FUNCTIONS
# ---------------------------------------------

print("Testing Sales Scenarios...")
print(simulate_sales_scenarios())
print("\n" + "="*50 + "\n")

print("Testing Risk Assessment...")
print(assess_project_risk(num_risks=5, complexity_level=7))
print("\n" + "="*50 + "\n")

print("Testing A/B Test Simulation...")
print(simulate_ab_test("Email subject lines"))
print("\n" + "="*50 + "\n")

print("Testing Monte Carlo Forecast...")
print(monte_carlo_revenue_forecast(base_revenue=250000))
print("\n" + "="*50 + "\n")

print("Testing Business Decision...")
decision = make_business_decision(
    decision_type="Technology Investment",
    options=[
        "Upgrade existing CRM system",
        "Implement new AI chatbot",
        "Build custom analytics platform"
    ]
)
print(decision)
print("\n" + "="*50 + "\n")

print("Testing KPI Calculation...")
print(calculate_business_kpis())
print("\n" + "="*50 + "\n")

print("Testing Multi-Server Analysis...")
print(multi_server_analysis())

print("\n✅ All MCP server functions tested successfully!")
```

---

## Troubleshooting

**Connection refused:**
- Verify the server URL is correct and includes `/sse`
- Check if the server is publicly accessible
- Ensure your API key has MCP permissions

**No response from server:**
- Check the input format matches what the server expects
- Verify the server is operational
- Try simpler requests first

**Approval required errors:**
- Change `require_approval` to "never" for testing
- In production, implement proper approval workflows

---

## Resources

- **OpenAI Platform**: [https://platform.openai.com](https://platform.openai.com)
- **API Documentation**: [https://platform.openai.com/docs](https://platform.openai.com/docs)
- **MCP Protocol**: [https://modelcontextprotocol.io](https://modelcontextprotocol.io)
- **GitHub MCP Servers**: [https://github.com/search?q=mcp-server](https://github.com/search?q=mcp-server)
- **API Keys**: [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Google Colab**: [https://colab.research.google.com](https://colab.research.google.com)

---

## Lab Checklist

- [ ] Set up OpenAI client
- [ ] Connected to first MCP server
- [ ] Created business probability simulations
- [ ] Built risk assessment tool
- [ ] Implemented A/B testing simulator
- [ ] Created decision support system
- [ ] Explored server configuration options
- [ ] Attempted to connect custom MCP server

---

## Key Takeaways

You've learned to:
- 🔌 Connect ChatGPT 5 to MCP servers
- 🎲 Use external servers for calculations
- 📊 Build business analysis tools
- 🔄 Integrate multiple servers
- 🚀 Extend ChatGPT's capabilities

**Congratulations!** 🎉 You can now enhance ChatGPT 5 with external MCP servers for powerful business applications!