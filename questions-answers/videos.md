---
layout: default
title: Videos & Other Large Media
parent: Questions & Answers
nav_order: 4
---

# Videos and Other Large Media
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Protocols for Video Uploads & Downloads ##

**Question**:
What protocols are typically used for videos and other large media, both upload and download? Assume we're talking about a YouTube-esque scenario in which clients are given presigned URLs to blob storage such as S3.

**Answer**:
For YouTube-style video platforms, using presigned URLs to blog storage, the protocols vary significantly between upload and download scenarios.

### Video Downloads (Streaming / Playback) ###
**HTTP / HTTPS** is overwhelmingly dominant for video downloads:
* **Adaptive streaming protocols** such as HLS (HTTP live streaming) and DASH (dynamic adaptive streaming over HTTP) use standard HTTP
* **Progressive download** for smaller videos also uses HTTP / HTTPS
* **Benefits**: Works through firewalls, leverages CDN infrastructure, supports range requests for seeking
* **S3 integration**: Presigned URLs work seamlessly for HTTP GET requests

**Why not specialized protocols?** While protocols such as RTMP or RTSP exist for streaming, modern platforms favor HTTP-based streaming because:
* Better firewall / proxy compatibility
* Easier CDN integration
* Simpler client implementation
* Can leverage existing HTTP infrastructure

### Video Uploads ###
This is where things get more interesting. Several protocols are commonly used:

#### HTTP / HTTPS with Multipart Upload ####
* Most common for large files
* S3's multipart upload API allows chunking larger videos
* Supports resumable uploads
* Presigned URLs can be generated for each chunk

#### WebRTC DataChannel ####
* Real-time uploads (live streaming scenarios)
* p2p or c2s
* low latency but more complex

#### Resumable Upload Protocols ####
* **tus.io protocol** over HTTP
* Google's resumable upload protocol
* Custom implementations using HTTP range headers

### Large Media-Specific Considerations ####
**Chunking / segmentation**: Large files typically broken into smaller chunks of 1-10 MB regardless of protocol. This enables:
* Parallel uploads
* Resume capability on failures
* Better error handling

**Range requests**: Both uploads and downloads heavily use HTTP range headers to handle partial content transfers.

### Real-World Implemtnation Patterns ###
**Upload Flow**:
```
Client → Presigned URL (HTTP POST/PUT) → S3 Multipart Upload
```

**Download Flow**:
```
Client → CDN (HTTP GET with ranges) → S3 → Adaptive streaming chunks
```

#### Alternative Upload Patterns: ####
* Some platforms use **WebSocket** connections for upload progress and control
* **HTTP/2** for multiplexing multiple upload streams
* **HTTP/3 (QUIC)** increasingly used for better performance over unreliable connections

### Protocol Evolution ###
The trend is towards **HTTP-based everything**:
* HTTP/2 and HTTP/3 have addressed many performance limitations of HTTP/1.1
* CDNs and edge infrastructure are optimized for HTTP
* Tooling and monitoring are built around HTTP
* Security models (TLS, certificiates) are well-established for HTTP

**Bottom line**: For a YouTube-style platform with presigned URLs, you're almost certainly looking at HTTP/HTTPS for both UL and DL, with multipart uploads for large files and adaptive streaming protocols (HLS/DASH) for playback. The presigned URL pattern works perfectly with this HTTP-centric approach.

