# AWS MCP Server

A [Model Context Protocol (MCP)](https://www.anthropic.com/news/model-context-protocol) server that enables AI assistants like Claude to interact with your AWS environment. This allows for natural language querying and management of your AWS resources during conversations. Think of it as a better Amazon Q alternative that integrates directly with Claude Desktop.

![AWS MCP Demo](./images/aws-mcp-demo.png)

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Advanced Configuration](#advanced-configuration)
- [Troubleshooting](#troubleshooting)
- [Architecture](#architecture)
- [Contributing](#contributing)
- [License](#license)
- [Support](#support)

## Features

- 🔍 **Natural Language AWS Queries**: Query and modify AWS resources using conversational language
- ☁️ **Multiple AWS Profiles**: Support for AWS profiles and SSO authentication
- 🌐 **Multi-Region Support**: Work across different AWS regions seamlessly
- 🔐 **Secure Credential Handling**: Your credentials never leave your machine - only local AWS credentials are used
- 🏃‍♂️ **Local Execution**: Runs entirely on your local machine with your existing AWS configuration
- 🛠️ **Comprehensive AWS Service Coverage**: Supports EC2, S3, Lambda, ECS, and many other AWS services
- 📊 **Rich Data Presentation**: Get detailed information about your AWS resources in an easy-to-read format

## Prerequisites

Before installing AWS MCP Server, ensure you have the following:

### Required Software
- **[Node.js](https://nodejs.org/)** (version 18.0 or higher)
- **[Claude Desktop](https://claude.ai/download)** application
- **Package Manager**: Either [npm](https://www.npmjs.com/) (comes with Node.js) or [pnpm](https://pnpm.io/)

### AWS Configuration
- **AWS CLI configured** with your credentials in `~/.aws/` directory
- **Valid AWS credentials** through one of these methods:
  - AWS CLI (`aws configure`)
  - AWS SSO (`aws sso login`)
  - Environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
  - IAM roles (for EC2 instances)

### Permissions
Your AWS credentials should have appropriate permissions for the resources you want to query. Common permissions include:
- `ec2:Describe*` for EC2 resources
- `s3:ListBucket`, `s3:GetBucketLocation` for S3 operations
- `lambda:ListFunctions`, `lambda:GetFunction` for Lambda operations
- `ecs:ListClusters`, `ecs:ListServices` for ECS operations

> **Note**: The MCP server uses AWS SDK v2 and respects your local AWS configuration and credentials.

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/jonparker/aws-mcp.git
cd aws-mcp
```

### 2. Install Dependencies

Choose your preferred package manager:

**Using npm:**
```bash
npm install
```

**Using pnpm:**
```bash
pnpm install
```

### 3. Verify Installation

Test that the MCP server can start correctly:

```bash
npm run start
# or
pnpm start
```

You should see output indicating the server is running. Press `Ctrl+C` to stop the test run.

> **Important**: Make note of the full path to your `aws-mcp` directory - you'll need this for the Claude Desktop configuration.

## Configuration

### Claude Desktop Setup

1. **Open Claude Desktop Settings**
   - Launch Claude Desktop application
   - Go to **Settings** → **Developer** → **Edit Config**

   ![Claude Settings](./images/desktop_settings.png)

2. **Edit Configuration File**
   
   Add the following entry to your `claude_desktop_config.json` file:

   **For npm users:**
   ```json
   {
     "mcpServers": {
       "aws": {
         "command": "npm",
         "args": [
           "--silent",
           "--prefix",
           "/path/to/your/aws-mcp",
           "start"
         ]
       }
     }
   }
   ```

   **For pnpm users:**
   ```json
   {
     "mcpServers": {
       "aws": {
         "command": "pnpm",
         "args": [
           "--silent",
           "--prefix",
           "/path/to/your/aws-mcp",
           "start"
         ]
       }
     }
   }
   ```

   > **⚠️ Important**: Replace `/path/to/your/aws-mcp` with the actual absolute path to your project directory.

3. **Restart Claude Desktop**
   
   After saving the configuration, completely restart the Claude Desktop application.

4. **Verify Connection**
   
   You should see a successful connection indicator in Claude:

   ![Claude MCP Connection Status](./images/verify_installation.png)

## Usage

Once configured, you can start using AWS MCP Server in your Claude conversations. Here are some examples to get you started:

### Getting Started Commands

1. **Profile Management**
   ```
   List available AWS profiles
   Switch to my production AWS profile
   What AWS profile am I currently using?
   ```

2. **Basic AWS Resource Queries**
   ```
   List all EC2 instances in my account
   Show me all S3 buckets with their sizes
   What Lambda functions are deployed in us-east-1?
   List all ECS clusters and their services
   ```

### Advanced Usage Examples

3. **Detailed Resource Information**
   ```
   Show me EC2 instances that are running and their instance types
   List S3 buckets created in the last 30 days
   Find all Lambda functions with Python runtime
   Show ECS services that are not running
   ```

4. **Cross-Service Queries**
   ```
   Which EC2 instances have public IP addresses?
   Show me all resources tagged with Environment:Production
   List all Lambda functions and their associated VPCs
   Find unused security groups
   ```

5. **Cost and Performance Insights**
   ```
   Show me my largest S3 buckets by size
   List EC2 instances by cost (most expensive first)
   Find Lambda functions with the highest invocation count
   ```

### Tips for Better Results

- **Be specific** about the AWS region if you need data from a particular region
- **Ask follow-up questions** to drill down into specific resources
- **Use natural language** - the MCP server understands context and intent
- **Combine services** in your queries for comprehensive insights

## Advanced Configuration

### Using with Node Version Manager (nvm)

If you're using nvm to manage Node.js versions, you'll need to specify the full path to your Node.js installation:

1. **Build the project first:**
   ```bash
   npm install  # or pnpm install
   ```

2. **Find your Node.js path:**
   ```bash
   which node
   # Example output: /Users/username/.nvm/versions/node/v20.10.0/bin/node
   ```

3. **Use this configuration in Claude Desktop:**
   ```json
   {
     "mcpServers": {
       "aws": {
         "command": "/Users/<USERNAME>/.nvm/versions/node/v20.10.0/bin/node",
         "args": [
           "/path/to/your/aws-mcp/node_modules/tsx/dist/cli.mjs",
           "/path/to/your/aws-mcp/index.ts"
         ]
       }
     }
   }
   ```

### Custom AWS Configuration

The MCP server respects standard AWS configuration methods:

**Environment Variables:**
```bash
export AWS_PROFILE=my-profile
export AWS_REGION=us-west-2
```

**AWS Config File (`~/.aws/config`):**
```ini
[profile my-profile]
region = us-west-2
output = json
```

**SSO Configuration:**
```bash
aws sso login --profile my-sso-profile
```

## Troubleshooting

### Common Issues and Solutions

#### 1. MCP Server Not Connecting

**Symptoms:** Claude shows "No MCP servers connected" or similar message.

**Solutions:**
- Verify the path in your Claude Desktop configuration is correct and absolute
- Ensure Node.js is installed and accessible
- Check that all dependencies are installed (`npm install` or `pnpm install`)
- Restart Claude Desktop completely after configuration changes

#### 2. AWS Credentials Not Found

**Symptoms:** Errors about missing AWS credentials or access denied.

**Solutions:**
- Run `aws configure list` to verify your AWS CLI configuration
- Check that your AWS credentials are valid: `aws sts get-caller-identity`
- For SSO users, ensure you're logged in: `aws sso login --profile your-profile`
- Verify your profile has the necessary IAM permissions

#### 3. Permission Denied Errors

**Symptoms:** "AccessDenied" or "UnauthorizedOperation" errors.

**Solutions:**
- Review the [Prerequisites](#prerequisites) section for required permissions
- Contact your AWS administrator to grant necessary permissions
- Use `aws iam simulate-principal-policy` to test permissions

#### 4. Region-Specific Issues

**Symptoms:** Resources not showing up or region errors.

**Solutions:**
- Specify the region in your queries: "List EC2 instances in us-west-2"
- Check your AWS profile's default region in `~/.aws/config`
- Some services are only available in specific regions

### Viewing Logs

To debug issues, check the MCP server logs:

**macOS:**
```bash
tail -n 50 -f ~/Library/Logs/Claude/mcp-server-aws.log
```

**General MCP logs:**
```bash
tail -n 50 -f ~/Library/Logs/Claude/mcp.log
```

**Manual Testing:**
You can also test the MCP server directly:
```bash
cd /path/to/aws-mcp
npm run start
```

### Getting Help

If you're still experiencing issues:

1. Check the [GitHub Issues](https://github.com/jonparker/aws-mcp/issues) for similar problems
2. Create a new issue with:
   - Your operating system and Node.js version
   - Claude Desktop version
   - Error messages from logs
   - Steps to reproduce the issue

## Architecture

### How It Works

AWS MCP Server acts as a bridge between Claude Desktop and your AWS environment:

```
Claude Desktop ↔ MCP Protocol ↔ AWS MCP Server ↔ AWS SDK ↔ AWS Services
```

1. **Claude Desktop** sends natural language requests via the Model Context Protocol
2. **AWS MCP Server** interprets the requests and generates appropriate AWS SDK calls
3. **AWS SDK** executes the calls using your local AWS credentials
4. **Results** are formatted and returned to Claude for presentation

### Technical Details

- **Runtime**: Node.js with TypeScript
- **AWS SDK**: Version 2 (AWS SDK for JavaScript)
- **Protocol**: Model Context Protocol (MCP) by Anthropic
- **Security**: Uses local AWS credentials, no data sent to external services
- **Execution**: All AWS operations run locally on your machine

### Supported AWS Services

The MCP server currently supports querying and managing:

- **Compute**: EC2, Lambda, ECS
- **Storage**: S3
- **Networking**: VPC, Security Groups, Load Balancers
- **Database**: RDS
- **Management**: CloudFormation, CloudWatch
- **Security**: IAM (read-only operations)

> **Note**: The server is designed to be extensible. Additional AWS services can be added by extending the code generation prompts and handlers.

## Contributing

We welcome contributions to improve AWS MCP Server! Here's how you can help:

### Development Setup

1. **Fork and clone the repository**
   ```bash
   git clone https://github.com/your-username/aws-mcp.git
   cd aws-mcp
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Make your changes**
   - The main server logic is in `index.ts`
   - Follow the existing code style and patterns
   - Test your changes thoroughly

4. **Test locally**
   ```bash
   npm run start
   ```

### Contribution Guidelines

- **Issues**: Check existing issues before creating new ones
- **Pull Requests**: 
  - Create descriptive PR titles and descriptions
  - Include tests for new functionality
  - Update documentation as needed
- **Code Style**: 
  - Use TypeScript
  - Follow existing patterns and conventions
  - Add comments for complex logic

### Areas for Contribution

- **New AWS Services**: Add support for additional AWS services
- **Error Handling**: Improve error messages and handling
- **Performance**: Optimize AWS SDK calls and response formatting
- **Documentation**: Improve examples and troubleshooting guides
- **Testing**: Add comprehensive test coverage

## License

This project is licensed under the ISC License. See the [LICENSE](LICENSE) file for details.

## Support

### Community Support

- **GitHub Issues**: [Report bugs or request features](https://github.com/jonparker/aws-mcp/issues)
- **Discussions**: Share ideas and get help from the community

### Professional Support

For enterprise support or custom implementations, please contact the maintainers through GitHub.

---

### Roadmap

Features currently in development:

- [ ] **MFA Support**: Enhanced multi-factor authentication handling
- [ ] **Credential Caching**: Cache SSO credentials to reduce refresh frequency
- [ ] **Enhanced Error Messages**: More detailed and actionable error descriptions
- [ ] **Performance Optimization**: Faster response times for large-scale queries
- [ ] **Additional AWS Services**: Support for more AWS services and operations

**Disclaimer**: This is an independent project and is not affiliated with Amazon Web Services, Inc. AWS is a trademark of Amazon.com, Inc. or its affiliates.

---

<div align="center">
  <a href="https://glama.ai/mcp/servers/ta7kdy57us">
    <img width="380" height="200" src="https://glama.ai/mcp/servers/ta7kdy57us/badge" alt="aws-mcp MCP server" />
  </a>
</div>
