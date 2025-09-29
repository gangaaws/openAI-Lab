# OpenAI ChatGPT 5 Multimodal API Lab Guide

## Lab Overview
**Duration:** 15-20 minutes  
**Platform:** [Google Colab](https://colab.research.google.com)  
**Prerequisites:** Basic Python knowledge, Google account  
**Objective:** Learn to use ChatGPT 5 with images and files

---

## Learning Objectives
By the end of this lab, you will be able to:
- Send images to ChatGPT 5 for analysis
- Process PDF documents from URLs
- Upload your own files to analyze
- Combine text and images in one request

---

## Part 1: Setup (2 minutes)

### Step 1: Create Your Notebook
1. Go to [https://colab.research.google.com](https://colab.research.google.com)
2. Click **"New Notebook"**
3. Rename it to `ChatGPT_5_Images_Files_Lab`

### Step 2: Install and Import
```python
# Install OpenAI library
!pip install openai --quiet

# Import what we need
from openai import OpenAI
from getpass import getpass
```

### Step 3: Add Your API Key
```python
# Enter your API key securely
api_key = getpass('Enter your OpenAI API key: ')

# Create the client
client = OpenAI(api_key=api_key)

print("✅ Ready to go!")
```

---

## Part 2: Working with Images (5 minutes)

### Step 4: Your First Image Analysis
Let's ask ChatGPT 5 to describe what's in an image:

```python
# Analyze an image from the web
response = client.responses.create(
    model="gpt-5",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "What do you see in this image? Describe it in 2-3 sentences."
                },
                {
                    "type": "input_image",
                    "image_url": "https://upload.wikimedia.org/wikipedia/commons/3/3b/LeBron_James_Layup_%28Cleveland_vs_Brooklyn_2018%29.jpg"
                }
            ]
        }
    ]
)

print("ChatGPT 5 sees:")
print(response.output_text)
```

**What's happening:**
- We send both text (our question) and an image URL
- ChatGPT 5 analyzes the image and responds
- The image must be publicly accessible on the internet

### Step 5: Ask Specific Questions About Images
Now let's ask something specific:

```python
# Ask a specific question about the image
def analyze_image(image_url, question):
    """
    Ask ChatGPT 5 a question about an image
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": question
                    },
                    {
                        "type": "input_image",
                        "image_url": image_url
                    }
                ]
            }
        ]
    )
    return response.output_text

# Try it with a food image
food_image = "https://images.unsplash.com/photo-1565299624946-b28f40a0ae38"
question = "What ingredients can you identify on this pizza?"

result = analyze_image(food_image, question)
print("🍕 Ingredients found:")
print(result)
```

### Step 6: Practical Example - Reading Signs and Text
Let's read text from images (super useful!):

```python
# Read text from a street sign or poster
sign_image = "https://images.unsplash.com/photo-1502920917128-1aa500764cbd"

response = client.responses.create(
    model="gpt-5",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "What text can you see in this image? Just list the text you can read."
                },
                {
                    "type": "input_image",
                    "image_url": sign_image
                }
            ]
        }
    ]
)

print("📝 Text found in image:")
print(response.output_text)
```

---

## Part 3: Working with Documents (5 minutes)

### Step 7: Analyze a PDF from URL
Let's analyze Warren Buffett's famous letter to shareholders:

```python
# Analyze a document from a URL
response = client.responses.create(
    model="gpt-5",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "What are the 3 main points from this letter? Keep it simple."
                },
                {
                    "type": "input_file",
                    "file_url": "https://www.berkshirehathaway.com/letters/2023ltr.pdf"
                }
            ]
        }
    ]
)

print("📄 Main points from the letter:")
print(response.output_text)
```

**Note:** This works with any publicly accessible PDF URL!

### Step 8: Create a Simple Document Summarizer
Let's make a reusable function:

```python
def summarize_document(pdf_url, num_points=3):
    """
    Get key points from any PDF document
    
    Args:
        pdf_url: Link to the PDF
        num_points: How many key points to extract
    """
    prompt = f"Give me the {num_points} most important points from this document. Use simple language."
    
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": prompt
                    },
                    {
                        "type": "input_file",
                        "file_url": pdf_url
                    }
                ]
            }
        ]
    )
    return response.output_text

# Try it with any PDF
test_pdf = "https://www.berkshirehathaway.com/letters/2023ltr.pdf"
summary = summarize_document(test_pdf, 5)

print("📋 Document Summary:")
print(summary)
```

---

## Part 4: Uploading Your Own Files (4 minutes)

### Step 9: Create a Simple File to Upload
First, let's create a text file to work with:

```python
# Create a simple story file
story = """
The Magic Bakery

Once upon a time, there was a bakery where the bread could talk.
Every morning, the croissants would greet customers in French.
The donuts would tell jokes to make people smile.
And the birthday cakes would sing happy birthday!

One day, a sad little girl came in.
The bread asked her what was wrong.
She said she had no friends at her new school.
So all the pastries threw her a party right there in the bakery!

From that day on, she visited every week.
And she was never lonely again.

The End.
"""

# Save it as a file
with open('magic_bakery.txt', 'w') as f:
    f.write(story)

print("✅ Created story file: magic_bakery.txt")
```

### Step 10: Upload and Analyze Your File
Now let's upload it and ask questions:

```python
# Upload the file to OpenAI
file = client.files.create(
    file=open("magic_bakery.txt", "rb"),
    purpose="user_data"
)

print(f"📤 File uploaded! ID: {file.id}")

# Now ask a question about it
response = client.responses.create(
    model="gpt-5",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_file",
                    "file_id": file.id
                },
                {
                    "type": "input_text",
                    "text": "What made the little girl happy in this story?"
                }
            ]
        }
    ]
)

print("\n💭 ChatGPT's answer:")
print(response.output_text)
```

### Step 11: Upload and Analyze a PDF
Let's create a simple function to handle any file:

```python
def upload_and_ask(file_path, question):
    """
    Upload a file and ask a question about it
    
    Args:
        file_path: Path to your file
        question: What you want to know
    """
    # Upload the file
    with open(file_path, 'rb') as f:
        uploaded = client.files.create(
            file=f,
            purpose="user_data"
        )
    
    # Ask the question
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_file",
                        "file_id": uploaded.id
                    },
                    {
                        "type": "input_text",
                        "text": question
                    }
                ]
            }
        ]
    )
    
    return response.output_text

# Test it with our story
answer = upload_and_ask('magic_bakery.txt', 'How many characters are in this story?')
print("Answer:", answer)
```

---

## Part 5: Combining Everything (4 minutes)

### Step 12: Image + Text Together
Let's build something fun - a recipe helper!

```python
def get_recipe_from_food_image(image_url):
    """
    Look at food and suggest how to make it
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": """Look at this food and give me:
                        1. What dish is this?
                        2. Three main ingredients I'd need
                        3. How long it might take to make
                        Keep it simple!"""
                    },
                    {
                        "type": "input_image",
                        "image_url": image_url
                    }
                ]
            }
        ]
    )
    return response.output_text

# Try with a pasta image
pasta = "https://images.unsplash.com/photo-1621996346565-e3dbc646d9a9"
recipe_help = get_recipe_from_food_image(pasta)

print("🍝 Recipe Helper:")
print(recipe_help)
```

---

## 🎯 Your Turn: Build a Study Buddy

Now it's time to practice! Create a simple study helper that can:

1. **Read images of notes or textbook pages**
2. **Answer questions about them**
3. **Make it conversational and helpful**

### Challenge Requirements:
- Create a function called `study_buddy()`
- It should take an image URL and a question
- It should give helpful, encouraging responses
- Add error handling (what if the image doesn't load?)

### Starter Code:
```python
def study_buddy(image_url, question):
    """
    A helpful study assistant that reads your notes
    
    Args:
        image_url: URL of your notes/textbook image
        question: What you want to know
    
    Returns:
        A helpful, encouraging response
    """
    
    # TODO: Create a friendly prompt that:
    # - Answers the question
    # - Encourages the student
    # - Keeps it simple
    
    # TODO: Make the API call
    
    # TODO: Add try/except for errors
    
    pass  # Replace this with your code!

# Test your study buddy
# notes_image = "URL_to_an_image_of_notes"
# help_me = study_buddy(notes_image, "What are the main points here?")
# print(help_me)
```

### 💡 Hints:
- Make your prompt friendly: "Hi! I'm your study buddy..."
- Add encouragement: "Great question!" or "You're doing great!"
- Handle errors gracefully: "Oops, I couldn't read that image. Can you try another?"

### 🌟 Bonus (Optional):
If you finish early, try adding:
1. **Quiz Mode**: Have it ask YOU a question about the material
2. **Summary Mode**: Automatically create study notes
3. **Multiple Images**: Handle more than one image at once

---

## Testing Your Code

### Test Images You Can Use:
```python
# Here are some test images that work well:
test_images = {
    "basketball": "https://upload.wikimedia.org/wikipedia/commons/3/3b/LeBron_James_Layup_%28Cleveland_vs_Brooklyn_2018%29.jpg",
    "food": "https://images.unsplash.com/photo-1565299624946-b28f40a0ae38",
    "nature": "https://images.unsplash.com/photo-1441974231531-c6227db76b6e",
    "city": "https://images.unsplash.com/photo-1449824913935-59a10b8d2000",
    "book": "https://images.unsplash.com/photo-1481627834876-b7833e8f5570"
}

# Test PDFs that work:
test_pdfs = {
    "berkshire": "https://www.berkshirehathaway.com/letters/2023ltr.pdf",
    # Add other public PDFs you find
}
```

---

## Troubleshooting

**"Image not found" error:**
- Make sure the URL is public (not from Google Drive or private sites)
- Try opening the URL in a new browser tab first

**"File too large" error:**
- PDFs should be under 20MB
- Images should be under 10MB

**"API key invalid" error:**
- Double-check you copied your entire API key
- Make sure there are no extra spaces

---

## Resources

- **OpenAI Platform**: [https://platform.openai.com](https://platform.openai.com)
- **API Keys**: [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Documentation**: [https://platform.openai.com/docs](https://platform.openai.com/docs)
- **Google Colab Help**: [https://colab.research.google.com/notebooks/welcome.ipynb](https://colab.research.google.com/notebooks/welcome.ipynb)

---

## Lab Checklist

- [ ] Set up OpenAI client
- [ ] Analyzed an image successfully
- [ ] Read a PDF from a URL
- [ ] Uploaded your own file
- [ ] Combined image and text in one request
- [ ] Built the Study Buddy function
- [ ] Tested with at least 2 different images

---

## What You Learned

Congratulations! You can now:
- 🖼️ Send images to ChatGPT 5 for analysis
- 📄 Process documents from the web
- 📤 Upload and analyze your own files
- 🎯 Build practical applications with multimodal AI

**Great job!** 🎉 You're ready to build cool projects with ChatGPT 5's multimodal features!