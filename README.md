# GitHub Profile Audio Introduction 
## Built with AWS Polly + S3 + CloudFront to learn practical & hands-on Cloud Architecture 

### AWS Cloud Architecture & Hybrid Content Delivery

An end-to-end cloud engineering project that transforms a professional introduction into synthesized speech using Amazon Polly, securely stores the generated audio in Amazon S3, and delivers it through Amazon CloudFront.

The project combines secure cloud infrastructure with GitHub portfolio integration to demonstrate practical knowledge of AWS, cloud security, networking, and content delivery.

<div align="center">

###  Listen to My Audio Introduction

[![Play Audio Introduction](https://img.shields.io/badge/Play_Audio_Introduction-Amazon_CloudFront-8C4FFF?style=for-the-badge&logo=amazonwebservices&logoColor=white)](https://d3hdbo4wynu8c.cloudfront.net/intro.mp3)

*Delivered directly through Amazon CloudFront over HTTPS.*

</div>

---

## Motivation

This project originated from a desire to make my GitHub profile more interactive while developing practical cloud engineering skills beyond theoretical learning.

Rather than simply uploading an audio file to GitHub, I designed and implemented a cloud architecture to explore:

- Managed AWS services and cloud infrastructure.
- Secure object storage and least-privilege access control.
- Content delivery networks and distributed edge caching.
- HTTP protocols, HTTPS communication, and request authentication.
- Cost-conscious cloud architecture and infrastructure validation.

## Technology Stack

| Technology | Application |
|---|---|
| Amazon Polly | Text-to-speech synthesis |
| SSML | Speech customization, pronunciation, and pacing |
| Amazon S3 | Secure object storage |
| Amazon CloudFront | Distributed content delivery and edge caching |
| Origin Access Control (OAC) | Secure CloudFront-to-S3 access |
| AWS Signature Version 4 | Origin request authentication |
| AWS Budgets | Cloud expenditure monitoring and alerts |
| Canva | MP4 creation using synthesized audio and a static image |
| GitHub | Portfolio integration and technical documentation |
| Draw.io (diagrams.net) | Cloud architecture design and visualization |
| Chrome DevTools | Network inspection and browser-based validation |
| cURL | Command-line HTTP testing |

## AWS Architecture

![AWS Architecture Diagram](./aws-architecture.png)

The architecture separates content generation, persistent storage, content delivery, and portfolio presentation into distinct responsibilities.

Amazon S3 remains private, while CloudFront serves as the public-facing HTTPS delivery endpoint. GitHub independently hosts the MP4 version for convenient inline playback.

## Methodology

### 1. Speech Synthesis

- Developed a professional introduction using Speech Synthesis Markup Language (SSML).
- Configured Amazon Polly's Standard engine with the Matthew voice.
- Customized pronunciation, speech rate, pitch, and pauses.
- Generated a 22,050 Hz MP3 audio file.

### 2. Secure Cloud Storage

- Provisioned a private Amazon S3 bucket in US East (Ohio), `us-east-2`.
- Manually uploaded the Polly-generated MP3.
- Enabled all four S3 Block Public Access settings.
- Configured server-side encryption using SSE-S3 (AES-256).
- Restricted CloudFront origin access to the specific audio object through a least-privilege bucket policy.

### 3. Distributed Content Delivery

- Configured Amazon CloudFront with the private S3 bucket as its origin.
- Implemented Origin Access Control (OAC) with SigV4 request signing.
- Enforced HTTPS through HTTP-to-HTTPS redirection.
- Restricted viewer methods to GET and HEAD.
- Configured edge caching using the managed CachingOptimized policy.

### 4. Hybrid GitHub Integration

GitHub does not natively support inline playback of arbitrary externally hosted MP3 files within README documents. To overcome this limitation, I implemented two independent delivery mechanisms:

| Delivery Mechanism | Implementation |
|---|---|
| Inline playback | Combined the Polly-generated MP3 with a static image using Canva, exported an MP4, and embedded it in my GitHub profile README. |
| Cloud-hosted playback | Made the original MP3 publicly accessible through CloudFront, with direct playback links intended for both my profile and project READMEs. |

### 5. Cost Monitoring

- Configured AWS Budgets with expenditure alerts.
- Avoided unnecessary compute infrastructure and optional paid features.
- Generated the audio once rather than synthesizing speech for every playback request.

## Testing & Validation

The architecture was validated using Chrome DevTools, incognito browser sessions, and cURL.

| Validation Test | Result |
|---|---|
| Anonymous direct S3 access blocked | Passed |
| Public CloudFront audio playback | Passed |
| HTTP-to-HTTPS redirection | Passed |
| HTTP 200 full-content retrieval | Passed |
| HTTP 206 byte-range retrieval | Passed |
| CloudFront cache hit and miss validation | Passed |
| Cache invalidation and repopulation | Passed |
| GitHub inline MP4 playback | Passed |

The complete CloudFront cache lifecycle was successfully validated:

`Cache Miss → Cache Hit → Invalidation → Cache Miss → Cache Hit`

## Limitations & Considerations

- **Speech synthesis:** The Standard Polly engine was selected due to account-level availability constraints.
- **Manual deployment:** Audio generation, file transfers, and multimedia updates are not automated.
- **Content synchronization:** The GitHub-hosted MP4 and AWS-hosted MP3 must be updated independently.
- **Public delivery:** The S3 origin is private, but the CloudFront audio endpoint is intentionally publicly accessible.
- **Cost management:** AWS Budgets provides expenditure alerts rather than a hard spending cap. Future hosting costs depend on account status and service usage.
- **Performance:** Cache behaviour was validated, but formal load testing and global latency benchmarking were outside the project scope.
- **Operational visibility:** Advanced logging, AWS WAF, and automated infrastructure provisioning were not implemented.


## Project Resources

- [Original SSML Source](./intro.ssml)
- [AWS Architecture Diagram](./aws-architecture.png)
- [CloudFront Audio Endpoint](https://d3hdbo4wynu8c.cloudfront.net/intro.mp3)

