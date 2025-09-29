# OpenAI ChatGPT 5 Streaming Responses Lab Guide

## Lab Overview
**Duration:** 15-20 minutes  
**Platform:** [Google Colab](https://colab.research.google.com)  
**Prerequisites:** Basic Python knowledge, Google account  
**Objective:** Learn to use streaming responses for real-time AI interactions

---

## Learning Objectives
By the end of this lab, you will be able to:
- Enable streaming for real-time responses
- Build progress indicators for long operations
- Create interactive chatbots with live feedback
- Handle streaming data efficiently for business applications

---

## Part 1: Setup (2 minutes)

### Step 1: Create Your Notebook
1. Go to [https://colab.research.google.com](https://colab.research.google.com)
2. Click **"New Notebook"**
3. Rename to `ChatGPT_5_Streaming_Lab`

### Step 2: Install and Import
```python
# Install OpenAI library
!pip install openai --quiet

# Import what we need
from openai import OpenAI
from getpass import getpass
import time
import json
```

### Step 3: Initialize Client
```python
# Enter your API key securely
api_key = getpass('Enter your OpenAI API key: ')

# Create the client
client = OpenAI(api_key=api_key)

print("✅ Ready to stream with ChatGPT 5!")
```

---

## Part 2: Basic Streaming (5 minutes)

### Step 4: Your First Stream
Let's see streaming in action:

```python
# Basic streaming example
print("🎭 Basic Streaming Demo:")
print("-" * 40)

stream = client.responses.create(
    model="gpt-5",
    input=[
        {
            "role": "user",
            "content": "Count from 1 to 10 slowly, with a brief pause between each number.",
        },
    ],
    stream=True,
)

# Print each chunk as it arrives
for event in stream:
    print(f"Chunk received: {event}")

print("\n" + "="*50 + "\n")
```

**What's happening:**
- `stream=True` enables streaming mode
- Response comes in chunks instead of all at once
- Each `event` is a piece of the response
- Great for long responses or real-time feedback

### Step 5: Extract Text from Stream
Get just the text content from streaming chunks:

```python
# Extract and display text in real-time
def stream_response(prompt):
    """
    Stream a response and display text as it arrives
    """
    print(f"💬 User: {prompt}")
    print("🤖 Assistant: ", end="")
    
    stream = client.responses.create(
        model="gpt-5",
        input=[
            {"role": "user", "content": prompt}
        ],
        stream=True,
    )
    
    full_response = ""
    for chunk in stream:
        # Extract text from chunk (structure may vary)
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                print(delta.content, end="", flush=True)
                full_response += delta.content
    
    print("\n")  # New line after response
    return full_response

# Test streaming with text extraction
response = stream_response("Write a 3-sentence elevator pitch for a cloud storage company.")
print("="*50 + "\n")
```

### Step 6: Simulate Typing Effect
Create a more natural conversation feel:

```python
# Create a typing effect for better UX
def stream_with_typing_effect(prompt, typing_speed=0.03):
    """
    Stream response with a typing effect
    
    Args:
        prompt: User's question
        typing_speed: Delay between characters (seconds)
    """
    print(f"💬 User: {prompt}")
    print("🤖 Assistant: ", end="")
    
    stream = client.responses.create(
        model="gpt-5",
        input=[
            {"role": "user", "content": prompt}
        ],
        stream=True,
    )
    
    for chunk in stream:
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                # Print each character with a small delay
                for char in delta.content:
                    print(char, end="", flush=True)
                    time.sleep(typing_speed)
    
    print("\n")

# Test typing effect
stream_with_typing_effect("What are the top 3 benefits of cloud computing for businesses?", typing_speed=0.02)
print("="*50 + "\n")
```

---

## Part 3: Business Applications (5 minutes)

### Step 7: Progress Indicator for Reports
Show progress while generating long reports:

```python
# Generate reports with progress indicator
def generate_report_with_progress(report_type, company_name):
    """
    Generate a business report with progress updates
    """
    print(f"📊 Generating {report_type} for {company_name}...")
    print("Progress: ", end="")
    
    prompt = f"""Generate a detailed {report_type} for {company_name}.
    Include these sections:
    1. Executive Summary
    2. Current Performance
    3. Market Analysis
    4. Recommendations
    5. Conclusion
    
    Make it comprehensive but concise."""
    
    stream = client.responses.create(
        model="gpt-5",
        input=[
            {"role": "user", "content": prompt}
        ],
        stream=True,
    )
    
    full_report = ""
    word_count = 0
    progress_markers = ["█"] * 20  # Progress bar
    current_progress = 0
    
    for chunk in stream:
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                full_report += delta.content
                word_count += len(delta.content.split())
                
                # Update progress bar every 10 words
                new_progress = min(word_count // 10, 20)
                if new_progress > current_progress:
                    for _ in range(new_progress - current_progress):
                        print("█", end="", flush=True)
                    current_progress = new_progress
    
    # Complete the progress bar
    remaining = 20 - current_progress
    for _ in range(remaining):
        print("█", end="", flush=True)
    
    print(" Complete!")
    print(f"\n📄 Report Generated ({word_count} words):")
    print("-" * 40)
    print(full_report)
    return full_report

# Generate a sample report
report = generate_report_with_progress("Quarterly Business Review", "TechCorp Solutions")
print("\n" + "="*50 + "\n")
```

### Step 8: Real-Time Data Analysis
Stream analysis results as they're generated:

```python
# Stream data analysis results
def stream_data_analysis(data_description):
    """
    Perform streaming data analysis with live updates
    """
    print(f"🔍 Analyzing: {data_description}")
    print("-" * 40)
    
    prompt = f"""Analyze the following data scenario: {data_description}
    
    Provide:
    1. Initial observations
    2. Key metrics to track
    3. Potential insights
    4. Recommended actions
    
    Format each section clearly."""
    
    stream = client.responses.create(
        model="gpt-5",
        input=[
            {"role": "user", "content": prompt}
        ],
        stream=True,
    )
    
    current_section = ""
    sections = []
    
    for chunk in stream:
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                current_section += delta.content
                
                # Check if we completed a section (ends with number and period)
                if any(f"{i}." in delta.content for i in range(1, 5)):
                    if current_section.strip():
                        sections.append(current_section)
                        print(f"✓ Section {len(sections)} received")
                        current_section = ""
    
    # Add any remaining content
    if current_section.strip():
        sections.append(current_section)
        print(f"✓ Section {len(sections)} received")
    
    print("\n📈 Analysis Complete!")
    print("-" * 40)
    
    # Display all sections
    full_analysis = "".join(sections)
    print(full_analysis)
    return full_analysis

# Analyze sample data
analysis = stream_data_analysis("Monthly sales data showing 15% increase in Q3 but 5% decrease in customer retention")
print("\n" + "="*50 + "\n")
```

### Step 9: Interactive Q&A with Streaming
Build an interactive business consultant:

```python
# Interactive streaming Q&A system
def business_consultant():
    """
    Interactive business consultant with streaming responses
    """
    print("🎯 Business Consultant AI")
    print("=" * 50)
    print("Ask me about business strategy, marketing, operations, or finance.")
    print("Type 'exit' to end the consultation.\n")
    
    conversation_history = []
    
    while True:
        # Get user input
        user_input = input("You: ")
        
        if user_input.lower() == 'exit':
            print("\n👋 Thank you for consulting with us!")
            break
        
        # Add to history
        conversation_history.append({"role": "user", "content": user_input})
        
        # Stream the response
        print("Consultant: ", end="")
        
        stream = client.responses.create(
            model="gpt-5",
            input=conversation_history,
            stream=True,
        )
        
        full_response = ""
        for chunk in stream:
            if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
                delta = chunk.choices[0].delta
                if hasattr(delta, 'content') and delta.content:
                    print(delta.content, end="", flush=True)
                    full_response += delta.content
        
        print("\n")
        
        # Add assistant response to history
        conversation_history.append({"role": "assistant", "content": full_response})
        
        # Keep conversation manageable (last 10 exchanges)
        if len(conversation_history) > 20:
            conversation_history = conversation_history[-20:]

# Run the consultant (commented out for non-interactive environment)
# Uncomment the line below to test in an interactive environment
# business_consultant()

# Demo version for non-interactive testing
print("🎯 Business Consultant Demo")
print("=" * 50)

demo_questions = [
    "What are the key factors for successful product launch?",
    "How can we improve customer retention?"
]

for question in demo_questions:
    print(f"\nYou: {question}")
    print("Consultant: ", end="")
    
    stream = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": question}],
        stream=True,
    )
    
    for chunk in stream:
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                print(delta.content, end="", flush=True)
    print("\n" + "-"*40)

print("\n" + "="*50 + "\n")
```

---

## Part 4: Advanced Streaming (4 minutes)

### Step 10: Stream Processing Pipeline
Process streaming data in real-time:

```python
# Advanced stream processing
class StreamProcessor:
    """
    Process streaming responses with analytics
    """
    
    def __init__(self):
        self.word_count = 0
        self.sentence_count = 0
        self.start_time = None
        self.chunks_received = 0
        
    def process_stream(self, prompt):
        """
        Process a stream with analytics
        """
        print(f"📝 Processing: {prompt[:50]}...")
        print("-" * 40)
        
        self.start_time = time.time()
        
        stream = client.responses.create(
            model="gpt-5",
            input=[{"role": "user", "content": prompt}],
            stream=True,
        )
        
        full_text = ""
        
        for chunk in stream:
            self.chunks_received += 1
            
            if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
                delta = chunk.choices[0].delta
                if hasattr(delta, 'content') and delta.content:
                    chunk_text = delta.content
                    full_text += chunk_text
                    
                    # Update statistics
                    self.word_count += len(chunk_text.split())
                    self.sentence_count += chunk_text.count('.')
                    
                    # Show progress every 5 chunks
                    if self.chunks_received % 5 == 0:
                        elapsed = time.time() - self.start_time
                        print(f"⚡ Chunks: {self.chunks_received} | "
                              f"Words: {self.word_count} | "
                              f"Time: {elapsed:.1f}s", end="\r")
        
        # Final statistics
        total_time = time.time() - self.start_time
        
        print(f"\n✅ Stream Complete!")
        print(f"📊 Statistics:")
        print(f"  - Total chunks: {self.chunks_received}")
        print(f"  - Total words: {self.word_count}")
        print(f"  - Total sentences: {self.sentence_count}")
        print(f"  - Processing time: {total_time:.2f} seconds")
        print(f"  - Words per second: {self.word_count/total_time:.1f}")
        
        return full_text

# Test the processor
processor = StreamProcessor()
result = processor.process_stream(
    "Write a detailed 5-step plan for launching a new SaaS product in the B2B market."
)

print("\n📄 Generated Content:")
print("-" * 40)
print(result)
print("\n" + "="*50 + "\n")
```

### Step 11: Buffered Streaming
Handle streams with buffering for better performance:

```python
# Buffered streaming for large responses
def buffered_stream(prompt, buffer_size=100):
    """
    Stream with buffering to reduce display updates
    
    Args:
        prompt: The user prompt
        buffer_size: Characters to buffer before displaying
    """
    print(f"💼 Buffered Stream Demo")
    print(f"Buffer size: {buffer_size} characters")
    print("-" * 40)
    print(f"Query: {prompt}")
    print("\n🤖 Response:\n")
    
    stream = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": prompt}],
        stream=True,
    )
    
    buffer = ""
    total_content = ""
    updates = 0
    
    for chunk in stream:
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                buffer += delta.content
                total_content += delta.content
                
                # Flush buffer when it reaches the size limit
                if len(buffer) >= buffer_size:
                    print(buffer, end="", flush=True)
                    updates += 1
                    buffer = ""
    
    # Flush any remaining content
    if buffer:
        print(buffer, end="", flush=True)
        updates += 1
    
    print(f"\n\n📈 Performance: {updates} display updates")
    return total_content

# Test buffered streaming
buffered_result = buffered_stream(
    "Explain the key components of a successful digital transformation strategy.",
    buffer_size=50
)
print("="*50 + "\n")
```

---

## Part 5: Error Handling and Best Practices (4 minutes)

### Step 12: Robust Streaming with Error Handling
Handle streaming errors gracefully:

```python
# Robust streaming with error handling
def safe_stream(prompt, max_retries=3):
    """
    Stream with error handling and retry logic
    """
    print(f"🛡️ Safe Streaming")
    print("-" * 40)
    
    for attempt in range(max_retries):
        try:
            print(f"Attempt {attempt + 1}/{max_retries}...")
            
            stream = client.responses.create(
                model="gpt-5",
                input=[{"role": "user", "content": prompt}],
                stream=True,
                timeout=30  # 30 second timeout
            )
            
            full_response = ""
            chunk_count = 0
            
            for chunk in stream:
                chunk_count += 1
                
                if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
                    delta = chunk.choices[0].delta
                    if hasattr(delta, 'content') and delta.content:
                        full_response += delta.content
                        print(".", end="", flush=True)  # Progress indicator
                
                # Simulate potential timeout
                if chunk_count > 1000:  # Safety limit
                    raise TimeoutError("Stream took too long")
            
            print(f"\n✅ Success! Received {chunk_count} chunks")
            return full_response
            
        except TimeoutError as e:
            print(f"\n⏱️ Timeout on attempt {attempt + 1}: {e}")
            if attempt < max_retries - 1:
                print("Retrying...")
                time.sleep(2)  # Wait before retry
        
        except Exception as e:
            print(f"\n❌ Error on attempt {attempt + 1}: {e}")
            if attempt < max_retries - 1:
                print("Retrying...")
                time.sleep(2)
    
    return None

# Test error handling
result = safe_stream("List 5 key performance indicators for a SaaS business.")
if result:
    print("\n📊 Result:")
    print(result)
else:
    print("\n❌ Failed to get response after all retries")

print("\n" + "="*50 + "\n")
```

---

## 🎯 Challenge: Build a Real-Time Report Generator

Create a streaming report generator that shows progress and sections as they're generated.

### Requirements:
1. Generate multi-section reports
2. Show which section is being written
3. Display word count in real-time
4. Calculate generation speed
5. Format the final report nicely

### Starter Code:
```python
class ReportGenerator:
    def __init__(self):
        self.sections = []
        self.current_section = ""
        self.word_count = 0
        self.start_time = None
        
    def generate_report(self, company_name, report_type):
        """
        Generate a streaming business report
        
        Args:
            company_name: Name of the company
            report_type: Type of report (annual, quarterly, etc.)
        """
        
        # TODO: Create a detailed prompt requesting specific sections
        
        # TODO: Start streaming and track progress
        
        # TODO: Detect section changes (look for headers)
        
        # TODO: Update statistics in real-time
        
        # TODO: Format and return the final report
        
        pass
    
    def display_progress(self):
        """Show real-time progress"""
        # TODO: Display current section being written
        # TODO: Show word count and generation speed
        pass
    
    def format_report(self):
        """Format the final report"""
        # TODO: Add headers and formatting
        # TODO: Include metadata (word count, generation time)
        pass

# Test your implementation
# generator = ReportGenerator()
# report = generator.generate_report("Acme Corp", "quarterly")
# print(report)
```

### 💡 Hints:
- Look for section headers (numbers, bullet points, headers)
- Update progress every N chunks for smooth display
- Use ANSI codes or simple characters for progress bars
- Track both total time and per-section time

---

## Complete Solution Code

```python
# ============================================
# COMPLETE CODE FOR CHATGPT 5 STREAMING LAB
# ============================================

# SETUP
# ---------------------------------------------
# !pip install openai --quiet

from openai import OpenAI
from getpass import getpass
import time
import json

# Initialize client
api_key = getpass('Enter your OpenAI API key: ')
client = OpenAI(api_key=api_key)
print("✅ Ready to stream with ChatGPT 5!")

# BASIC STREAMING
# ---------------------------------------------

def stream_response(prompt):
    """Stream a response and display text as it arrives"""
    print(f"💬 User: {prompt}")
    print("🤖 Assistant: ", end="")
    
    stream = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": prompt}],
        stream=True,
    )
    
    full_response = ""
    for chunk in stream:
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                print(delta.content, end="", flush=True)
                full_response += delta.content
    
    print("\n")
    return full_response

# BUSINESS APPLICATIONS
# ---------------------------------------------

def generate_report_with_progress(report_type, company_name):
    """Generate a business report with progress updates"""
    print(f"📊 Generating {report_type} for {company_name}...")
    print("Progress: ", end="")
    
    prompt = f"""Generate a detailed {report_type} for {company_name}.
    Include: Executive Summary, Current Performance, Market Analysis, 
    Recommendations, and Conclusion. Be comprehensive but concise."""
    
    stream = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": prompt}],
        stream=True,
    )
    
    full_report = ""
    word_count = 0
    current_progress = 0
    
    for chunk in stream:
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content:
                full_report += delta.content
                word_count += len(delta.content.split())
                
                new_progress = min(word_count // 10, 20)
                if new_progress > current_progress:
                    for _ in range(new_progress - current_progress):
                        print("█", end="", flush=True)
                    current_progress = new_progress
    
    remaining = 20 - current_progress
    for _ in range(remaining):
        print("█", end="", flush=True)
    
    print(" Complete!")
    print(f"\n📄 Report Generated ({word_count} words)")
    return full_report

# ADVANCED STREAMING
# ---------------------------------------------

class StreamProcessor:
    """Process streaming responses with analytics"""
    
    def __init__(self):
        self.word_count = 0
        self.sentence_count = 0
        self.start_time = None
        self.chunks_received = 0
        
    def process_stream(self, prompt):
        """Process a stream with analytics"""
        print(f"📝 Processing: {prompt[:50]}...")
        self.start_time = time.time()
        
        stream = client.responses.create(
            model="gpt-5",
            input=[{"role": "user", "content": prompt}],
            stream=True,
        )
        
        full_text = ""
        
        for chunk in stream:
            self.chunks_received += 1
            
            if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
                delta = chunk.choices[0].delta
                if hasattr(delta, 'content') and delta.content:
                    chunk_text = delta.content
                    full_text += chunk_text
                    self.word_count += len(chunk_text.split())
                    self.sentence_count += chunk_text.count('.')
                    
                    if self.chunks_received % 5 == 0:
                        elapsed = time.time() - self.start_time
                        print(f"⚡ Chunks: {self.chunks_received} | "
                              f"Words: {self.word_count} | "
                              f"Time: {elapsed:.1f}s", end="\r")
        
        total_time = time.time() - self.start_time
        
        print(f"\n✅ Stream Complete!")
        print(f"📊 Statistics:")
        print(f"  - Total chunks: {self.chunks_received}")
        print(f"  - Total words: {self.word_count}")
        print(f"  - Total sentences: {self.sentence_count}")
        print(f"  - Processing time: {total_time:.2f} seconds")
        print(f"  - Words per second: {self.word_count/total_time:.1f}")
        
        return full_text

# CHALLENGE SOLUTION - REPORT GENERATOR
# ---------------------------------------------

class ReportGenerator:
    def __init__(self):
        self.sections = []
        self.current_section = ""
        self.current_section_name = ""
        self.word_count = 0
        self.start_time = None
        self.section_times = {}
        
    def generate_report(self, company_name, report_type):
        """Generate a streaming business report"""
        
        print(f"📈 Generating {report_type} Report for {company_name}")
        print("="*50)
        
        prompt = f"""Generate a comprehensive {report_type} report for {company_name}.
        
        Include these sections with clear headers:
        
        1. EXECUTIVE SUMMARY
        Brief overview of key findings and recommendations.
        
        2. FINANCIAL PERFORMANCE
        Analysis of financial metrics and trends.
        
        3. MARKET POSITION
        Competitive analysis and market share.
        
        4. OPERATIONAL HIGHLIGHTS
        Key operational achievements and challenges.
        
        5. STRATEGIC RECOMMENDATIONS
        Actionable recommendations for improvement.
        
        6. CONCLUSION
        Summary and next steps.
        
        Make each section detailed and insightful."""
        
        self.start_time = time.time()
        section_start = time.time()
        
        stream = client.responses.create(
            model="gpt-5",
            input=[{"role": "user", "content": prompt}],
            stream=True,
        )
        
        full_report = ""
        section_headers = [
            "EXECUTIVE SUMMARY",
            "FINANCIAL PERFORMANCE",
            "MARKET POSITION",
            "OPERATIONAL HIGHLIGHTS",
            "STRATEGIC RECOMMENDATIONS",
            "CONCLUSION"
        ]
        
        for chunk in stream:
            if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
                delta = chunk.choices[0].delta
                if hasattr(delta, 'content') and delta.content:
                    chunk_text = delta.content
                    self.current_section += chunk_text
                    full_report += chunk_text
                    self.word_count += len(chunk_text.split())
                    
                    # Check for section headers
                    for header in section_headers:
                        if header in chunk_text.upper():
                            # Save previous section if exists
                            if self.current_section_name:
                                section_time = time.time() - section_start
                                self.section_times[self.current_section_name] = section_time
                                print(f"✓ {self.current_section_name} completed "
                                      f"({section_time:.1f}s)")
                            
                            # Start new section
                            self.current_section_name = header
                            section_start = time.time()
                            print(f"\n📝 Writing {header}...", end="", flush=True)
                            self.sections.append(self.current_section)
                            self.current_section = ""
                    
                    # Show progress dots
                    if self.word_count % 20 == 0:
                        print(".", end="", flush=True)
        
        # Save final section
        if self.current_section:
            self.sections.append(self.current_section)
            if self.current_section_name:
                section_time = time.time() - section_start
                self.section_times[self.current_section_name] = section_time
                print(f"\n✓ {self.current_section_name} completed ({section_time:.1f}s)")
        
        total_time = time.time() - self.start_time
        
        # Display statistics
        print("\n\n" + "="*50)
        print("📊 GENERATION STATISTICS")
        print(f"Total Time: {total_time:.2f} seconds")
        print(f"Total Words: {self.word_count}")
        print(f"Generation Speed: {self.word_count/total_time:.1f} words/second")
        print(f"Sections Generated: {len(self.sections)}")
        
        return self.format_report(company_name, report_type, full_report)
    
    def format_report(self, company_name, report_type, content):
        """Format the final report"""
        formatted = f"""
{'='*60}
{report_type.upper()} REPORT
{company_name}
Generated: {time.strftime('%Y-%m-%d %H:%M:%S')}
{'='*60}

{content}

{'='*60}
Report Metadata:
- Total Words: {self.word_count}
- Generation Time: {time.time() - self.start_time:.2f} seconds
- Sections: {len(self.sections)}
{'='*60}
        """
        return formatted

# TEST ALL FUNCTIONS
# ---------------------------------------------

# Test basic streaming
print("1️⃣ BASIC STREAMING TEST")
print("="*50)
response = stream_response("List 3 benefits of cloud computing for businesses.")
print("="*50 + "\n")

# Test report generation with progress
print("2️⃣ REPORT GENERATION TEST")
print("="*50)
report = generate_report_with_progress("Quarterly Review", "TechCorp")
print(report[:500] + "...")  # Show first 500 chars
print("="*50 + "\n")

# Test stream processor
print("3️⃣ STREAM PROCESSOR TEST")
print("="*50)
processor = StreamProcessor()
result = processor.process_stream("Explain 3 key metrics for SaaS businesses.")
print("\nContent Preview:", result[:300] + "...")
print("="*50 + "\n")

# Test report generator (challenge solution)
print("4️⃣ ADVANCED REPORT GENERATOR TEST")
print("="*50)
generator = ReportGenerator()
full_report = generator.generate_report("Acme Corporation", "Annual")
print("\nFirst 500 characters of report:")
print(full_report[:500] + "...")

print("\n✅ All streaming functions tested successfully!")
```

---

## Troubleshooting

**Stream not displaying:**
- Make sure to use `flush=True` in print statements
- Check that you're iterating through the stream properly

**Incomplete responses:**
- Don't break the loop early
- Handle all chunks even if they seem empty

**Performance issues:**
- Use buffering for large responses
- Limit display updates to improve performance

---

## Resources

- **OpenAI Platform**: [https://platform.openai.com](https://platform.openai.com)
- **Streaming Documentation**: [https://platform.openai.com/docs/api-reference/streaming](https://platform.openai.com/docs/api-reference/streaming)
- **API Reference**: [https://platform.openai.com/docs/api-reference](https://platform.openai.com/docs/api-reference)
- **Google Colab**: [https://colab.research.google.com](https://colab.research.google.com)

---

## Lab Checklist

- [ ] Set up OpenAI client
- [ ] Created basic streaming response
- [ ] Extracted text from stream chunks
- [ ] Built typing effect display
- [ ] Generated reports with progress
- [ ] Implemented stream processor
- [ ] Added error handling
- [ ] Built report generator challenge

---

## Key Takeaways

You've learned to:
- ⚡ Enable streaming for real-time responses
- 📊 Show progress during generation
- 💬 Build interactive chat interfaces
- 📈 Track streaming analytics
- 🛡️ Handle errors in streaming

**Excellent work!** 🎉 You can now build responsive AI applications with real-time feedback!