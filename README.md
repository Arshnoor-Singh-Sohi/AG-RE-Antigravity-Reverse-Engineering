# AntiGravity Reverse Engineering Documentation Repository

Welcome to the comprehensive documentation repository for the AntiGravity browser automation system. This repository exists because AntiGravity, a sophisticated internal tool developed at Google, lacks public documentation. Through careful reverse engineering work, we have extracted, documented, and explained every tool, architectural pattern, and design decision discoverable within the compiled binary. Whether you are integrating with AntiGravity, studying system design patterns, or simply curious about how modern browser automation works at scale, this repository serves as your complete guide.

## What This Repository Contains

This repository represents months of detective work translating machine code back into human understanding. You will find detailed architectural diagrams showing how AntiGravity is structured from the ground up, complete API documentation for all twenty-seven tools the system exposes, real working code examples demonstrating integration patterns, and educational content explaining not just what AntiGravity does but why it was designed this way. Every diagram can be rendered directly in your browser or IDE, every code example can be adapted to your needs, and every explanation assumes you are learning this material for the first time.

The centerpiece of this repository is the complete architectural reference document. This single comprehensive file contains four interconnected Mermaid diagrams that reveal the system from different perspectives. The first diagram maps out the entire system architecture showing how components are layered and how data flows between them. The second diagram traces a single request from submission through completion, revealing the temporal behavior that static architecture diagrams cannot show. The third diagram documents the state machine that tracks every operation's lifecycle through states like queued, running, waiting, and done. The fourth diagram organizes all twenty-seven tools into a logical hierarchy that mirrors how developers actually think about capabilities when solving problems.

Beyond diagrams, you will find production-quality Python code demonstrating real integration patterns. These are not toy examples but realistic implementations showing how to build a robust client library, how to handle errors gracefully across different failure modes, how to optimize performance by understanding operation costs, and how to implement retry logic that distinguishes between transient failures worth retrying and permanent errors that should fail fast. Every code example includes extensive comments explaining the reasoning behind design decisions, not just what the code does but why it does it that way.

## Who Should Read This Documentation

If you are a software engineer tasked with integrating your systems with AntiGravity, this documentation provides the API contract you need. You will learn exactly what arguments each tool requires, what responses to expect, how to handle the asynchronous nature of the execution engine, and what error conditions you must prepare for. The code examples give you working patterns you can adapt rather than building everything from scratch.

If you are an AI researcher or developer working with autonomous agents, AntiGravity represents a mature example of how to give language models the ability to control computers. The architectural patterns here solve real problems that every agent system eventually faces. How do you validate requests before committing resources? How do you track operations that take seconds or minutes to complete? How do you let operations wait for external conditions without blocking the entire system? How do you organize dozens of capabilities so they are discoverable and intuitive? This documentation answers all these questions with concrete examples.

If you are a computer science student or professional studying system design, AntiGravity demonstrates enterprise patterns worth learning. The layered architecture with clear separation of concerns, the validation-at-the-boundary pattern that fails fast, the state machine approach to lifecycle management, and the namespace organization of capabilities are all patterns you will encounter repeatedly in your career. Understanding them deeply in one system builds transferable knowledge.

If you are simply curious about how browser automation works at Google scale, this documentation lifts the veil on a system processing potentially millions of operations daily. You will see how performance considerations shape design decisions, how error handling becomes more sophisticated in production systems, and how seemingly simple operations like scrolling a webpage require surprising amounts of infrastructure.

## Repository Structure and Navigation Guide

The repository is organized to support both linear learning and random access reference lookup. You can read straight through to build comprehensive understanding, or jump directly to the section solving your immediate problem.

The main architectural reference document lives at the root level in a file called `antigravity-architecture-complete.md`. This document contains everything in one place specifically so you can search it, reference it offline, and avoid jumping between multiple files. However, its comprehensive nature means it runs over fifteen hundred lines. Do not let this intimidate you. The document is structured with clear section headers, a detailed table of contents, and progressive complexity. Start at the introduction even if you consider yourself an expert, because it establishes terminology and mental models that the rest of the document builds upon.

The original reverse engineering notes that started this project appear in `antigravity-tools.md`. This document shows the raw detective work, including the exact commands used to extract strings from the binary, the patterns searched for, and the evidence found for each tool. Reading this file gives you insight into the reverse engineering methodology itself. You will see how examining strings in compiled binaries can reveal API structures, how Go's type system leaves distinctive patterns in binaries, and how patient systematic work uncovers hidden documentation. If you are interested in reverse engineering as a skill, start here.

