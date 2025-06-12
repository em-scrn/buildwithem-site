---
title: "Building a Professional Blog with Claude, Hugo, and AWS"
date: 2025-06-10
description: "How I used Claude AI to create a complete Hugo blog with Blowfish theme and deployed it to AWS with Terraform"
tags: ["claude", "hugo", "aws", "terraform", "automation", "ai-assisted-development"]
categories: ["DevOps", "AI Tools"]
showHero: false
---

## Why I Built This Blog (And Why You Should Build Your Own)

As a DevOps engineer in a world that's quickly adopting AI, having a professional online presence is crucial. I wanted a platform to share technical insights, document my learning journey, and showcase my skills to potential employers. After exploring various options, I decided to build a static blog using Hugo and deploy it to AWS - but with a twist: I used Claude AI to accelerate the entire development process (also called 'Vibe Coding' in 2025 lol).

This post details exactly how I used Claude to build this very blog you're reading, complete with Infrastructure as Code, CI/CD pipeline, and best practices.

## The Stack: Why These Technologies?

Before diving into the implementation, let me explain why I chose this particular stack:

### [Hugo](https://gohugo.io/) + [Blowfish Theme](https://blowfish.page/)
- **Performance**: Static sites are lightweight and load very quickly
- **Theme Ecosystem**: Hugo's extensive theme gallery lets you quickly pick and customize a professional design without starting from scratch
- **Developer-friendly**: Write posts in Markdown format
- **Minimal**: Blowfish theme offers modern design with dark/light modes

