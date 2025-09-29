# OpenAI ChatGPT 5 API Lab Guide

## Lab Overview
**Duration:** 15-20 minutes  
**Platform:** [Google Colab](https://colab.research.google.com)  
**Prerequisites:** Basic Python knowledge, Google account  
**Objective:** Learn to interact with OpenAI's ChatGPT 5 API to generate text responses

---

## Learning Objectives
By the end of this lab, you will be able to:
- Set up OpenAI API authentication in Google Colab
- Make basic API calls to ChatGPT 5
- Handle API responses and extract text
- Customize parameters for different outputs
- Implement error handling for API calls

---

## Part 1: Environment Setup (3 minutes)

### Step 1: Open Google Colab
1. Navigate to [https://colab.research.google.com](https://colab.research.google.com)
2. Click on **"New Notebook"** to create a fresh workspace
3. Rename your notebook to `ChatGPT_5_API_Lab` by clicking on the title

### Step 2: Install Required Libraries
In the first cell, install the OpenAI Python library:

```python
# Install the OpenAI library
!pip install openai --quiet

# Import necessary libraries
import os
from openai import OpenAI
import json
```

**Explanation:** The `--quiet` flag suppresses verbose installation output. We import `os` for environment variables and `json` for handling structured data.

### Step 3: Set Up API Authentication
Create a new cell and add your API key securely:

```python
# Secure way to add your API key in Colab
from getpass import getpass

# This will create a password field to enter your API key securely
api_key = getpass('Enter your OpenAI API key: ')

# Initialize the OpenAI client
client = OpenAI(api_key=api_key)

print("✅ OpenAI client initialized successfully!")
```

**Explanation:** Using `getpass` hides your API key from being displayed in the notebook, maintaining security.

---

## Part 2: Basic API Calls (5 minutes)

### Step 4: Your First ChatGPT 5 Request
Create a simple text generation request:

```python
# Basic text generation example
response = client.responses.create(
    model="gpt-5",
    input="Write a one-sentence bedtime story about a unicorn."
)

# Display the generated text
print("Generated Story:")
print(response.output_text)
```

**Explanation:** The `responses.create()` method sends your input to the model and returns a response object containing the generated text.

### Step 5: Understanding Response Structure
Explore the response object:

```python
# Examine the full response object
print("Response Type:", type(response))
print("\nAvailable Attributes:")
print(dir(response))

# Access specific response metadata
if hasattr(response, 'usage'):
    print("\nToken Usage:")
    print(f"  Input tokens: {response.usage.input_tokens}")
    print(f"  Output tokens: {response.usage.output_tokens}")
    print(f"  Total tokens: {response.usage.total_tokens}")
```

**Explanation:** Understanding the response structure helps you extract additional information like token usage, which is important for cost management.

---

## Part 3: Advanced Parameters (5 minutes)

### Step 6: Customizing Generation Parameters
Experiment with different parameters to control output:

```python
# Advanced parameters example
def generate_text(prompt, temperature=0.7, max_tokens=150, top_p=1.0):
    """
    Generate text with customizable parameters
    
    Args:
        prompt: Input text for the model
        temperature: Controls randomness (0.0 to 2.0)
        max_tokens: Maximum length of generated text
        top_p: Nucleus sampling parameter
    """
    try:
        response = client.responses.create(
            model="gpt-5",
            input=prompt,
            temperature=temperature,
            max_tokens=max_tokens,
            top_p=top_p
        )
        return response.output_text
    except Exception as e:
        return f"Error: {str(e)}"

# Test with different temperatures
prompt = "Explain quantum computing in simple terms:"

print("🌡️ Low Temperature (0.3) - More Focused:")
print(generate_text(prompt, temperature=0.3))
print("\n" + "="*50 + "\n")

print("🌡️ High Temperature (1.5) - More Creative:")
print(generate_text(prompt, temperature=1.5))
```

**Explanation:** 
- **Temperature** controls randomness (0 = deterministic, 2 = very random)
- **max_tokens** limits response length
- **top_p** implements nucleus sampling for better quality

### Step 7: Batch Processing
Process multiple prompts efficiently:

```python
# Batch processing example
prompts = [
    "Write a haiku about coding",
    "Create a recipe title for a futuristic dish",
    "Suggest a name for a friendly robot"
]

results = []

for i, prompt in enumerate(prompts, 1):
    print(f"Processing prompt {i}/{len(prompts)}...")
    
    response = client.responses.create(
        model="gpt-5",
        input=prompt,
        temperature=0.8,
        max_tokens=50
    )
    
    results.append({
        "prompt": prompt,
        "response": response.output_text
    })

# Display results
print("\n📊 Batch Processing Results:\n")
for result in results:
    print(f"Prompt: {result['prompt']}")
    print(f"Response: {result['response']}")
    print("-" * 40)
```

---

## Part 4: Error Handling & Best Practices (3 minutes)

### Step 8: Implement Robust Error Handling
Create a production-ready function with error handling:

```python
import time

def safe_generate(prompt, retries=3, delay=1):
    """
    Generate text with retry logic and error handling
    
    Args:
        prompt: Input text
        retries: Number of retry attempts
        delay: Delay between retries (seconds)
    """
    for attempt in range(retries):
        try:
            response = client.responses.create(
                model="gpt-5",
                input=prompt,
                temperature=0.7,
                max_tokens=100
            )
            return {
                "success": True,
                "text": response.output_text,
                "attempt": attempt + 1
            }
        except Exception as e:
            print(f"Attempt {attempt + 1} failed: {str(e)}")
            if attempt < retries - 1:
                print(f"Retrying in {delay} seconds...")
                time.sleep(delay)
            else:
                return {
                    "success": False,
                    "error": str(e),
                    "attempts": retries
                }

# Test the function
result = safe_generate("What are three benefits of cloud computing?")

if result["success"]:
    print("✅ Generation successful!")
    print(f"Response: {result['text']}")
else:
    print("❌ Generation failed after all retries")
    print(f"Error: {result['error']}")
```

---

## Part 5: Challenge Section (4-5 minutes)

### 🎯 Your Turn: Build a Creative Writing Assistant

Now it's your time to apply what you've learned! Create a function that acts as a creative writing assistant with the following requirements:

**Challenge Requirements:**
1. Create a function called `creative_assistant()` that:
   - Takes a writing style (e.g., "poetic", "technical", "humorous")
   - Takes a topic from the user
   - Generates content in the specified style
   
2. Implement at least 3 different writing styles with different temperature settings

3. Add a word count feature that ensures the output is within a specified range

4. Create a simple interactive loop that lets users generate multiple pieces

**Starter Code:**
```python
def creative_assistant(topic, style="neutral", min_words=50, max_words=100):
    """
    Your creative writing assistant
    
    Args:
        topic: What to write about
        style: Writing style (poetic/technical/humorous/neutral)
        min_words: Minimum word count
        max_words: Maximum word count
    
    Returns:
        Generated text in the specified style
    """
    
    # TODO: Define style parameters (temperature, prompts)
    styles = {
        "poetic": {
            "temperature": 1.2,
            "prefix": "Write poetically about: "
        },
        # Add more styles here
    }
    
    # TODO: Implement the generation logic
    
    # TODO: Check word count and regenerate if needed
    
    pass  # Replace with your implementation

# Test your function
# result = creative_assistant("artificial intelligence", style="poetic")
# print(result)
```

### 🌟 Bonus Challenges:
1. **Style Mixer**: Combine two styles (e.g., "technical-humorous")
2. **Sentiment Control**: Add positive/negative sentiment parameters
3. **Format Options**: Support different formats (paragraph, bullet points, dialogue)
4. **Save & Export**: Save all generated content to a JSON file with metadata

---

## Expected Solution Approach

For the challenge section, a good solution should:
- Use different temperature values for each style (poetic: 1.2-1.5, technical: 0.3-0.5, humorous: 0.8-1.0)
- Include style-specific prompt engineering
- Implement word count validation using `len(text.split())`
- Handle edge cases and API errors gracefully

---

## Resources & Further Learning

- **OpenAI Documentation**: [https://platform.openai.com/docs](https://platform.openai.com/docs)
- **API Reference**: [https://platform.openai.com/docs/api-reference](https://platform.openai.com/docs/api-reference)
- **Best Practices**: [https://platform.openai.com/docs/guides/best-practices](https://platform.openai.com/docs/guides/best-practices)
- **Rate Limits**: [https://platform.openai.com/docs/guides/rate-limits](https://platform.openai.com/docs/guides/rate-limits)
- **Google Colab Tips**: [https://colab.research.google.com/notebooks/basic_features_overview.ipynb](https://colab.research.google.com/notebooks/basic_features_overview.ipynb)

---

## Lab Completion Checklist

- [ ] Successfully installed OpenAI library
- [ ] Configured API authentication
- [ ] Made first API call to ChatGPT 5
- [ ] Experimented with temperature settings
- [ ] Implemented error handling
- [ ] Completed the creative assistant challenge
- [ ] (Bonus) Attempted at least one bonus challenge

---

## Submission Guidelines

1. Save your completed Colab notebook
2. Download as `.ipynb` file: File → Download → Download .ipynb
3. Ensure all cells have been run and show output
4. Include comments explaining your challenge solution
5. Submit your notebook file through the course platform

---

**Congratulations!** 🎉 You've successfully completed the ChatGPT 5 API Lab. You now have the foundation to build powerful AI-powered applications!