Supporting code examples are collected in the `examples` directory. Rather than embedding all code in the main document, we have extracted complete working examples into individual Python files. The `client.py` file implements a full-featured client library with validation, polling, error handling, and retry logic. The `web_scraping.py` file demonstrates browser automation for data extraction. The `file_analysis.py` file shows filesystem and search tool usage. The `testing_framework.py` file illustrates complex multi-tool workflows. Each file is documented to be both educational and immediately usable in your projects.

The `diagrams` directory contains individual Mermaid files for each architectural diagram. If you want to modify a diagram, embed it in your own documentation, or render it with different styling, grab the source files from here. The files include rendering hints and suggested color schemes in comments.

## How to View and Use the Diagrams

The Mermaid diagrams in this repository will render automatically if you view the Markdown files on GitHub, GitLab, or Bitbucket. Simply open any file containing Mermaid code blocks and the platform will render them as visual diagrams. This requires no setup on your part.

If you prefer working locally, Visual Studio Code provides excellent Mermaid support through extensions. Install the Mermaid Preview extension from the marketplace, then open any Markdown file and use the preview pane. You will see diagrams rendered in real time as you edit. This workflow is ideal if you want to customize diagrams for your specific needs or create variations exploring different architectural decisions.

For presentations or printed documentation, you can export diagrams to images using several tools. The Mermaid CLI tool lets you render diagrams to PNG or SVG from the command line. Web-based editors like the official Mermaid Live Editor allow you to paste diagram code, customize styling, and export high-resolution images. If you are building slides about AntiGravity or similar systems, exported diagram images integrate cleanly into PowerPoint, Keynote, or LaTeX presentations.

Understanding what each diagram teaches you helps you use them effectively. The system architecture diagram answers the question of what components exist and how they connect. Use this diagram when explaining AntiGravity to someone unfamiliar with it, when designing integrations that need to understand the full system, or when debugging issues and trying to identify which layer the problem originates in. The sequence diagram answers the question of what happens when you call a tool. Use this diagram when designing client libraries, implementing retry logic, or explaining asynchronous operation to stakeholders. The state machine diagram answers the question of what states operations can be in. Use this diagram when implementing status polling, designing monitoring dashboards, or handling operation cancellation. The mind map answers the question of how capabilities are organized. Use this diagram when helping users discover which tool solves their problem or when designing abstractions that group related capabilities.

## Prerequisites and Background Knowledge

This documentation aims to be accessible to anyone with programming experience, but certain background knowledge will help you get more value from the material. You should be comfortable reading Python code even if it is not your primary language. The code examples use Python because it is widely readable and commonly used in automation, but the patterns translate to any language. If you struggle with the Python syntax, focus on the comments and prose explanations which capture the important concepts language-independently.

Understanding HTTP and REST APIs will help you grasp how the API layer works. AntiGravity uses JSON for request and response formatting following patterns common in modern web services. If you have ever called a web API from code, you already have the mental model needed. If not, think of it like filling out a web form and submitting it, then receiving a response page with your results.

Familiarity with asynchronous programming concepts helps when reasoning about the state machine and operation lifecycle. Operations do not complete instantly, so you need to understand patterns for submitting work and later checking results. If you have used promises in JavaScript, async/await in Python, or futures in Java, you understand the core idea. If not, think of it like dropping off your laundry at a dry cleaner and getting a ticket. You cannot wait there for hours while they clean your clothes, so you leave and come back later with your ticket to check if your order is ready.

Basic understanding of web browsers and the Document Object Model helps when reading about browser automation tools. You do not need to know how to build a browser, but understanding that web pages are structured as trees of elements and that browsers expose APIs for controlling them will make the browser tools more intuitive. If you have ever used browser developer tools to inspect a webpage, you have seen the DOM.

Knowledge of file systems and operating system concepts helps with the file operation tools. Understanding what absolute paths are, how directory hierarchies work, and what file permissions mean will make those tools immediately clear. Most programmers have this knowledge already from working with files in any programming environment.

No background in reverse engineering is required to use this documentation, but if you are curious about the methodology, the original reverse engineering notes explain the process step by step. You will learn how binary analysis works, what strings are and why they persist in compiled code, how to search for patterns that reveal structure, and how to infer API contracts from type information embedded in binaries.

## Getting Started with Integration

If you came here to actually integrate with AntiGravity, let me walk you through the path from complete beginner to working integration. Start by reading the introduction section of the main architectural document. This orients you to what AntiGravity is, what problems it solves, and how it is structured at the highest level. Spend fifteen minutes here building your mental model. Do not skip this even if you are experienced because it establishes terminology used throughout the documentation.

Next, study the system architecture diagram carefully. Trace the flow from external world through API layer, validation layer, Cortex engine, tool implementations, and execution layer. Notice how this layered structure means your code only talks to the API layer and never directly to lower layers. This simplifies your integration because you only need to understand the API contract, not the internal implementation.

