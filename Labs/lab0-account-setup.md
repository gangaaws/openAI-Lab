# Lab 0: Development Environment Setup

## Overview
Welcome to the OpenAI/GPT-5 course! Before we begin building AI applications, you'll need to set up accounts on several platforms that we'll use throughout the course. This lab will guide you through creating and configuring these essential accounts.

## Learning Objectives
By completing this lab, you will:
- Set up a complete development environment for AI/ML projects
- Create accounts on key platforms for coding, collaboration, and deployment
- Understand the purpose of each platform in our development workflow

## Time Required
Approximately 30-45 minutes

## Prerequisites
- A valid email address
- A web browser (Chrome, Firefox, Safari, or Edge recommended)
- Basic familiarity with web navigation

---

## Part 1: Google Colab Account Setup

Google Colab provides free cloud-based Jupyter notebooks with GPU support, perfect for running AI models without local hardware requirements.

### Steps:
1. Navigate to [https://colab.research.google.com](https://colab.research.google.com)
2. Click "Sign in" in the top right corner
3. Use your existing Google account or create a new one:
   - If creating new: Click "Create account" and follow the prompts
   - Choose a username and password you'll remember
4. Once signed in, click "New notebook" to verify access
5. Test your setup by running this simple code cell:
   ```python
   print("Hello, OpenAI Course!")
   import sys
   print(f"Python version: {sys.version}")
   ```
6. Save your test notebook with a meaningful name like "Lab0_Test"

### Verification:
- [ ] Successfully logged into Google Colab
- [ ] Created and ran a test notebook
- [ ] Saved the notebook to your Google Drive

---

## Part 2: GitHub Account Creation

GitHub will be our version control platform for storing code, collaborating on projects, and building your portfolio.

### Steps:
1. Go to [https://github.com](https://github.com)
2. Click "Sign up" in the top right corner
3. Enter your email address and click "Continue"
4. Create a strong password and click "Continue"
5. Choose a unique username (this will be part of your public profile URL)
   - Tip: Use a professional username as this may be seen by future employers
6. Solve the verification puzzle
7. Verify your email address by checking your inbox
8. Complete the profile setup:
   - Select "Just me" for team size
   - Choose "Student" for your role
   - Select interests relevant to AI/ML
9. Create your first repository:
   - Click the "+" icon in the top right
   - Select "New repository"
   - Name it "openai-course-labs"
   - Make it public
   - Initialize with a README
   - Click "Create repository"

### Verification:
- [ ] GitHub account created and email verified
- [ ] Profile username set
- [ ] First repository created
- [ ] README file visible in repository

---

## Part 3: Streamlit Cloud Account Setup

Streamlit Cloud allows us to deploy interactive web applications for our AI models directly from GitHub repositories.

### Steps:
1. Visit [https://streamlit.io/cloud](https://streamlit.io/cloud)
2. Click "Sign up" or "Get started"
3. Click "Continue with GitHub" (this is why we created GitHub first!)
4. Authorize Streamlit to access your GitHub account
5. Complete your Streamlit profile:
   - Confirm your email
   - Select your role (Student)
   - Choose your experience level
6. Link your GitHub repository:
   - Click "New app"
   - Select your "openai-course-labs" repository
   - Cancel the deployment for now (we'll use this later)

### Verification:
- [ ] Streamlit account created
- [ ] GitHub account successfully linked
- [ ] Can see your GitHub repositories in Streamlit

---

## Part 4: Netlify Account Setup

Netlify provides instant deployment for web applications and will host our AI-powered web interfaces.

### Steps:
1. Navigate to [https://www.netlify.com](https://www.netlify.com)
2. Click "Sign up" in the top right
3. Select "Sign up with GitHub" (using your newly created GitHub account)
4. Authorize Netlify to access your GitHub account
5. Complete the onboarding process:
   - Choose "Personal" for team type
   - Enter your name
   - Skip the optional survey or complete it
6. Explore the dashboard:
   - Click "Add new site"
   - Select "Import an existing project"
   - Choose "GitHub" as your Git provider
   - You should see your repositories listed
   - Cancel for now (we'll deploy projects in future labs)

### Verification:
- [ ] Netlify account created via GitHub
- [ ] GitHub repositories visible in Netlify
- [ ] Dashboard accessible

---

## Part 5: Account Security Best Practices

Now that you have all accounts set up, let's ensure they're secure:

### Enable Two-Factor Authentication (2FA):

#### GitHub 2FA:
1. Go to Settings → Password and authentication
2. Click "Enable two-factor authentication"
3. Follow the setup process using an authenticator app or SMS

#### Google Account 2FA:
1. Visit [https://myaccount.google.com/security](https://myaccount.google.com/security)
2. Click on "2-Step Verification"
3. Follow the setup process

### Password Management:
- Use unique, strong passwords for each account
- Consider using a password manager like Bitwarden, 1Password, or LastPass
- Never share your credentials

---

## Part 6: Integration Testing

Let's verify all accounts work together:

1. **Create a test file in Google Colab:**
   - Create a new notebook
   - Add a markdown cell with: `# My First AI Project`
   - Add a code cell with: `print("Integration Test Successful!")`
   - Save as "integration_test.ipynb"

2. **Upload to GitHub:**
   - Go to your "openai-course-labs" repository
   - Click "Add file" → "Upload files"
   - Upload your .ipynb file from Google Drive
   - Commit the changes

3. **Verify in Streamlit and Netlify:**
   - Log into Streamlit Cloud and confirm you can see the updated repository
   - Log into Netlify and verify the repository shows the new file

---

## Submission Requirements

Create a markdown file named `lab0_submission.md` in your GitHub repository with the following information:

```markdown
# Lab 0 Submission

## Account Confirmations

- **Google Colab Username:** [your-email]
- **GitHub Username:** [your-username]
- **GitHub Repository URL:** https://github.com/[your-username]/openai-course-labs
- **Streamlit Account:** ✓ Linked to GitHub
- **Netlify Account:** ✓ Linked to GitHub

## Security Checklist
- [ ] GitHub 2FA enabled
- [ ] Google 2FA enabled
- [ ] Strong passwords set for all accounts

## Integration Test
- [ ] Test notebook created in Colab
- [ ] File uploaded to GitHub
- [ ] Repository visible in Streamlit
- [ ] Repository visible in Netlify

## Questions or Issues
[List any problems encountered or questions you have]

Submitted by: [Your Name]
Date: [Submission Date]
```

---

## Troubleshooting

### Common Issues and Solutions:

**GitHub email verification not received:**
- Check spam/junk folder
- Request resend from GitHub settings
- Try using a different email address

**Streamlit won't connect to GitHub:**
- Ensure you're logged into GitHub first
- Clear browser cookies and try again
- Try using an incognito/private browser window

**Netlify authorization failing:**
- Revoke access in GitHub settings → Applications
- Clear browser cache
- Re-authorize from Netlify

**Google Colab won't save notebooks:**
- Ensure you're signed into Google Drive
- Check available Google Drive storage
- Try saving to GitHub directly from Colab (File → Save a copy in GitHub)

---

## Next Steps

Congratulations! You now have all the necessary accounts for our OpenAI/GPT-5 course. In the upcoming labs, we will:

- Lab 1: Introduction to the OpenAI API and basic prompt engineering
- Lab 2: Building your first GPT-powered application in Google Colab
- Lab 3: Creating an interactive Streamlit app with GPT integration
- Lab 4: Deploying your AI application to the web using Netlify

## Additional Resources

- [Google Colab Documentation](https://colab.research.google.com/notebooks/welcome.ipynb)
- [GitHub Learning Lab](https://lab.github.com/)
- [Streamlit Documentation](https://docs.streamlit.io/)
- [Netlify Documentation](https://docs.netlify.com/)
- [OpenAI API Documentation](https://platform.openai.com/docs)

---

## Support

If you encounter any issues during setup:
1. Check the troubleshooting section above
2. Search for solutions in the platform's help documentation
3. Post questions in the course discussion forum
4. Contact the instructor during office hours

Remember: These accounts will be essential for all future labs and projects in this course, so ensure everything is properly configured before proceeding.