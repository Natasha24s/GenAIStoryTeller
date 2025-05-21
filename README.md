Here's the GitHub-friendly markdown format:

# GenAI Story Teller Pipeline with AWS Services

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

![Architecture Diagram](https://github.com/Natasha24s/GenAIStoryTeller/blob/main/Architecture.png)

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

You can use the API in two steps:

Start the process:

```
curl -X POST \
  https://your-api-endpoint.execute-api.region.amazonaws.com/prod/generate \
  -H "Content-Type: application/json" \
  -d '{"topic": "A day at the beach"}'
```

    
This will return:

```
{
  "executionArn": "arn:aws:states:region:account:execution:StateMachine:execution-id",
  "startDate": "2025-05-01T15:53:38.000Z",
  "message": "Story generation process started successfully",
  "status": "IN_PROGRESS"
}
```
   
Check the status and get the final result:

```
curl "https://your-api-endpoint.execute-api.region.amazonaws.com/prod/status?executionArn=arn:aws:states:region:account:execution:StateMachine:execution-id"
```    
When the process is complete, you'll get the video generation result:

```
{
  "status": "Completed",
  "story_id": "20250501_a_day_at_the_beach_abc123",
  "source_bucket": "your-source-bucket",
  "destination_bucket": "your-destination-bucket",
  "output_location": "s3://your-destination-bucket/job-id/output.mp4",
  "timestamp": "2025-05-01 15:53:38",
  "message": "Video generation completed successfully"
}
```    
If the process is still running, you'll get:

```
{
  "executionArn": "arn:aws:states:region:account:execution:StateMachine:execution-id",
  "status": "RUNNING",
  "startDate": "2025-05-01T15:53:38.000Z",
  "message": "Execution in progress"
}
```

To test the API Gateway GET endpoint in the AWS Console, follow these steps:

Open AWS Console and navigate to API Gateway
Select your API (StoryGeneratorAPI)
In the left navigation pane, click on "Resources"
Click on the GET method under /status
Click the "TEST" button
In the Query Strings section, add:

```
executionArn = arn:aws:states:us-east-1:your-account-id:execution:StoryProcessingStateMachine-xxx:execution-id
```
The response should look like:

```    
{
  "status": "RUNNING",
  "startDate": "2025-05-01T15:53:38.000Z",
  "executionArn": "arn:aws:states:us-east-1:your-account-id:execution:StoryProcessingStateMachine-xxx:execution-id",
  "message": "Execution in progress"
}
```

    
Or if completed:

```
{
  "status": "Completed",
  "story_id": "20250501_a_day_at_the_beach_abc123",
  "source_bucket": "your-source-bucket",
  "destination_bucket": "your-destination-bucket",
  "output_location": "s3://your-destination-bucket/job-id/output.mp4",
  "timestamp": "2025-05-01 15:53:38",
  "message": "Video generation completed successfully"
}
```
The process flow is:

```    
GenerateStory (First Lambda)
    ↓
    - Generates scenes
    - Creates polly_input narrative
    ↓
GenerateVideo (Second Lambda)
    ↓
    - Creates silent video using Nova Reel
    - Saves to video_output_location
    ↓
GenerateAudioVideo (Third Lambda)
    ↓
    - Generates narration audio using Polly
    - Uses MediaConvert to merge:
      * Input video from video_output_location
      * Narration audio from Polly
    - Saves final merged video to final_output_location

 ```   

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