Now read the request flow sequence diagram section. This shows what actually happens when you submit a request. Pay attention to the state transitions and the branching for success versus error cases. Understanding this flow is critical for implementing reliable integrations that handle all scenarios gracefully.

With this conceptual foundation, turn to the code examples. Start with `client.py` which implements a reusable client library. Read through the implementation carefully, paying attention to the error handling patterns, the polling logic, and how validation is layered. You can use this client library as-is in your projects or use it as a reference when implementing clients in other languages.

Pick one of the example workflows that resembles your use case. If you need browser automation, study `web_scraping.py`. If you need file system operations, study `file_analysis.py`. If you need complex multi-step workflows, study `testing_framework.py`. Run these examples in a local environment (obviously with mock AntiGravity API since you likely do not have access to the real one) to see how they behave. Modify them incrementally to match your specific requirements.

Finally, consult the tool documentation in the original reverse engineering notes to understand exactly what arguments each tool accepts and what responses it returns. The JSON Schema specifications are precise contracts you must honor. Build validation into your code that checks arguments locally before submitting requests, as this catches errors immediately rather than after network round trips.

## Understanding the Teaching Approach

This documentation takes a deliberately educational approach rather than serving purely as reference material. You will notice extensive prose explanations that break down complex concepts into digestible pieces, real-world analogies that connect abstract technical ideas to concrete experiences, progressive complexity where simple concepts are explained before building on them, explicit learning checkpoints where we pause to ensure understanding before proceeding, and anticipation of confusion where we address likely misunderstandings before they arise.

This teaching approach means the documentation is longer than minimal reference documentation would be, but the investment in reading pays off in deeper understanding. When you finish reading, you will not just know how to call AntiGravity tools but will understand why they are designed this way, what problems the architecture solves, where the system might fail and how to handle it, and how the patterns here apply to other systems you will encounter.

The teaching approach also means we respect your intelligence while meeting you where you are. We do not assume you know everything, but neither do we talk down to you. We explain concepts thoroughly when first introducing them, then use those concepts freely afterward trusting you have internalized them. If you ever feel lost, return to earlier sections and rebuild your understanding. The layered structure means every concept builds on previous ones, so gaps in early understanding compound into confusion later.

## Contributing to This Repository

This repository represents community knowledge about a proprietary system. As such, contributions are both welcome and valuable. If you discover additional tools through your own reverse engineering work, please document them following the established format. Include the evidence from the binary, your inferred API contract, example usage, and any lessons learned about behavior or edge cases.

If you find errors in existing documentation, submit corrections with supporting evidence. Given that this knowledge comes from reverse engineering rather than official documentation, mistakes are possible. Corrections help everyone, but we need evidence rather than speculation. Show the binary strings you found, the behavior you observed, or the official documentation you discovered.

If you build integration examples in languages other than Python, contributions in JavaScript, Go, Java, Rust, or other languages help developers working in those ecosystems. Follow the same patterns of extensive comments, error handling, and educational value that the Python examples demonstrate.

If you create tooling around this documentation like better diagram renderers, API testing frameworks, or mock servers for development, please share them. These meta-tools help the community make better use of the core documentation.

If you work at Google and can validate or correct information here, please do so carefully and in compliance with your employer's policies regarding proprietary information. We want accuracy but never at the cost of violating confidentiality obligations.

When contributing, maintain the educational tone and comprehensive approach that characterizes existing documentation. Brief reference-style contributions are useful but work best when integrated into the larger teaching framework. Think about the reader who knows nothing and is trying to learn, not just the expert who needs a quick reference.

## Legal and Ethical Considerations

This repository documents a proprietary system through reverse engineering. Such work raises important questions about intellectual property, trade secrets, and responsible disclosure. Let me address these directly.

Reverse engineering for interoperability purposes is generally protected under law in most jurisdictions. When you need to integrate with a system and documentation is unavailable, examining the system to understand its interface falls under fair use and interoperability exceptions to copyright and trade secret law. This repository focuses exclusively on API documentation for integration purposes, not on reproducing the implementation or competing with the system.

However, if you work at Google or have contractual obligations regarding proprietary information, nothing in this repository should be construed as encouraging violation of those obligations. Do not contribute information obtained through privileged access or in violation of non-disclosure agreements. The reverse engineering work documented here used only publicly observable behavior and publicly accessible binaries, without insider access or leaked documents.

If you use this documentation to integrate with AntiGravity in production systems, ensure your use complies with whatever terms of service or usage policies govern access to AntiGravity. This documentation tells you how the system works technically but cannot advise on whether your particular use is authorized. That determination depends on your relationship with Google and any agreements governing your access.

