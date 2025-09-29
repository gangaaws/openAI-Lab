# Lab Title: Build Your First AI-Powered Image Analysis API in 60 Minutes

## (5 Mins) 1. The Big Picture

### Learning Objectives
After completing this lab, you will be able to:
- **Implement** the OpenAI Responses API to analyze images from URLs
- **Configure** multimodal prompts combining text and visual inputs
- **Deploy** a working image analysis solution for real-world business scenarios
- **Extend** the basic implementation to handle custom analysis requirements

### Business Scenario
You're a developer at TravelTech Inc., a fast-growing travel review platform. Your product team has identified that 65% of user-submitted reviews include photos, but these images are not being utilized for insights. Your task is to build a proof-of-concept API integration that can automatically analyze review photos to extract valuable information like landmarks, food quality, room conditions, and safety concerns. This will help your company provide better search filters, automated tagging, and quality control for user-generated content.

### Prerequisites & Setup
- **Google Account**: Required for Google Colab access
- **OpenAI API Key**: Sign up at [platform.openai.com](https://platform.openai.com) if you don't have one
- **Google Colab**: Open [colab.research.google.com](https://colab.research.google.com) and create a new notebook

**Initial Setup Cell** (Run this first):
```python
# Cell 1: Install required packages and set up environment
!pip install openai python-dotenv pillow requests -q

import os
from getpass import getpass

# Securely input your API key
api_key = getpass('Enter your OpenAI API Key: ')
os.environ['OPENAI_API_KEY'] = api_key

print("✅ Environment configured successfully!")
print("🚀 Ready to start building your image analyzer!")
```

## (30 Mins) 2. Guided Walkthrough: Building the Foundation

### Step 1: Setting Up Your API Client and Helper Functions (5 mins)

**Why**: Before we can analyze images, we need to establish a secure connection to the OpenAI API and create helper functions to display images in Colab. This foundation will handle all our authentication and visualization needs.

```python
# Cell 2: Initialize OpenAI client and create helper functions
from openai import OpenAI
import requests
from PIL import Image
from io import BytesIO
import matplotlib.pyplot as plt

# Initialize the OpenAI client
client = OpenAI(api_key=os.environ['OPENAI_API_KEY'])

def display_image(url, title="Image"):
    """Helper function to display images in Colab"""
    try:
        response = requests.get(url)
        img = Image.open(BytesIO(response.content))
        plt.figure(figsize=(10, 6))
        plt.imshow(img)
        plt.axis('off')
        plt.title(title)
        plt.show()
        return True
    except Exception as e:
        print(f"Could not display image: {e}")
        return False

# Test the setup
print("✅ OpenAI client initialized successfully!")
print("✅ Helper functions loaded!")

# Test with a sample image
test_url = "https://images.unsplash.com/photo-1566073771259-6a8506099945"
display_image(test_url, "Test Hotel Image")
```

**What's Happening**: 
- The `OpenAI` client handles all API communication with your secure key
- `display_image()` lets us see what images we're analyzing right in Colab
- We're using matplotlib to render images inline with our analysis results
- The test image confirms everything is working before we proceed

**Expected Output:**
- You should see a hotel/resort image displayed
- Confirmation messages that setup is complete

### Step 2: Analyzing Your First Travel Photo (8 mins)

**Why**: Now we'll implement the core functionality - sending an image URL to the API and receiving intelligent analysis. This demonstrates the power of multimodal AI models in understanding visual content.

```python
# Cell 3: Create the image analysis function
def analyze_hotel_photo(image_url, custom_prompt=None):
    """
    Analyze a hotel photo using OpenAI's multimodal capabilities
    
    Args:
        image_url: URL of the image to analyze
        custom_prompt: Optional custom analysis prompt
    
    Returns:
        Analysis text or None if error
    """
    try:
        # Default prompt for hotel analysis
        default_prompt = """Analyze this hotel photo for: 
        1. Room condition and cleanliness
        2. Visible amenities and features
        3. Overall quality assessment (budget/mid-range/luxury)
        4. Any potential concerns for travelers
        Please be specific and detailed."""
        
        response = client.responses.create(
            model="gpt-5",
            input=[
                {
                    "role": "user",
                    "content": [
                        {
                            "type": "input_text",
                            "text": custom_prompt or default_prompt
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
    except Exception as error:
        print(f"❌ Error analyzing image: {error}")
        return None

# Test with a real hotel room image
print("🔍 Analyzing hotel room image...")
test_url = "https://images.unsplash.com/photo-1611892440504-42a792e24d32"

# Display the image
display_image(test_url, "Hotel Room for Analysis")

# Analyze the image
analysis = analyze_hotel_photo(test_url)

# Display results
print("\n" + "="*50)
print("📸 IMAGE ANALYSIS RESULTS")
print("="*50)
print(analysis)
```

**What's Happening**:
- The `input` array combines two content types: text instructions and an image URL
- The model processes both inputs simultaneously to understand context
- We're using structured prompts to get consistent, detailed analysis
- Error handling ensures the notebook doesn't crash on API failures

**Expected Output:**
```
🔍 Analyzing hotel room image...
[Image of hotel room displayed]

==================================================
📸 IMAGE ANALYSIS RESULTS
==================================================
The hotel room appears to be in excellent condition with modern furnishings. 
1. Room condition: Clean and well-maintained with crisp white linens
2. Visible amenities: King-size bed, bedside lighting, modern decor, work area
3. Quality assessment: Mid-range to luxury category
4. Concerns: None visible - appears comfortable and safe for travelers
```

**Check Your Understanding**: 
Which API parameter allows us to send both text and images in a single request?
- a) The model parameter
- b) The input array with multiple content types
- c) The image_url field alone
- d) The role field

