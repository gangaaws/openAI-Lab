# OpenAI ChatGPT 5 Multimodal API Lab Guide

## Lab Overview
**Duration:** 15-20 minutes  
**Platform:** [Google Colab](https://colab.research.google.com)  
**Prerequisites:** Basic Python knowledge, Google account  
**Objective:** Learn to use ChatGPT 5's multimodal capabilities for business applications

---

## Learning Objectives
By the end of this lab, you will be able to:
- Analyze business images (charts, products, documents)
- Process corporate PDFs and reports
- Upload and analyze internal documents
- Build practical business automation tools

---

## Part 1: Environment Setup (2 minutes)

### Step 1: Create Your Notebook
1. Go to [https://colab.research.google.com](https://colab.research.google.com)
2. Click **"New Notebook"**
3. Rename it to `ChatGPT_5_Business_Multimodal_Lab`

### Step 2: Install Required Libraries
```python
# Install required libraries for OpenAI and PDF handling
!pip install openai reportlab --quiet

# Import necessary modules
from openai import OpenAI
from getpass import getpass
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas
from reportlab.lib.utils import simpleSplit
```

### Step 3: Initialize OpenAI Client
```python
# Secure API key entry
api_key = getpass('Enter your OpenAI API key: ')

# Initialize the OpenAI client
client = OpenAI(api_key=api_key)

print("✅ OpenAI client initialized successfully!")
```

---

## Part 2: Business Image Analysis (5 minutes)

### Step 4: Analyze Business Charts and Graphs
Let's analyze a business visualization to extract insights:

```python
# Analyze a business chart or graph
def analyze_business_chart(image_url):
    """
    Extract data and insights from business charts
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": """Analyze this business chart and provide:
                        1. Type of visualization (bar chart, pie chart, etc.)
                        2. Key metrics shown
                        3. Main trends or patterns
                        4. Business insights or recommendations
                        Format as bullet points."""
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

# Example: Analyze a sales dashboard
chart_url = "https://images.unsplash.com/photo-1551288049-bebda4e38f71"
analysis = analyze_business_chart(chart_url)

print("📊 Business Chart Analysis:")
print(analysis)
print("\n" + "="*50 + "\n")
```

### Step 5: Product Image Analysis for E-commerce
Automate product cataloging and description generation:

```python
# Analyze product images for inventory management
def analyze_product_for_inventory(image_url):
    """
    Extract product details for inventory systems
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": """Analyze this product image for our inventory system:
                        1. Product category
                        2. Color variants visible
                        3. Material (if identifiable)
                        4. Suggested SKU prefix
                        5. Professional product description (50 words)
                        6. Suggested retail price range
                        Format as structured data."""
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

# Example: Analyze a product for cataloging
product_url = "https://images.unsplash.com/photo-1542291026-7eec264c27ff"
product_details = analyze_product_for_inventory(product_url)

print("🏷️ Product Inventory Analysis:")
print(product_details)
print("\n" + "="*50 + "\n")
```

### Step 6: Extract Text from Business Documents
OCR capabilities for digitizing paper documents:

```python
# Extract text from scanned documents or receipts
def extract_text_from_document(image_url):
    """
    OCR for business documents, receipts, invoices
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": """Extract all readable text from this document image.
                        If it's a receipt or invoice, also identify:
                        - Total amount
                        - Date
                        - Vendor/Company name
                        - Key line items"""
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

# Example: Extract text from a document
document_image = "https://images.unsplash.com/photo-1554224155-6726b3ff858f"
extracted_text = extract_text_from_document(document_image)

print("📄 Document Text Extraction:")
print(extracted_text)
print("\n" + "="*50 + "\n")
```

---

## Part 3: Corporate PDF Processing (5 minutes)

### Step 7: Financial Report Analysis
Process quarterly earnings and annual reports:

```python
# Analyze financial reports
def analyze_financial_report(pdf_url):
    """
    Extract key financial metrics from corporate reports
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": """Analyze this financial report and extract:
                        1. Revenue (current vs previous period)
                        2. Net profit/loss
                        3. Key performance indicators mentioned
                        4. Major risks or challenges noted
                        5. Executive summary in 3 bullet points
                        
                        Format as an executive brief."""
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

# Example: Analyze Berkshire Hathaway annual letter
report_url = "https://www.berkshirehathaway.com/letters/2023ltr.pdf"
financial_summary = analyze_financial_report(report_url)

print("💰 Financial Report Analysis:")
print(financial_summary)
print("\n" + "="*50 + "\n")
```

### Step 8: Contract and Legal Document Review
Automate contract analysis for key terms:

```python
# Review contracts for important terms
def review_contract(pdf_url):
    """
    Extract key terms from contracts and agreements
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": """Review this contract and identify:
                        1. Parties involved
                        2. Contract value/payment terms
                        3. Key deliverables
                        4. Important dates/deadlines
                        5. Termination clauses
                        6. Liability/penalty clauses
                        
                        Highlight any unusual or high-risk terms."""
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

# Example with a public filing
contract_url = "https://www.sec.gov/Archives/edgar/data/1018724/000101872423000013/amzn-20221231.pdf"
contract_review = review_contract(contract_url)

print("⚖️ Contract Review Summary:")
print(contract_review)
print("\n" + "="*50 + "\n")
```

---

## Part 4: Internal Document Upload (4 minutes)

### Step 9: Create and Upload Internal Documents
Since the API only accepts PDFs, we'll create a helper function:

```python
# Helper function to create PDFs from text
def create_pdf_from_text(text_content, filename="document.pdf"):
    """
    Convert text content to PDF for upload
    """
    c = canvas.Canvas(filename, pagesize=letter)
    width, height = letter
    
    # Set up formatting
    margin = 50
    y_position = height - margin
    line_height = 15
    max_width = width - (2 * margin)
    
    # Process text
    lines = text_content.split('\n')
    
    for line in lines:
        if y_position < margin:  # New page if needed
            c.showPage()
            y_position = height - margin
        
        if line:
            wrapped_lines = simpleSplit(line, "Helvetica", 12, max_width)
            for wrapped_line in wrapped_lines:
                c.drawString(margin, y_position, wrapped_line)
                y_position -= line_height
        else:
            y_position -= line_height
    
    c.save()
    return filename

# Create a sample business memo
memo = """INTERNAL MEMO

TO: All Department Heads
FROM: Operations Team
DATE: January 15, 2024
RE: Q1 2024 Operational Efficiency Initiative

OBJECTIVE:
Implement cost-saving measures to achieve 15% operational efficiency improvement.

KEY INITIATIVES:
1. Automation of manual reporting processes
2. Consolidation of vendor contracts
3. Energy consumption reduction program
4. Remote work optimization

EXPECTED OUTCOMES:
- $2.5M annual cost savings
- 30% reduction in manual tasks
- 20% decrease in energy costs
- Improved employee satisfaction scores

TIMELINE:
Phase 1: Jan-Feb 2024 - Assessment and planning
Phase 2: Mar-Apr 2024 - Implementation
Phase 3: May-Jun 2024 - Monitoring and adjustment

ACTION REQUIRED:
Submit departmental efficiency plans by January 31, 2024."""

# Create PDF
pdf_file = create_pdf_from_text(memo, "company_memo.pdf")
print(f"✅ Created PDF: {pdf_file}")
```

### Step 10: Upload and Analyze Internal Documents
```python
# Upload and analyze the internal document
def analyze_internal_document(pdf_path, analysis_type="summary"):
    """
    Upload and analyze internal company documents
    """
    # Upload the file
    with open(pdf_path, 'rb') as f:
        uploaded = client.files.create(
            file=f,
            purpose="user_data"
        )
    
    print(f"📤 File uploaded. ID: {uploaded.id}")
    
    # Define analysis types
    prompts = {
        "summary": "Provide a concise executive summary of this document.",
        "action_items": "List all action items and deadlines mentioned.",
        "metrics": "Extract all metrics, KPIs, and quantifiable goals.",
        "risks": "Identify potential risks or challenges mentioned."
    }
    
    # Analyze the document
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
                        "text": prompts.get(analysis_type, prompts["summary"])
                    }
                ]
            }
        ]
    )
    
    return response.output_text

# Analyze for action items
action_items = analyze_internal_document("company_memo.pdf", "action_items")
print("\n📋 Action Items Extracted:")
print(action_items)

# Analyze for metrics
metrics = analyze_internal_document("company_memo.pdf", "metrics")
print("\n📊 Metrics and KPIs:")
print(metrics)
print("\n" + "="*50 + "\n")
```

---

## Part 5: Combined Business Applications (4 minutes)

### Step 11: Invoice Processing System
Combine image and text analysis for accounts payable:

```python
def process_invoice(image_url=None, pdf_url=None, additional_notes=""):
    """
    Complete invoice processing system
    
    Args:
        image_url: Scanned invoice image
        pdf_url: Digital invoice PDF
        additional_notes: PO numbers or special instructions
    """
    content = [
        {
            "type": "input_text",
            "text": """Process this invoice and extract:
            1. Vendor name and contact
            2. Invoice number and date
            3. Total amount due
            4. Line items with amounts
            5. Payment terms
            6. Due date
            
            Flag any anomalies or missing information.
            Format as structured data for our accounting system."""
        }
    ]
    
    # Add image if provided
    if image_url:
        content.append({
            "type": "input_image",
            "image_url": image_url
        })
    
    # Add PDF if provided
    if pdf_url:
        content.append({
            "type": "input_file",
            "file_url": pdf_url
        })
    
    # Add notes
    if additional_notes:
        content.append({
            "type": "input_text",
            "text": f"Additional information: {additional_notes}"
        })
    
    response = client.responses.create(
        model="gpt-5",
        input=[{"role": "user", "content": content}]
    )
    
    return response.output_text

# Example: Process an invoice image
invoice_image = "https://images.unsplash.com/photo-1554224155-6726b3ff858f"
invoice_data = process_invoice(
    image_url=invoice_image,
    additional_notes="PO#2024-1234, Budget code: MKT-Q1"
)

print("🧾 Invoice Processing Results:")
print(invoice_data)
print("\n" + "="*50 + "\n")
```

### Step 12: Competitive Analysis Tool
Analyze competitor materials and reports:

```python
def competitive_analysis(company_name, document_url):
    """
    Analyze competitor documents for strategic insights
    """
    response = client.responses.create(
        model="gpt-5",
        input=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                        "text": f"""Analyze this {company_name} document for competitive intelligence:
                        1. Business strategy and focus areas
                        2. Financial performance indicators
                        3. New products or services mentioned
                        4. Market expansion plans
                        5. Technology investments
                        6. Weaknesses or challenges admitted
                        
                        Provide actionable insights for our strategy team."""
                    },
                    {
                        "type": "input_file",
                        "file_url": document_url
                    }
                ]
            }
        ]
    )
    return response.output_text

# Example: Analyze competitor's annual report
competitor_report = "https://www.berkshirehathaway.com/letters/2023ltr.pdf"
insights = competitive_analysis("Berkshire Hathaway", competitor_report)

print("🎯 Competitive Intelligence Report:")
print(insights)
print("\n" + "="*50 + "\n")
```

---

## 🎯 Challenge: Build a Document Intelligence System

Create a comprehensive document processing system for your business operations.

### Requirements:
1. **Process Multiple Document Types**: Handle invoices, contracts, and reports
2. **Extract Structured Data**: Return data in a format ready for databases
3. **Batch Processing**: Handle multiple documents efficiently
4. **Error Handling**: Gracefully handle various file formats and errors

### Starter Code:
```python
class BusinessDocumentProcessor:
    def __init__(self, client):
        self.client = client
        self.processed_documents = []
        
    def process_document(self, doc_source, doc_type="general"):
        """
        Process a business document based on type
        
        Args:
            doc_source: URL or file path
            doc_type: invoice, contract, report, memo
        
        Returns:
            Structured data extraction
        """
        
        # TODO: Implement document type detection
        # TODO: Apply appropriate prompts for each type
        # TODO: Return structured JSON output
        
        pass
    
    def batch_process(self, document_list):
        """
        Process multiple documents and generate summary report
        """
        # TODO: Process each document
        # TODO: Track success/failure
        # TODO: Generate executive summary
        
        pass
    
    def export_to_json(self, output_file="results.json"):
        """
        Export all results to JSON for integration
        """
        # TODO: Format results
        # TODO: Save to file
        
        pass

# Initialize processor
processor = BusinessDocumentProcessor(client)

# Test your implementation
# result = processor.process_document("invoice.pdf", "invoice")
# print(result)
```

### 💡 Implementation Tips:
- Use consistent JSON schemas for each document type
- Include confidence scores for extracted data
- Log processing times for performance monitoring
- Implement retry logic for API failures

### 🌟 Bonus Features (Optional):
1. **Multi-language Support**: Detect and translate foreign documents
2. **Compliance Checking**: Flag documents missing required information
3. **Trend Analysis**: Track changes across document versions

---

## Common Business Use Cases

### Examples of What You Can Build:
```python
# Purchase Order Processor
def process_purchase_order(po_url):
    # Extract vendor, items, quantities, delivery dates
    pass

# Expense Report Analyzer  
def analyze_expense_report(receipt_images):
    # Categorize expenses, check policy compliance
    pass

# RFP Response Generator
def analyze_rfp(rfp_url):
    # Extract requirements, deadlines, evaluation criteria
    pass

# Financial Statement Analyzer
def analyze_financial_statements(statement_url):
    # Calculate ratios, identify trends, benchmark
    pass
```

---

## Troubleshooting

**Common Issues:**

**"Invalid file format" error:**
- Solution: Convert to PDF using the `create_pdf_from_text()` function
- The API only accepts PDF files, not .txt, .docx, etc.

**"File too large" error:**
- Solution: PDFs must be under 20MB
- Consider splitting large documents

**"Rate limit exceeded":**
- Solution: Implement delays between requests
- Use exponential backoff for retries

---

## Resources

- **OpenAI Platform**: [https://platform.openai.com](https://platform.openai.com)
- **API Documentation**: [https://platform.openai.com/docs](https://platform.openai.com/docs)
- **API Keys**: [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Rate Limits**: [https://platform.openai.com/docs/guides/rate-limits](https://platform.openai.com/docs/guides/rate-limits)
- **Google Colab**: [https://colab.research.google.com](https://colab.research.google.com)

---

## Lab Completion Checklist

- [ ] Configured OpenAI client successfully
- [ ] Analyzed business charts and graphs
- [ ] Extracted text from document images
- [ ] Processed financial reports from URLs
- [ ] Created and uploaded PDF documents
- [ ] Built invoice processing function
- [ ] Implemented document processor class
- [ ] Tested with multiple document types

---

## Key Takeaways

You've learned to:
- 📊 Extract insights from business visualizations
- 📄 Process corporate documents automatically
- 🏷️ Analyze products for inventory management
- 💰 Review financial reports and contracts
- 🧾 Build practical business automation tools

**Excellent work!** 🎉 You're now equipped to build AI-powered document processing systems for real business applications!