The educational portions of this documentation discussing system design patterns, architectural principles, and engineering best practices carry no intellectual property concerns. These are general knowledge in the software engineering field. Learning from well-designed systems and applying those lessons to your own work is exactly how the field advances.

## What This Documentation Cannot Tell You

Despite the comprehensiveness of this reverse engineering effort, significant gaps remain in our understanding. The internal implementation of tools remains opaque. We know what arguments tools accept and what responses they return, but we cannot see the actual code executing those operations. This means we cannot predict exact performance characteristics, understand all edge cases, or anticipate every possible failure mode.

The authentication and authorization mechanisms are not documented here. We do not know how AntiGravity determines whether a caller is permitted to execute operations. We do not know what rate limits exist or how they are enforced. We do not know what audit logging occurs or who can access those logs. These operational concerns matter greatly in production but are invisible to reverse engineering of the binary.

The deployment architecture is unknown. We do not know whether AntiGravity runs as a single service or is distributed across multiple systems. We do not know how it achieves reliability and fault tolerance. We do not know what dependencies it has on other Google infrastructure. This means we cannot reason about availability, scalability, or disaster recovery.

The development roadmap and future plans are completely invisible. This documentation describes AntiGravity as it exists in the binary we analyzed, but Google engineers are presumably continuing to develop it. New tools may be added, existing tools may change, and architectural improvements may alter behavior. This documentation is a snapshot in time, not a living contract.

The official stance and support policy from Google is unclear. We do not know whether Google considers external use of AntiGravity acceptable, discouraged, or prohibited. We do not know whether support channels exist or whether users are expected to be self-sufficient. Anyone using this documentation should understand they are working without official support.

## The Value of Unofficial Documentation

You might wonder why you should trust documentation created through reverse engineering rather than waiting for official documentation. Let me explain the value proposition honestly.

Official documentation, when it exists, is authoritative and maintained by the people who built the system. It reflects their intended design and includes details like support policies and roadmaps that reverse engineering cannot discover. However, official documentation does not exist for many internal tools, specialized systems, or legacy codebases. When you need to integrate with such systems, reverse engineering may be your only option.

Reverse engineering produces documentation grounded in observable behavior rather than stated intentions. Sometimes these differ. The actual system may have quirks, undocumented features, or behaviors that diverge from original design documents. Reverse engineered documentation captures what the system actually does, not what someone once planned for it to do.

Community-created documentation often includes hard-won lessons from people who struggled to make integrations work. Official documentation may gloss over edge cases, assume knowledge the reader lacks, or omit details obvious to the system's creators but not to external users. Community documentation tends to be more tutorial in nature because the authors remember not knowing and want to help others avoid the same struggles.

The educational value of documentation like this extends beyond the specific system. By studying one well-designed system deeply, you learn patterns and principles applicable to many systems. Even if you never use AntiGravity directly, understanding how it is architected makes you a better systems thinker.

That said, if official documentation becomes available, you should prefer it for authoritative information while potentially using community documentation for its educational value and battle-tested integration patterns.

## Acknowledgments and Related Work

This documentation builds on the work of multiple researchers who independently reverse engineered aspects of AntiGravity. The blog post by Alok Bishoyi on reverse engineering browser automation provided methodological insights and validated our approach. Online discussions in various technical communities surfaced fragments of knowledge that helped complete the picture. While individuals contributed pieces, this repository represents the first comprehensive synthesis of that distributed knowledge into a single coherent resource.

The Mermaid diagramming language created by Knut Sveidqvist and maintained by a large open source community enables the visual explanations central to this documentation. Without Mermaid's ability to express complex diagrams as simple text, this repository would be far less accessible.

The broader reverse engineering community deserves recognition for developing the tools and techniques that make work like this possible. String extraction utilities, binary analysis frameworks, pattern matching tools, and decompilation research all contribute to our ability to understand proprietary systems.

## Moving Forward

This repository is a living resource that will evolve as we learn more about AntiGravity, as the system itself changes, and as the community contributes improvements. Star the repository to receive notifications of significant updates. Fork it to maintain your own version with custom additions or adaptations. Submit issues when you find problems or have questions. Submit pull requests when you have improvements to share.

Whether you use this documentation to build integrations, to study system design, or simply to satisfy curiosity about how sophisticated automation systems work, we hope you find it valuable. The investment in thorough documentation pays dividends not just in immediate utility but in the deeper understanding that comes from studying well-designed systems.

Now turn to the main architectural document and begin your journey into understanding AntiGravity. Take your time, follow the progression, and build your understanding piece by piece. The system is complex but approachable when taken systematically. You will emerge with knowledge that serves you far beyond this specific system.

---