*Answer: b) The input array with multiple content types enables multimodal inputs*

### Step 3: Building a Batch Analysis System (10 mins)

**Why**: Travel reviews often include multiple photos. We'll create a system to analyze multiple images efficiently and compile insights into a structured report that businesses can act upon.

```python
# Cell 4: Create batch analysis system
import json
import time
from datetime import datetime

def analyze_travel_review(review_data):
    """
    Analyze multiple images from a travel review
    
    Args:
        review_data: Dictionary with 'id' and 'images' list
    
    Returns:
        Structured analysis results
    """
    results = {
        "reviewId": review_data["id"],
        "timestamp": datetime.now().isoformat(),
        "totalImages": len(review_data["images"]),
        "imageAnalyses": [],
        "summary": "",
        "overallQuality": ""
    }
    
    print(f"📋 Starting analysis for Review: {review_data['id']}")
    print(f"📸 Processing {len(review_data['images'])} images...\n")
    
    # Analyze each image
    for index, image_url in enumerate(review_data["images"], 1):
        print(f"🔍 Analyzing image {index}/{len(review_data['images'])}...")
        
        # Display the image
        display_image(image_url, f"Image {index}: {image_url.split('/')[-1][:20]}")
        
        # Analyze with specific prompts
        analysis = analyze_hotel_photo(
            image_url,
            """Identify: 
            1. Location type (hotel room/restaurant/pool/lobby/exterior)
            2. Three key features visible
            3. Quality level (1-10 scale)
            4. Any red flags or concerns"""
        )
        
        if analysis:
            results["imageAnalyses"].append({
                "imageNumber": index,
                "imageUrl": image_url,
                "analysis": analysis
            })
            print(f"✅ Image {index} analyzed successfully\n")
        
        # Rate limiting - be respectful to the API
        if index < len(review_data["images"]):
            time.sleep(2)
    
    # Generate overall summary
    print("📊 Generating summary insights...")
    summary_prompt = f"""Based on these {len(results['imageAnalyses'])} image analyses:
    {json.dumps([a['analysis'] for a in results['imageAnalyses']], indent=2)}
    
    Provide:
    1. Overall property assessment in one sentence
    2. Top 3 highlights across all images
    3. Overall quality rating (Budget/Mid-range/Luxury/Ultra-luxury)
    4. Main concern if any"""
    
    summary_response = client.responses.create(
        model="gpt-5",
        input=summary_prompt
    )
    
    results["summary"] = summary_response.output_text
    
    return results

# Test with multiple travel images
sample_review = {
    "id": "REVIEW_COLAB_001",
    "images": [
        "https://images.unsplash.com/photo-1566073771259-6a8506099945",  # Hotel exterior/pool
        "https://images.unsplash.com/photo-1590490360182-c33d57733427",  # Hotel room
        "https://images.unsplash.com/photo-1559599746-8823b38544c6"   # Restaurant/dining
    ]
}

# Run the analysis
full_analysis = analyze_travel_review(sample_review)

# Display formatted results
print("\n" + "="*60)
print("📊 COMPLETE REVIEW ANALYSIS REPORT")
print("="*60)
print(f"Review ID: {full_analysis['reviewId']}")
print(f"Timestamp: {full_analysis['timestamp']}")
print(f"Images Analyzed: {full_analysis['totalImages']}")
print("\n--- SUMMARY ---")
print(full_analysis['summary'])
print("="*60)
```

