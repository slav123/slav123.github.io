---
title: "Email Catch: From Weekend Project to Amazon SES Alternative"
author: slav
type: post
date: 2025-07-20T16:42:07+02:00
url: /2025/email-catch-en/
description: "High-performance SMTP service written in Go as an alternative to Amazon SES. From weekend project to production-ready tool for receiving and processing emails."
thumbnail: /images/2025/Google_AI_Studio_2025-07-20T14_49_37.277Z.webp
categories:
- Blog
- Go
- DevOps
translationKey: "email-catch-2025"
---
As a developer who enjoys weekend experiments, I decided to tackle a problem that probably affects many of us - how to simply receive and process emails without having to use complicated cloud solutions.

## Project Genesis

The Email Catch story is a typical evolution path of a weekend project. It all started with a simple need: I wanted control over how my application receives emails. I wrote the first version in Node.js - quickly, functionally, then Go appeared on the scene.

I decided to rewrite everything. I used the "vibe coding" approach - since my knowledge of Go was already somewhat rooted.

## Why Not Serverless?

When Amazon SES appeared, the natural choice seemed to be using serverless solutions and setting everything up on AWS Lambda. And indeed - for a while, that's what I did. However, over time, the complexity of the entire solution began to overwhelm me:

- Configuring multiple Lambda functions
- Managing IAM roles
- Debugging distributed processes
- Vendor lock-in

All of this led me to conclude that I needed something simpler, more controllable.

## What Was Built?

Email Catch is a high-performance SMTP service written in Go that:

**Receives emails** - just set an MX record to our server and all emails sent to our domain go directly to the application.

**Routing on steroids** - a rule system allows email redirection based on domain and recipient pattern.

**Flexible storage** - support for:
- Local storage (`.eml` files)
- S3-compatible solutions (AWS S3, MinIO, DigitalOcean Spaces)

**Webhooks** - instant notifications with complete email content in JSON format

## Integration with Automation

The real power of Email Catch is revealed when combined with automation tools like [n8n](https://n8n.io/). Thanks to webhooks, you can easily:

**Process invoices** - automatically extract data from PDF attachments and pass it to accounting systems

**Notifications** - instant Slack alerts about new emails from specific domains and senders

**Archiving** - intelligent storage of important emails with categorization

## Security

Email Catch was designed with security in mind:

- **TLS/SSL** - encrypted communication with automatic Let's Encrypt certificates
- **Multi-port support** - support for standard SMTP ports (25, 587, 465, 2525)
- **Graceful shutdown** - safe connection closing
- **Rate limiting** - protection against abuse

## Summary

Sometimes the best solutions arise from frustration with existing tools. Email Catch shows that we don't always need complicated cloud solutions - sometimes a simple, well-written service can be much more effective.

If you're looking for an alternative to Amazon SES or simply want full control over email receiving in your application, Email Catch might be the answer to your needs.

---

**Email Catch is available on GitHub**: https://github.com/slav123/email-catch

*The project is open source under MIT license - contributions welcome!*