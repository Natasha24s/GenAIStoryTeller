Here's the GitHub-friendly markdown format:

# Video Generation Pipeline with AWS Services

A serverless solution that automatically generates narrative videos from text prompts using AWS AI services.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![AWS: v1](https://img.shields.io/badge/AWS-v1-orange.svg)](https://aws.amazon.com/)

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Deployment](#deployment)
- [Usage](#usage)
- [Testing](#testing)
- [Output Examples](#output-examples)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Overview

This pipeline automatically:
- Generates story scenes from a topic
- Creates images for each scene
- Produces narrative voiceover
- Combines everything into a final video

### Features
- 🚀 Fully serverless architecture
- 🎯 API-driven workflow
- 🔄 Real-time status monitoring
- 🛠️ Error handling and recovery
- 📊 Progress tracking

## Architecture

![Architecture Diagram](path/to/architecture.png)

### Components Used
- AWS Lambda
- AWS Step Functions
- Amazon Bedrock
- Amazon Polly
- AWS MediaConvert
- Amazon S3
- Amazon API Gateway

## Prerequisites

1. AWS Account with appropriate permissions
2. AWS CLI installed and configured
3. Two S3 buckets (source and destination)
4. Node.js 14+ (for deployment scripts)

## Deployment

### 1. Clone Repository

```bash
git clone https://github.com/yourusername/video-generation-pipeline
cd video-generation-pipeline
```

### 2. Deploy CloudFormation Stack

```bash
aws cloudformation create-stack \
  --stack-name video-generation-pipeline \
  --template-body file://template.yaml \
  --parameters \
    ParameterKey=SourceBucketName,ParameterValue=your-source-bucket \
    ParameterKey=DestinationBucketName,ParameterValue=your-destination-bucket \
  --capabilities CAPABILITY_IAM
```

### 3. Note Important Outputs

```bash
aws cloudformation describe-stacks \
  --stack-name video-generation-pipeline \
  --query 'Stacks[0].Outputs'
```

## Usage

### Generate a Video

```bash
curl -X POST \
  https://your-api-endpoint/prod/generate \
  -H "Content-Type: application/json" \
  -d '{"topic": "A day at the beach"}'
```

### Check Generation Status

```bash
curl "https://your-api-endpoint/prod/status?executionArn=your-execution-arn"
```

## Testing

### 1. Basic Integration Test

```bash
# Start generation
curl -X POST \
  https://your-api-endpoint/prod/generate \
  -H "Content-Type: application/json" \
  -d '{"topic": "Test story"}'

# Check status
curl "https://your-api-endpoint/prod/status?executionArn=your-execution-arn"
```

### 2. Console Testing

1. Open AWS Console
2. Navigate to API Gateway
3. Select your API
4. Test endpoints using the built-in interface

## Output Examples

### Successful Generation
```json
{
  "status": "Completed",
  "story_id": "20250501_test_story_abc123",
  "outputs": {
    "initial_video": {
      "status": "Completed",
      "location": "s3://bucket/job-id/output.mp4",
      "timestamp": "2025-05-01 15:53:38"
    },
    "final_video": {
      "status": "Completed",
      "location": "s3://bucket/story_id/final/final_output.mp4",
      "timestamp": "2025-05-01 16:00:00"
    }
  }
}
```

### Error State
```json
{
  "status": "Failed",
  "error": "Error description",
  "timestamp": "2025-05-01 15:53:38"
}
```

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

---