**What's Happening**:
- We iterate through multiple images, displaying each in Colab for verification
- Each image gets specific analysis prompts for consistency
- Rate limiting (2-second delay) prevents API throttling
- Results are structured for easy database storage or API responses
- Summary generation synthesizes insights across all images

**Expected Output:**
- Multiple hotel images displayed sequentially
- Progress indicators for each analysis step
- Comprehensive summary report with quality ratings and highlights

**Check Your Understanding**:
Why is rate limiting important when processing multiple API calls?
- a) To save money on API costs
- b) To prevent hitting API rate limits and ensure stable operation
- c) To make the code run slower
- d) To improve analysis accuracy

*Answer: b) To prevent hitting API rate limits and ensure stable operation*

### Step 4: Adding Business Logic and Insights Extraction (7 mins)

**Why**: Raw analysis is powerful, but businesses need actionable insights. We'll add logic to automatically categorize findings, calculate quality scores, and flag important items for immediate attention.

```python
# Cell 5: Business intelligence layer
def extract_business_insights(analysis_text):
    """
    Extract structured business insights from free-text analysis
    
    Args:
        analysis_text: Raw analysis from the AI model
    
    Returns:
        Dictionary of categorized insights
    """
    insights = {
        "qualityScore": 50,  # Start at neutral
        "categories": [],
        "warnings": [],
        "highlights": [],
        "amenities": [],
        "actionItems": []
    }
    
    # Convert to lowercase for checking
    lower_analysis = analysis_text.lower()
    
    # Quality scoring keywords (weighted)
    quality_indicators = {
        'positive': {
            'excellent': 15, 'luxury': 15, 'beautiful': 10,
            'clean': 10, 'modern': 10, 'spacious': 10,
            'well-maintained': 10, 'stunning': 12, 'pristine': 12
        },
        'negative': {
            'dirty': -20, 'damaged': -20, 'old': -10,
            'small': -10, 'broken': -25, 'unsafe': -30,
            'poor': -15, 'outdated': -10, 'cramped': -10
        }
    }
    
    # Calculate quality score
    for word, value in quality_indicators['positive'].items():
        if word in lower_analysis:
            insights['qualityScore'] += value
            insights['highlights'].append(f"✨ {word.capitalize()} condition noted")
    
    for word, value in quality_indicators['negative'].items():
        if word in lower_analysis:
            insights['qualityScore'] += value  # value is already negative
            insights['warnings'].append(f"⚠️ Issue detected: {word}")
            insights['actionItems'].append(f"Address {word} condition immediately")
    
    # Clamp score between 0-100
    insights['qualityScore'] = max(0, min(100, insights['qualityScore']))
    
    # Categorize by location/amenity type
    category_keywords = {
        'accommodation': ['room', 'bed', 'suite', 'bedroom'],
        'dining': ['restaurant', 'bar', 'breakfast', 'dining', 'food'],
        'wellness': ['pool', 'gym', 'spa', 'fitness'],
        'business': ['meeting', 'conference', 'business center', 'wifi'],
        'exterior': ['exterior', 'entrance', 'facade', 'building', 'grounds']
    }
    
    for category, keywords in category_keywords.items():
        if any(keyword in lower_analysis for keyword in keywords):
            insights['categories'].append(category)
    
    # Extract specific amenities mentioned
    amenity_keywords = ['pool', 'gym', 'spa', 'wifi', 'parking', 'restaurant', 
                       'bar', 'balcony', 'view', 'kitchen', 'minibar', 'safe']
    
    for amenity in amenity_keywords:
        if amenity in lower_analysis:
            insights['amenities'].append(amenity)
    
    # Determine urgency level for warnings
    if insights['warnings']:
        if any('unsafe' in w.lower() or 'broken' in w.lower() for w in insights['warnings']):
            insights['urgencyLevel'] = 'HIGH'
        else:
            insights['urgencyLevel'] = 'MEDIUM'
    else:
        insights['urgencyLevel'] = 'LOW'
    
    return insights

# Create a comprehensive dashboard
def create_analysis_dashboard(review_analysis):
    """
    Create a business dashboard from review analysis
    """
    print("\n" + "="*60)
    print("📊 BUSINESS INTELLIGENCE DASHBOARD")
    print("="*60)
    
    all_insights = []
    total_score = 0
    
    for img_analysis in review_analysis['imageAnalyses']:
        insights = extract_business_insights(img_analysis['analysis'])
        all_insights.append(insights)
        total_score += insights['qualityScore']
        
        print(f"\n📸 Image {img_analysis['imageNumber']}:")
        print(f"   Quality Score: {insights['qualityScore']}/100")
        print(f"   Categories: {', '.join(insights['categories']) if insights['categories'] else 'None identified'}")
        print(f"   Amenities: {', '.join(insights['amenities']) if insights['amenities'] else 'None identified'}")
        
        if insights['highlights']:
            print(f"   Highlights: {'; '.join(insights['highlights'][:3])}")
        
        if insights['warnings']:
            print(f"   ⚠️ WARNINGS: {'; '.join(insights['warnings'])}")
    
    # Calculate aggregate metrics
    avg_score = total_score / len(all_insights) if all_insights else 0
    all_warnings = [w for i in all_insights for w in i['warnings']]
    all_amenities = list(set([a for i in all_insights for a in i['amenities']]))
    
    print("\n" + "-"*60)
    print("📈 AGGREGATE METRICS")
    print("-"*60)
    print(f"Average Quality Score: {avg_score:.1f}/100")
    print(f"Quality Grade: {'A (Excellent)' if avg_score >= 80 else 'B (Good)' if avg_score >= 60 else 'C (Fair)' if avg_score >= 40 else 'D (Poor)'}")
    print(f"Total Warnings: {len(all_warnings)}")
    print(f"Unique Amenities: {len(all_amenities)}")
    
    if all_warnings:
        print(f"\n🚨 IMMEDIATE ACTION REQUIRED:")
        for warning in all_warnings[:5]:  # Show top 5
            print(f"   • {warning}")
    
    print("\n" + "="*60)
    
    return {
        'averageScore': avg_score,
        'totalWarnings': len(all_warnings),
        'amenitiesCount': len(all_amenities),
        'insights': all_insights
    }

# Run the dashboard on our previous analysis
dashboard = create_analysis_dashboard(full_analysis)

# Visualize the quality scores
import matplotlib.pyplot as plt

scores = [extract_business_insights(a['analysis'])['qualityScore'] 
          for a in full_analysis['imageAnalyses']]

plt.figure(figsize=(10, 5))
plt.bar(range(1, len(scores) + 1), scores, color=['green' if s >= 60 else 'orange' if s >= 40 else 'red' for s in scores])
plt.xlabel('Image Number')
plt.ylabel('Quality Score')
plt.title('Quality Scores by Image')
plt.ylim(0, 100)
plt.axhline(y=60, color='gray', linestyle='--', label='Good Threshold')
plt.legend()
plt.show()
```