### AWS Infrastructure
- **[S3](https://aws.amazon.com/s3/) + [CloudFront](https://aws.amazon.com/cloudfront/)**: Cost-effective global content delivery
- **[AWS Web Application Firewall (WAF)](https://aws.amazon.com/waf/)**: Built-in security against common web attacks
- **[Route53](https://aws.amazon.com/route53/)**: Professional DNS management
- **[ACM](https://aws.amazon.com/certificate-manager/)**: Free SSL certificates

### Infrastructure as Code
- **[Terraform](https://www.terraform.io/)**: Reproducible, version-controlled infrastructure
- **[GitHub Actions](https://github.com/features/actions)**: Automated deployments on every push

## Using Claude to Build the Site

Here's where it gets interesting. Instead of spending hours researching Hugo configurations and AWS best practices, I collaborated with Claude to rapidly prototype and build the entire solution. I was already aware of how the AWS stack works due to my experience at AA Insurance, so I had a good idea of the infrastructure requirements.

## Prerequisites

Before we dive into the implementation, make sure you have:

**Required Tools:**
- [Hugo installed](https://gohugo.io/installation/) on your machine
- [Git](https://git-scm.com/downloads) for version control
- [AWS CLI](https://aws.amazon.com/cli/) for infrastructure deployment
- [Terraform](https://www.terraform.io/downloads) for Infrastructure as Code

**Accounts Needed:**
- [GitHub account](https://github.com) (free) - for code repositories
- [AWS account](https://aws.amazon.com) (free tier available) - for hosting infrastructure
- [Claude AI access](https://claude.ai) (free tier available) - for AI assistance

**Domain Setup (Choose One Option):**

**Option 1: Custom Domain (Recommended)**
- Purchase a domain from any registrar (GoDaddy, Namecheap, etc.) and transfer DNS management to Route53 for easier setup
- OR Purchase a domain directly from Route53 via [AWS Console](https://console.aws.amazon.com/route53/domains/home) or [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/route53domains/register-domain.html)
- Follow [AWS Route53 documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html) for domain registration

**Option 2: Use S3 Bucket Domain (Free)**
- Your site will be accessible via: `yourbucketname.s3-website-region.amazonaws.com`
- Follow [S3 static website hosting guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)
- Note: You'll need to modify the Terraform configuration to exclude Route53 and ACM certificate resources

**Basic Knowledge:**
- Comfortable with command line/terminal
- Basic understanding of Git (clone, commit, push)
- Familiarity with text editors

## Step-by-Step Implementation

### Step 1: Theme Selection & Initial Planning with Claude

**First, choose your Hugo theme.** Before working with Claude, browse the [Hugo Themes gallery](https://themes.gohugo.io/) and pick a theme that fits your style and needs. I chose Blowfish because of its minimal design and built-in table of contents feature - perfect for technical blog posts.

Once you've selected your theme, start your collaboration with Claude using a prompt like this:
```
Let's build a web app, a simple static site that has a blog and can display my resume.
I want to include this web app in my resume when I apply for roles. I'm a DevOps
engineer and I would like to add musings and learnings to the blog. I think Hugo is
a good place to start for the front end and I want it deployed to AWS S3 and
CloudFront with WAF.
```

**What to expect from Claude's response:**
- Complete Hugo site structure and folder layout
- Configuration files for your chosen theme
- Sample content for pages (About, Resume, Blog posts)
- GitHub Actions workflow for deployment
- Terraform infrastructure code for AWS

Use Claude's response as your blueprint for the next step - setting up your local development environment and repositories.

### Step 2: Repository Setup and Configuration

Instead of keeping everything in one repository, I recommend creating two separate Git repositories:
- **`blogname-site`**: Contains the Hugo site, content, themes, and deployment workflows
- **`blogname-infra`**: Contains Terraform infrastructure code, AWS configurations, and infrastructure CI/CD

This separation provides several benefits:
- **Different deployment cadences**: Site updates happen frequently, infrastructure changes are less frequent
- **Access control**: Different team members can have different permissions for site vs infrastructure
- **Cleaner CI/CD**: Each repo has its own focused pipeline
- **Better organization**: Infrastructure as Code stays separate from application code

**Create your repositories on GitHub:**
1. Go to [GitHub](https://github.com) and create two new public repositories
2. Use the naming convention: `blogname-site` and `blogname-infra`
3. **Important:** Initialize both repositories with README files (check the "Add a README file" option)

**Set up your local development environment:**
With both Git repositories created, clone them locally to set up your workspace:

```bash
# Create project folder and navigate to it
mkdir blogname
cd blogname

# Clone both repositories
git clone https://github.com/yourusername/blogname-site.git
git clone https://github.com/yourusername/blogname-infra.git

# Your folder structure should now look like:
# blogname/
# ├── blogname-site/
# └── blogname-infra/
```

For reference, here are my repositories: [`buildwithem-site`](https://github.com/em-scrn/buildwithem-site) and [`buildwithem-infra`](https://github.com/em-scrn/buildwithem-infra)

### Step 3: Site Setup

With your repositories cloned locally, it's time to set up the Hugo site. Navigate to your site repository and initialize the Hugo project:

```bash
# Navigate to the site repository
cd blogname-site

# Create new Hugo site (use --force since the directory isn't empty)
hugo new site . --force

# Add your chosen theme as a git submodule
# Replace 'blowfish' with your chosen theme name and repository URL
git submodule add -b main https://github.com/nunocoracao/blowfish.git themes/blowfish

# For other themes, the command would look like:
# git submodule add [theme-repo-url] themes/[theme-name]

# Start the development server to test
hugo server
```

Use this Claude prompt to generate the Hugo configuration files and folder structure:
Important: Modify this prompt to match your chosen theme:
```
I've set up a Hugo site with the [YOUR_THEME_NAME] theme. I need you to generate the complete configuration files for a DevOps engineer's blog and resume site. Please provide:
- config/_default/config.yaml
- config/_default/params.yaml  
- config/_default/menus.yaml
- Sample content for blog, about, and resume pages
- Proper folder structure
```
Once Claude has generated the files, copy them to the appropriate files as per the folder structure provided:

```bash
buildwithem-site/
├── config/
│   └── _default/
│       ├── config.yaml
│       ├── params.yaml
│       └── menus.yaml
├── content/
│   ├── blog/
│   │   └── _index.md
│   ├── about.md
│   └── resume.md
├── layouts/
│   └── 404.html
├── themes/
│   └── blowfish/
├── assets/
├── .github/
│   └── workflows/
│       └── deploy.yml
└── .gitignore
```

Test your changes locally: Make your changes as required and apply them to the site by running the Hugo development server:
```bash
# Use command in terminal
hugo server --disableFastRender
```
You can keep this server running in your terminal and whenever you make changes and save them, the local version in your browser (typically http://localhost:1313) will automatically refresh with the new changes.

### Step 4: Infrastructure Setup

Based on my previous experience at AA Insurance, I knew that a simple way to host static sites is with S3, CloudFront and WAF. Claude helped me quickly create a Terraform main.tf for this infrastructure.

**First, ensure you have the required tools installed:**

```bash
# Install Terraform & AWS CLI via Homebrew
brew install terraform awscli

# Verify installations
terraform --version
aws --version
```

**Configure your AWS credentials:**

1. **Create an IAM user with appropriate permissions**: Your AWS user needs these IAM permissions for Terraform to work:
  - `AmazonS3FullAccess`
  - `CloudFrontFullAccess`
  - `AmazonRoute53FullAccess`
  - `AWSWAFv2FullAccess`
  - `AWSCertificateManagerFullAccess`
  
  You can attach these policies in the AWS IAM Console under your user's permissions.

2. **Generate AWS Access Keys**: Go to AWS Console → IAM → Users → Your User → Security Credentials → Create Access Key

3. **Configure AWS CLI**:

```bash
aws configure
# Enter your AWS Access Key ID
# Enter your AWS Secret Access Key  
# Enter your default region (e.g., us-east-1)
# Enter default output format (json)
```

Use this prompt to generate your Terraform infrastructure:
```
I need Terraform code to deploy a static website to AWS. The infrastructure should include:
- S3 bucket for static website hosting
- CloudFront distribution for global CDN
- WAF for security protection
- Route53 for DNS management
- ACM certificate for SSL
- Use Origin Access Control (not OAI) for S3 access
- Include proper security policies and best practices
```

Claude will generate the Terraform files with this structure:

```bash
blogname-infra/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars.example
└── .gitignore
```

terraform.tfvars.example
```hcl
region          = "ap-southeast-2"
domain_name     = "blogname.com"
www_domain_name = "www.blogname.com"
waf_name        = "blogname-web-acl"
cdn_tag         = "blogname-cdn"
oac_name        = "blogname-s3-oac"
oac_description = "OAC for secure S3 access"
```
Note: When you copy this to terraform.tfvars, make sure to:

Replace "blogname.com" with your actual domain name
Update the region to your preferred AWS region (e.g., "us-east-1" for US East)
Replace "blogname" in all resource names with your project name for consistency

Copy the generated Terraform code into the appropriate files according to this structure.

**Set up your infrastructure repository:**

```bash
# Navigate to your infrastructure repository
cd ../blogname-infra

# Copy the Terraform files that Claude generated
# Create your terraform.tfvars file from the example
cp terraform.tfvars.example terraform.tfvars
# Edit terraform.tfvars with your domain name and other variables

# Initialize Terraform
terraform init

# Plan the infrastructure changes
terraform plan

# Apply the infrastructure (after reviewing the plan)
terraform apply

# Get important outputs for GitHub secrets
terraform output cloudfront_distribution_id
terraform output s3_bucket_name
```

You can see the complete infrastructure code in my [buildwithem-infra repository](https://github.com/em-scrn/buildwithem-infra) for reference.

### Step 5: Deployment Pipeline

With your site configured and infrastructure ready, it's time to set up automated deployments. This ensures that every time you push changes to your blog, they automatically deploy to your AWS infrastructure.

**Use this Claude prompt to generate your GitHub Actions workflow:**
```
I need a GitHub Actions workflow to automatically deploy my Hugo site to AWS S3 and invalidate CloudFront. The workflow should:

Build the Hugo site with the Blowfish theme
Deploy to S3 with optimized caching headers
Invalidate CloudFront distribution
Only deploy on pushes to the main branch
Use GitHub secrets for AWS credentials
```
Claude will generate a workflow file that you should save as `.github/workflows/deploy.yml` in your site repository.

**Configure GitHub Secrets:**
In your GitHub repository settings, add these secrets:
- `AWS_ACCESS_KEY_ID`: Your AWS access key
- `AWS_SECRET_ACCESS_KEY`: Your AWS secret key
- `S3_BUCKET_NAME`: Your domain name (matches your S3 bucket)
- `CLOUDFRONT_DISTRIBUTION_ID`: From your Terraform output

**Test your deployment:**
```bash
# Commit and push your changes
git add .
git commit -m "Initial site setup"
git push origin main

# Check the Actions tab in GitHub to monitor the deployment
```

Your site should now automatically deploy whenever you push changes!
This completes the full workflow from setup to automated deployment! 🚀

---

## AWS Infrastructure Deep Dive

### Security First
The Terraform configuration includes enterprise-grade security:

```hcl
resource "aws_wafv2_web_acl" "web" {
  name        = var.waf_name
  description = "WAF for CloudFront"
  scope       = "CLOUDFRONT"

  rule {
    name     = "AWS-AWSManagedRulesCommonRuleSet"
    priority = 1
    statement {
      managed_rule_group_statement {
        name        = "AWSManagedRulesCommonRuleSet"
        vendor_name = "AWS"
      }
    }
  }
}
```
### Performance Optimization
- **CloudFront**: Global CDN for fast content delivery
- **S3 Transfer Acceleration**: Optimized uploads
- **Gzip Compression**: Reduced bandwidth usage
- **Optimized Caching**: Static assets cached for 1 year, HTML for 1 hour

### Cost Optimization
The entire infrastructure costs less than $5/month for a professional blog:
- S3 storage: ~$1/month
- CloudFront: ~$2/month
- Route53: ~$0.50/month
- WAF: ~$1/month
## Lessons Learned & Conclusion

Building this blog with Claude was a game-changer. What would have taken several weekends of research, configuration, and troubleshooting was completed in a few focused hours.

### What Claude Excels At
- **Rapid Prototyping**: Complete site structure generated in minutes vs hours of research
- **Best Practices**: Automatically incorporated modern AWS practices like Origin Access Control
- **Documentation**: Generated comprehensive setup guides and configuration files
- **Problem Solving**: Quickly adapted workflows to work with existing infrastructure

### Where Human Expertise Still Matters
- **Architecture Decisions**: I chose the S3+CloudFront approach based on real-world experience
- **Requirements Refinement**: Specific prompts came from understanding what I actually needed
- **Troubleshooting**: DevOps experience was crucial for debugging deployment issues
- **Customisation**: Adapting generated code to specific requirements and constraints

The key to success was combining AI assistance with DevOps expertise. Claude provided the implementation speed, but my experience guided the architectural decisions and ensured production-ready quality.

**Ready to build your own AI-assisted blog?** 

1. Start with the prerequisites and follow the step-by-step guide above
2. Customize the prompts based on your specific needs and experience
3. Don't be afraid to iterate—Claude excels at refining and improving solutions
4. Remember: you bring the expertise, AI brings the speed

As AI tools continue to evolve, they're becoming essential multipliers for engineers. The question isn't whether to use them, but how to use them effectively to amplify your existing skills and deliver better solutions faster.

**Have questions about the implementation or want to discuss AI-assisted development strategies?** Feel free to reach out through the links in my [about page](/about) or connect with me on [LinkedIn](https://linkedin.com/in/eunicesocorin). I'd love to hear about your own experiments with AI-assisted development!

---

*P.S. The complete source code for this blog is available in my GitHub repositories: [buildwithem-site](https://github.com/em-scrn/buildwithem-site) and [buildwithem-infra](https://github.com/em-scrn/buildwithem-infra). Star them if you found this helpful!*