**Expected Output:**
- Business Intelligence Dashboard with quality scores
- Categorized insights for each image
- Aggregate metrics and quality grade
- Bar chart visualization of quality scores
- Action items for property management

## (10 Mins) 3. Ideation: Connecting to Your World

Take a moment to reflect on what you've built and brainstorm real-world applications:

1. **Scale and Automation**: Your travel platform receives 10,000 photo reviews daily. How would you modify this system to handle that volume? Consider:
   - Could you use Google Cloud Functions to process images asynchronously?
   - How would you implement caching to avoid re-analyzing identical images?
   - What database structure would efficiently store millions of analysis results?

2. **Enhanced Intelligence**: Beyond quality assessment, what other insights could transform your business?
   - How could you detect competitive advantages by analyzing competitor property photos?
   - Could you identify seasonal patterns (empty pools in winter, crowded lobbies in summer)?
   - What about detecting accessibility features for travelers with disabilities?
   - How might you flag potential safety or compliance issues that need immediate attention?

3. **Integration Challenges**: Consider these real-world scenarios:
   - How would you handle images that are too dark, blurry, or contain inappropriate content?
   - What if travelers upload photos with people's faces - how do you handle privacy?
   - How could you support multiple languages when signs or menus appear in photos?
   - What's your strategy for managing API costs when processing millions of images monthly?

## (15 Mins) 4. Application Challenge: Make It Your Own

### The Challenge
Your product manager just came with an urgent request: **"We need a Competitive Intelligence System by tomorrow!"**

The marketing team wants to understand how your properties compare to competitors. Build a system that:

1. Compares photos from at least two different properties
2. Identifies unique selling points and competitive gaps
3. Generates actionable recommendations for property partners
4. Creates a visual comparison report

### Your Mission:
Modify and extend your code to create a `competitive_analysis` function that:
- Takes two or more property image URLs
- Compares amenities, quality, and unique features
- Calculates competitive advantage scores
- Provides specific recommendations for improvement

```python
# Cell 6: Your challenge workspace
def competitive_analysis(property_a_images, property_b_images):
    """
    YOUR CHALLENGE: Complete this function to compare two properties
    
    Args:
        property_a_images: List of image URLs for Property A
        property_b_images: List of image URLs for Property B
    
    Returns:
        Competitive analysis report with scores and recommendations
    """
    
    # YOUR CODE HERE
    # Hints are provided below if you get stuck!
    
    pass

# Test your solution
property_a = [
    "https://images.unsplash.com/photo-1551882547-ff40c63fe5fa",  # Luxury hotel
]

property_b = [
    "https://images.unsplash.com/photo-1564501049412-61c2a3083791",  # Budget hotel
]

# Run your competitive analysis
# results = competitive_analysis(property_a, property_b)
```

### Hints (Try solving first, then check if stuck):

<details>
<summary>Hint 1: Comparison Structure</summary>

Create a comparison prompt that analyzes both images together:
```python
comparison_prompt = """
Compare these two hotel properties:
1. List amenities visible in Property A vs Property B
2. Rate quality of each (1-10 scale)
3. Identify unique features in each
4. Which offers better value and why?
"""
```
</details>

<details>
<summary>Hint 2: Using Multiple Images in One Request</summary>

You can send multiple images in a single API call:
```python
input=[{
    "role": "user",
    "content": [
        {"type": "input_text", "text": "Compare these properties..."},
        {"type": "input_image", "image_url": property_a_images[0]},
        {"type": "input_image", "image_url": property_b_images[0]}
    ]
}]
```
</details>

<details>
<summary>Hint 3: Scoring Competitive Advantage</summary>

Calculate advantage based on unique features:
```python
def calculate_competitive_score(analysis):
    # Extract mentions of "unique", "only", "better", "superior"
    # Count exclusive amenities
    # Factor in quality ratings
    # Return a score 0-100
```
</details>

### Success Criteria:
✅ Your code runs without errors in Colab  
✅ Successfully analyzes and compares at least 2 property images  
✅ Outputs include specific competitive advantages/disadvantages  
✅ Provides at least 2 actionable recommendations  
✅ Includes some form of visualization (chart, table, or formatted output)

---

**Congratulations!** 🎉 You've built a production-ready image analysis system entirely in Google Colab! You've learned to work with multimodal AI, process batches efficiently, extract business intelligence, and create actionable insights from visual content.

**Next Steps**: 
- Export your notebook and convert it to a Flask API
- Connect to Google Cloud Storage for image management
- Implement a Streamlit dashboard for real-time analysis
- Explore the [OpenAI documentation](https://platform.openai.com/docs) for video analysis capabilities

**Remember**: Save your notebook (File → Save a copy in Drive) to keep your work!
