# Complete Beginner's Guide to Understanding the AntiGravity Reverse Engineering Document

## 1. High-Level Orientation — What Are You Looking At?

Imagine you just walked into a locked workshop and found a complex machine with no instruction manual. You cannot ask the person who built it what it does, so instead you carefully take apart the machine piece by piece, examining each component, and writing down what you discover. That is exactly what this markdown file represents.

This file is a **research report** written by someone who performed what programmers call "reverse engineering." The person who wrote this document did not have access to the official documentation for a software program called "AntiGravity browser," so they used detective work to figure out how the program works by examining the program's compiled code.

### Who Would Write This File?

A security researcher, a curious software engineer, or someone who needs to understand how a tool works when the creators have not shared public documentation. In this case, someone wanted to understand what capabilities the AntiGravity browser has, so they examined the actual program files.

### Who Would Read This File?

Developers who want to integrate with AntiGravity, researchers studying how browser automation works, or people building similar tools who want to learn from this system's design. You might also read this if you are trying to use AntiGravity and need to know what operations it can perform.

### Where Does This Fit in Real Life?

AntiGravity appears to be a sophisticated browser automation tool, probably used internally at Google (the file paths show "google3" which is Google's internal code repository structure). Imagine a robot that can control a web browser just like a human would—clicking buttons, filling forms, scrolling pages, reading content. This document catalogs all the different actions that robot can perform.

---

## 2. Concept Prerequisites — Building Blocks of Understanding

Before we dive into the file itself, let me teach you the foundational concepts you need. Think of this as learning the alphabet before reading a book.

### Concept: Reverse Engineering

**What it is:** Taking apart a finished product to understand how it was built, without having access to the original blueprints or instructions.

**Why it exists:** Sometimes you need to understand how something works but the creator did not share documentation. Maybe the company wants to keep it secret, maybe the documentation was lost, or maybe you are studying a competitor's product.

**Real-world analogy:** Imagine buying a delicious cookie and trying to figure out the recipe by tasting it, examining the texture, and identifying each ingredient. You are working backward from the finished product.

**Where it appears in this file:** The entire document is the result of reverse engineering. The author examined the compiled AntiGravity program and extracted information about its tools and capabilities.

---

### Concept: Binary/Compiled Code

**What it is:** When programmers write software, they write in human-readable programming languages. Before the computer can run that software, it gets translated into binary code (ones and zeros) that computers understand. This translation process is called "compilation," and the result is a "binary" or "compiled" file.

**Why it exists:** Computers cannot directly understand human programming languages like Python or Go. They need instructions in binary format.

**Real-world analogy:** Think of it like translating a book from English into an ancient language that only a specific machine can read. The meaning is the same, but the format is completely different.

**Where it appears in this file:** The author mentions examining "the binary" and extracting "strings" from it. They are looking at the compiled, machine-readable version of AntiGravity to discover information.

---

### Concept: API (Application Programming Interface)

**What it is:** A defined way for one program to communicate with another program. It is like a menu at a restaurant—it tells you what you can order and how to order it.

**Why it exists:** Programs need standardized ways to interact. If every program communicated differently, software would be chaos. APIs provide rules and structure.

**Real-world analogy:** When you go to a restaurant, you do not walk into the kitchen and start cooking. You look at a menu (the API), which shows you what dishes are available and how to request them. The kitchen does not need to know who you are—just what you want from the menu.

**Where it appears in this file:** The tools documented here represent API functions that AntiGravity exposes. Each tool is like one item on the menu—something you can request the program to do.

---

### Concept: JSON and JSON Schema

**What it is:** JSON (JavaScript Object Notation) is a format for organizing data in a structured, human-readable way. JSON Schema is a blueprint that describes what a valid JSON document should look like.

**Why it exists:** When programs exchange data, they need a common format everyone understands. JSON became popular because it is simple and works across different programming languages. JSON Schema adds rules—like saying "this field must be a number" or "this field is required."

**Real-world analogy:** Think of JSON as a form you fill out, with labeled boxes for different pieces of information. JSON Schema is like the instructions at the top of the form that say which boxes are required and what type of information goes in each box.

**Where it appears in this file:** Each tool has a JSON Schema that defines what information (called "arguments" or "parameters") you must provide when using that tool.

---

### Concept: Tool/Function/Method

**What it is:** A defined action a program can perform. Like a single button on a remote control.

**Why it exists:** Programs need discrete, reusable capabilities. Instead of writing the same code over and over, programmers create tools (also called functions or methods) that can be called whenever needed.

**Real-world analogy:** Think of your TV remote. It has distinct buttons—volume up, channel down, power on. Each button is a "tool" that performs one specific action. You press the button (call the tool), and the TV responds predictably.

**Where it appears in this file:** The document lists twenty-seven different tools that AntiGravity provides, like "browser.scroll_down," "search.in_file," and "terminal.read." Each tool is a distinct capability.

---

### Concept: Arguments/Parameters

**What it is:** Additional information you provide when calling a tool to specify exactly what you want it to do.

**Why it exists:** Tools need context. The "open file" tool needs to know *which* file to open. The "scroll down" tool needs to know *which page* to scroll.

**Real-world analogy:** When you tell someone to "drive to the store," you need to specify *which* store. The store's address is an argument—extra information that makes the instruction complete and actionable.

**Where it appears in this file:** Every tool in the document has a table showing its arguments. For example, the "browser.open_url" tool requires a "Url" argument (which website to open) and has an optional "PageIdToReplace" argument (whether to replace an existing tab).

---

### Concept: Struct (Data Structure)

**What it is:** In programming, a struct (short for "structure") is like a template or container that holds related pieces of information together. It defines what fields exist and what type of data each field holds.

**Why it exists:** Programs need organized ways to group related data. Instead of having ten separate variables floating around, you bundle them into one logical package.

**Real-world analogy:** Think of a medical form at a doctor's office. The form has labeled sections: Name, Date of Birth, Address, Phone Number. A struct is like that form template—it defines what information belongs together and what type of information goes in each section.

**Where it appears in this file:** The author found struct definitions in the binary code. Each struct corresponds to one tool and defines what arguments that tool accepts. For example, they found a struct for the "drag" tool that has fields for "PageID" and "Waypoints."

---

### Concept: Browser Automation

**What it is:** Technology that allows a program to control a web browser automatically—clicking, typing, scrolling, reading content—just like a human would, but much faster.

**Why it exists:** Many workflows involve repetitive browser tasks (like testing a website, scraping data, or filling forms). Automating these tasks saves time and ensures consistency.

**Real-world analogy:** Imagine having a robot assistant that can use your computer's web browser exactly like you do. You give it instructions like "Go to this website, click the 'Search' button, and tell me what results appear." Browser automation tools make this possible.

**Where it appears in this file:** Many of the documented tools control browser behavior—scrolling pages, clicking elements, capturing screenshots, reading page content. AntiGravity is fundamentally a browser automation system.

---

### Concept: DOM (Document Object Model)

**What it is:** When a web browser loads a webpage, it creates a structured representation of that page's content. This representation is called the DOM. It organizes the page into a tree of elements—headers, paragraphs, buttons, images—that programs can interact with.

**Why it exists:** Web pages are written in HTML, which is just text. Browsers need to convert that text into a structured format so they can display the page and allow programs to manipulate it (like changing text color or hiding elements).

**Real-world analogy:** Think of a play script versus an actual theatrical performance. The script (HTML) is just words on paper. The DOM is like the staged performance—actors in position, props arranged, lighting set. Once the performance is running, you can interact with it (dim the lights, move a prop). The DOM makes the webpage "alive" and interactive.

**Where it appears in this file:** Several browser tools reference the DOM—like "browser.get_dom_tree" (which retrieves the structure of a webpage) and "ElementIndex" arguments (which identify specific elements on the page).

---

### Concept: File System Operations

**What it is:** Actions that programs perform on files and folders stored on a computer—creating files, reading their contents, deleting them, moving them, searching for them.

**Why it exists:** All programs need to store and retrieve data. The operating system provides file system operations as a fundamental capability.

**Real-world analogy:** Think of your computer's file system as a physical filing cabinet. File system operations are like opening drawers, reading documents, adding new folders, or throwing away old papers.

**Where it appears in this file:** Many tools perform file operations—"fs.view_file_range" (read part of a file), "fs.list_directory" (see what files are in a folder), "fs.delete_knowledge_path" (remove a file). The "fs" prefix likely stands for "file system."

---

### Concept: Terminal/Command Line

**What it is:** A text-based interface for controlling a computer. Instead of clicking icons and buttons, you type commands as text. The terminal executes those commands and shows text output.

**Why it exists:** Graphical interfaces are convenient for everyday tasks, but text-based interfaces are powerful for automation and technical work. They provide precise control and can be scripted.

**Real-world analogy:** Think of the difference between ordering food by pointing at pictures on a menu versus verbally describing exactly what you want to a chef. The terminal is like speaking directly to the computer in its native language.

**Where it appears in this file:** The "terminal.read" tool allows AntiGravity to read output from terminal sessions. This means the automation system can run command-line programs and see their results.

---

### Concept: Strings in Binary Code

**What it is:** When you examine a compiled program, most of it is unreadable binary data. However, text strings (like error messages, tool names, or descriptions) are often stored in a readable format inside the binary. Examining these strings can reveal information about the program.

**Why it exists:** Programs need to store text for error messages, debug output, documentation, and other purposes. This text remains human-readable even after compilation.

**Real-world analogy:** Imagine finding an ancient machine with no manual. However, some labels and warning messages are engraved on the machine in English. Those labels give you clues about what the machine does. Binary strings are like those labels.

**Where it appears in this file:** The author extracted strings from the AntiGravity binary using the "strings" command. Then they searched for patterns in those strings to find tool definitions. The "ParseToolArgs" patterns they searched for are like signatures that identify where tools are defined.

---

Now that you understand the foundational concepts, let's walk through the file itself, section by section.

---

## 3. Line-by-Line Deep Explanation of the Document

### Title and Introduction Section

The document begins with a title: **"Reverse Engineering AntiGravity browser (Step by Step)"**

This immediately tells us the document's purpose. The author is explaining their detective work figuring out how AntiGravity browser works. The phrase "Step by Step" promises that we will learn their methodology, not just their findings.

The subtitle reads: **"AntiGraviti tools parsed based on reverse engineering and ParseToolArgs structs found in the binary."**

Let me unpack this sentence word by word. "AntiGraviti tools" refers to the individual capabilities or functions that AntiGravity provides. "Parsed" means examined and extracted—the author looked through data and pulled out meaningful information. "ParseToolArgs structs" is a specific pattern the author searched for. In the Go programming language (which AntiGravity appears to be written in), "ParseToolArgs" is likely a function that processes tool arguments. By finding these patterns, the author could identify tool definitions. "Found in the binary" confirms they examined the compiled program file.

The next line explains: **"We need strings, the juice is in the DATA block and not in the code."**

This is insider knowledge about reverse engineering. Compiled programs have different sections. The CODE section contains the actual machine instructions (very hard to understand). The DATA section contains constants like text strings and numbers (much easier to read). The author is saying the useful information is in the DATA section, particularly the text strings.

---

### Commands Section

The author shows the exact commands they ran on their Mac computer. Let's break down each command:

**Command 1:** `strings /Applications/Antigravity.app/Contents/Resources/app/extensions/antigravity/bin/language_server_macos_arm > ~/Downloads/language_server_macos_arm_strings.txt`

This command does three things. First, `strings` is a Unix utility that extracts readable text from binary files. Second, the long path `/Applications/Antigravity.app/...` points to where AntiGravity is installed on a Mac. The file name "language_server_macos_arm" suggests this is a server component that handles language operations, compiled for Mac computers with ARM processors (like newer MacBooks with Apple Silicon). Third, the `>` symbol redirects the output into a text file in the Downloads folder. This creates a searchable text file containing all strings found in the binary.

**Why save to a file?** Searching through text in a file is much faster than running the extraction command repeatedly.

**Command 2:** `grep -i "google3/third_party/jetski/cortex/utils/utils.ParseToolArgs\[" ~/Downloads/language_server_macos_arm_strings.txt`

Here the author uses `grep`, a search tool. The `-i` flag means "ignore case" (match uppercase or lowercase). They are searching for a very specific pattern: `"google3/third_party/jetski/cortex/utils/utils.ParseToolArgs["`. 

Let me explain what this pattern means. "google3" is Google's internal code repository path. Everything at Google is organized under "google3." The path "third_party/jetski/cortex" suggests this is code from a project called "Cortex," possibly part of a larger "Jetski" initiative. The "utils" indicates utility functions. "ParseToolArgs[" is the function name followed by an opening bracket, which in Go precedes the type parameter.

**Why search for this pattern?** In Go, when you call a generic function like `ParseToolArgs`, the full function signature including type information gets embedded as a string in the binary. By finding these patterns, the author can identify every tool definition because each tool's arguments are processed through `ParseToolArgs`.

**Command 3:** `grep --line-buffered "ParseToolArgs\[go.shape.struct {.*}" ~/Downloads/language_server_macos_arm_strings.txt`

This is a variation of the previous search using a regular expression pattern. The `{.*}` means "followed by an opening brace and any characters." This catches the struct definition that follows ParseToolArgs.

**Command 4:** `grep -i "^CORTEX_STEP_.*$" ~/Downloads/language_server_macos_arm_strings.txt | uniq`

This searches for lines that start with "CORTEX_STEP_" and end immediately after (the `^` means start of line, `$` means end of line, `.*` means any characters in between). The `| uniq` part removes duplicate lines. The author is finding state machine states—different stages the Cortex system can be in during operation.

**Reference Link:** The author mentions finding another researcher's blog post about reverse engineering browser automation. This validates their approach—other people have done similar detective work.

---

### The Findings Section

Now we reach the main content. The document lists twenty-seven different tools, each with consistent formatting. Let me walk through the first tool in extreme detail, then we can move faster through subsequent tools.

---

### Tool #1: Browser Drag Tool (Complete Walkthrough)

**Evidence Section:**

The author shows the raw text they extracted from the binary:

```
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageID string "jsonschema_description:\"page_id of the Browser page to perform the drag operation on\"";
    Waypoints []google3/third_party/jetski/cortex/tools/tools.waypoint "jsonschema_description:\"A series of pixel coordinates defining the drag path. When this tool call is executed, the first waypoint will be clicked, then the mouse will be dragged to each subsequent waypoint in the provided order, and finally the mouse will be released at the last waypoint.\""
  }
]
```

This is a Go struct definition embedded in the binary. Let me decode every piece:

- `go.shape.struct` means this is a structure definition in Go
- `PageID string` means there is a field called PageID that holds text
- The part in quotes after `string` is metadata describing what PageID means: which browser page to perform the drag on
- `Waypoints []google3/third_party/jetski/cortex/tools/tools.waypoint` means Waypoints is an array (the `[]` brackets indicate "list of") of waypoint objects
- The description explains the drag behavior: click the first point, drag through all points, release at the last point

**Real-world analogy for this tool:** Imagine you need to draw a signature on a touchscreen. You touch your finger down at the start of the signature (first waypoint), drag it along the path of your signature (middle waypoints), and lift your finger at the end (last waypoint). This tool automates that dragging motion in a web browser.

**Logical Tool Name:**

The author infers the tool name should be `browser.drag`. The "browser." prefix is a namespace indicating this tool operates on browser pages. The "drag" describes the action. This follows a common naming pattern in APIs where related tools share a prefix.

**Purpose:**

The author explains what the tool does in plain English. This tool performs drag gestures—like dragging a slider, reordering list items, or drawing on a canvas. The word "gesture" means a mouse movement pattern.

**Arguments Table:**

The author creates a table documenting each field:

| Field | Type | Required? | Description |
|-------|------|-----------|-------------|
| PageID | string | not marked | Page ID of the browser page to perform the drag operation on. |
| Waypoints | array of waypoint | not marked | Series of pixel coordinates defining the drag path... |

The "Type" column tells us what kind of data each field expects. "Required?" notes whether the tool will fail without this field. The struct tags did not explicitly mark these as required, but logically you need both—you must specify which page and what path to drag.

**JSON Schema:**

Finally, the author provides a formal JSON Schema. This is a machine-readable specification of the tool's interface. The schema could be used by other programs to validate requests before sending them to AntiGravity. Each property gets a description, and the schema specifies this is an object type.

---

### Understanding the Pattern for All Tools

Now that we have analyzed one tool completely, you can see the pattern repeated for all twenty-seven tools:

1. **Evidence:** The raw text from the binary
2. **Logical tool name:** The inferred API name
3. **Purpose:** What the tool does in plain language
4. **Arguments:** What information you provide when calling the tool
5. **JSON Schema:** Formal specification

This consistent structure makes the document systematic and easy to reference. Each tool is self-contained—you can understand it without reading other sections.

---

### Quick Overview of Other Interesting Tools

Let me highlight a few particularly interesting tools and explain what makes them significant:

**Terminal Read (Tool #2):**

This tool reads output from command-line sessions. Imagine you run a Python script in a terminal window. This tool lets AntiGravity see what that script printed. This is powerful because it bridges browser automation with traditional programming environments.

**Web Search (Tool #4):**

This tool performs web searches, optionally prioritizing specific domains or choosing between web and image search. The fact that AntiGravity has built-in search integration suggests it is designed for tasks that require gathering information from the internet, not just interacting with predetermined pages.

**Document Chunk Viewer (Tool #5):**

This tool views specific chunks of documents. Many systems break large documents into smaller chunks for processing. Being able to retrieve a specific chunk by position suggests AntiGravity works with large documents that do not fit in memory all at once.

**File View with Line Range (Tool #8):**

This tool reads files but lets you specify exactly which lines to retrieve. This is tremendously useful when working with large files. Instead of loading a million-line log file into memory, you can read lines one thousand through two thousand. The fact that lines are "1-indexed" means the first line is line one, not line zero. This matches human intuition but differs from many programming languages that start counting at zero.

**Browser Scroll Tools (Tools #17-18):**

AntiGravity has separate tools for scrolling up versus scrolling down. Each can scroll to the absolute top or bottom, or scroll relative to a specific element on the page. The "ElementIndex" argument refers to elements in the DOM tree—each interactive element gets a number so programs can reference it precisely.

**Memory Get Tool (Tool #27):**

This fascinating tool retrieves memories by ID. The "Reason" field requires explaining why you are accessing these memories. This suggests AntiGravity has some kind of logging or auditing system tracking tool usage and reasoning. This could be for debugging, security, or understanding how the system makes decisions.

---

### Cortex State Machine Section

At the end of the document, the author lists states the Cortex system can be in. A state machine is a system that can exist in one state at a time and transitions between states based on events.

The states fall into categories:

**Sources (where actions come from):**
- UNSPECIFIED: Unknown or not specified
- MODEL: The AI model itself initiated the action
- SYSTEM: The system infrastructure initiated the action

**Statuses (what is happening now):**
- GENERATING: Creating new content or planning
- QUEUED: Waiting in line to run
- RUNNING: Currently executing
- DONE: Completed successfully
- ERROR: Failed with an error
- INTERRUPTED: Stopped before completion
- HALTED: Deliberately stopped

**Types (what kind of action):**
- CODE_ACTION: Modifying code
- GIT_COMMIT: Saving changes to version control
- SEARCH_WEB: Querying the internet
- BROWSER_INPUT: Interacting with a web page
- NOTIFY_USER: Displaying a message to the user

These states give insight into AntiGravity's architecture. It is not just executing commands—it has a sophisticated workflow engine that tracks what stage each operation is in. This allows for pausing, resuming, error recovery, and parallel execution.

---

## 4. Teaching Artifacts

Let me now create multiple teaching artifacts to cement your understanding.

---

### Artifact A: Complete Glossary

**AntiGravity:** A browser automation tool developed internally at Google that allows programmatic control of web browsers and file systems.

**API (Application Programming Interface):** A set of defined tools and rules that allow programs to interact with each other.

**Argument/Parameter:** Extra information provided to a tool to specify what action to take (like telling a "delete file" tool which file to delete).

**Binary:** The compiled, machine-readable form of a program that computers can execute directly.

**Browser Automation:** Technology that allows software to control a web browser programmatically—clicking, scrolling, typing—as if a human were using it.

**Chunk:** A portion of a larger document or file, used when data is too large to process all at once.

**Compilation:** The process of translating human-readable code into machine-executable binary format.

**Cortex:** The internal system name for the execution engine that powers AntiGravity.

**DOM (Document Object Model):** The structured representation of a webpage that browsers create, organizing content into a tree of interactive elements.

**File System:** The organizational structure computers use to store and retrieve files and folders.

**Go (Programming Language):** A modern programming language developed by Google, known for simplicity and performance.

**Grep:** A Unix command-line tool for searching text using patterns.

**JSON (JavaScript Object Notation):** A lightweight format for structuring data in a readable way using key-value pairs.

**JSON Schema:** A specification that defines what valid JSON documents should look like—what fields are required, what types they should be.

**Namespace:** A prefix used in naming to group related items together (like "browser." for all browser-related tools).

**PageID:** An identifier that uniquely references a specific browser tab or page.

**ParseToolArgs:** A function in AntiGravity's code that processes tool arguments, found repeatedly in the binary during reverse engineering.

**Reverse Engineering:** Taking apart a finished product to understand how it works without access to original documentation or source code.

**State Machine:** A system that can exist in one of several defined states and transitions between states based on events.

**String (in binary context):** Human-readable text found inside compiled program files.

**Struct (Structure):** A data format that groups related information together with defined fields and types.

**Terminal:** A text-based interface for controlling a computer by typing commands.

**Tool:** A discrete capability or function that a program provides, callable through its API.

**Waypoint:** A coordinate point along a path, used in the drag tool to define where the mouse should move.

---

### Artifact B: System Architecture Diagram

Let me describe the architecture in prose since we are working with text:

AntiGravity operates as a layered system. At the foundation sits the Cortex execution engine, which manages the state machine and coordinates all operations. This engine maintains awareness of what each operation is doing—whether it is queued, running, completed, or failed.

Above Cortex is the tool layer, divided into several categories. Browser tools handle web page interaction—opening URLs, scrolling, clicking, dragging, reading content, capturing screenshots. File system tools manage reading, writing, searching, and deleting files and directories. Search tools provide capabilities ranging from simple workspace searches to complex queries across multiple directories. Terminal tools bridge to command-line environments, reading output from shell sessions. Memory tools store and retrieve information across sessions. Web tools fetch content from arbitrary URLs.

Each tool accepts arguments that specify exactly what to do. These arguments flow through the ParseToolArgs processor, which validates them against defined schemas. If arguments are valid, Cortex schedules the operation, executing it when resources are available.

Results flow back up through Cortex to whatever called the tool—likely an AI model making decisions about what to do next. The model can examine results, decide the next action, and call additional tools. This creates a loop: observe, decide, act, observe.

---

### Artifact C: Simplified Plain-English Rewrite

**What This Document Is:**

Someone wanted to know how a Google program called AntiGravity works. The company did not share documentation publicly, so this person examined the actual program file and figured out what it can do by reading clues hidden in the compiled code.

**How They Did It:**

Programs contain readable text strings even after compilation. The researcher extracted all text strings from AntiGravity and searched for patterns that reveal tool definitions. Each tool is like a single button on a remote control—it does one thing. They found twenty-seven tools.

**What AntiGravity Does:**

AntiGravity is a system that controls web browsers automatically. Imagine a robot that can use a computer just like you do—visiting websites, clicking buttons, filling out forms, reading information. AntiGravity does exactly that. It also works with files on your computer and can run command-line programs.

**Why This Matters:**

If you are building software that needs to interact with AntiGravity, this document tells you exactly what commands you can give it. Each tool has a name (like "browser.scroll_down") and requires specific information (like which page to scroll). The document lists all twenty-seven tools with complete details about how to use each one.

**The State Machine Part:**

AntiGravity tracks what each operation is doing. Is it waiting to start? Currently running? Finished successfully? Failed with an error? These different stages are called states. The document lists all possible states, giving insight into how AntiGravity manages complex workflows.

---

### Artifact D: Step-by-Step Flow of Using AntiGravity

**Step 1: Understanding What You Want to Accomplish**

Before using any tool, you must clearly define your goal. Do you want to visit a webpage? Search for files? Read terminal output? Each goal maps to one or more tools.

**Step 2: Choosing the Right Tool**

Look through the twenty-seven available tools and select the one matching your goal. For example, if you want to open a webpage, you would choose "browser.open_url." If you want to search files in a directory, you would choose "search.in_directories."

**Step 3: Preparing Arguments**

Every tool needs specific information to work. Read the tool's argument requirements. For "browser.open_url," you must provide the URL you want to visit. You can optionally provide a PageIdToReplace if you want to redirect an existing tab instead of opening a new one.

**Step 4: Formatting Your Request**

Structure your request as JSON matching the tool's schema. For "browser.open_url," your JSON might look like:

```json
{
  "Url": "https://example.com"
}
```

**Step 5: Sending the Request to AntiGravity**

Your program sends the JSON request to AntiGravity's API, specifying which tool to call and providing the arguments.

**Step 6: Cortex Processes the Request**

Inside AntiGravity, the Cortex engine receives your request. It validates the arguments using ParseToolArgs, checking that everything matches the expected schema. If validation passes, Cortex queues the operation.

**Step 7: Execution**

When resources are available, Cortex changes the operation's state from QUEUED to RUNNING and executes the tool. For a browser tool, this might involve sending commands to a browser instance through automation protocols. For a file tool, it directly accesses the file system.

**Step 8: Receiving Results**

The tool completes (state becomes DONE) or fails (state becomes ERROR). Results are returned to your program as JSON. You can then examine the results and decide what to do next—perhaps call another tool, process the data, or report completion to a user.

**Step 9: Iteration**

Most workflows involve multiple tools. You might open a webpage, read its content, search for specific information, click a button, wait for a response, and read again. Each step uses different tools in sequence, building toward your ultimate goal.

---

### Artifact E: Real-World Analogy Map

Let me map each technical component to everyday objects and scenarios:

**AntiGravity System → Personal Assistant Robot**

Imagine hiring a robot assistant who can use your computer. That is AntiGravity.

**Tools → Buttons on the Robot's Control Panel**

Each tool is like a button that makes the robot do one specific thing. Press the "open webpage" button, and the robot navigates to a website. Press "search files" button, and the robot hunts through your documents.

**Arguments → Details You Write on Sticky Notes**

When you press a button, you hand the robot a sticky note with details. For the "open webpage" button, the note says which URL to visit. For "search files," the note says what to search for and where to look.

**JSON Schema → The Template for Sticky Notes**

Each button comes with a template showing exactly what information the sticky note must contain. Some fields are required, some are optional. If your note does not match the template, the robot refuses to act.

**Cortex Engine → The Robot's Brain**

This is the thinking part that coordinates everything. It tracks what the robot is currently doing, what it needs to do next, and what has already been completed. If something goes wrong, the brain notices and reports the error.

**State Machine → Task List with Status Markers**

Imagine a whiteboard showing all tasks: "Task 1: Waiting," "Task 2: In Progress," "Task 3: Done." The state machine is like that whiteboard, tracking each operation's current status.

**Browser Automation → Teaching the Robot to Use a Web Browser**

Instead of programming every possible interaction, browser automation gives the robot general skills: how to click, type, scroll, read. Then you give it specific instructions using those skills.

**Reverse Engineering → Figuring Out the Robot by Watching It Work**

The company that built the robot did not give you a manual. So you watch the robot carefully, take notes about what buttons exist and what happens when you press them, and write your own manual. That is what this document represents.

---

### Artifact F: Common Confusions and Mistakes

**Confusion 1: "Is AntiGravity a web browser like Chrome or Firefox?"**

**Why people think this:** The word "browser" appears everywhere in the document.

**Correct understanding:** AntiGravity is not a browser you use directly. It is automation software that *controls* actual browsers like Chrome. Think of it as a robot that knows how to use Chrome, not a replacement for Chrome.

---

**Confusion 2: "Do I need to understand reverse engineering to use AntiGravity?"**

**Why people think this:** The document is about reverse engineering, so maybe that is how you use the tool.

**Correct understanding:** Reverse engineering was how this documentation was *created*. To *use* AntiGravity, you just need to know the tool names and their arguments (which this document provides). The reverse engineering part is behind-the-scenes detective work.

---

**Confusion 3: "Can I run these commands on any computer?"**

**Why people think this:** The document shows command-line examples.

**Correct understanding:** The Unix commands shown (strings, grep) only work on Mac or Linux. However, the AntiGravity tools themselves work wherever AntiGravity is installed. The commands were just used to examine the program, not to use it.

---

**Confusion 4: "Why are some arguments marked as 'not required' when they seem essential?"**

**Why people think this:** Looking at the "browser.drag" tool, both PageID and Waypoints seem necessary, but they are marked "not required."

**Correct understanding:** The "required" marking comes from JSON schema tags in the code. The author is honestly reporting what they found—the tags were missing. However, logically, both fields *are* required or the tool cannot work. This reveals that the original developers might have been inconsistent with their schema annotations.

---

**Confusion 5: "What is the difference between 'search.workspace_simple' and 'search.in_directories'?"**

**Why people think this:** Both perform searches, so why have two tools?

**Correct understanding:** Different search tools serve different purposes. "workspace_simple" searches a default set of files (your current project). "search.in_directories" lets you explicitly specify which folders to search. It is like the difference between searching "my documents" automatically versus choosing exactly which folders to search.

---

**Confusion 6: "Can I create new tools or just use the existing twenty-seven?"**

**Why people think this:** Maybe the system is extensible.

**Correct understanding:** This document only shows built-in tools. Whether AntiGravity allows custom tools is not documented here. The system might support plugins or extensions, but that is not covered in what the author discovered through reverse engineering.

---

**Confusion 7: "Why does everything reference 'google3'?"**

**Why people think this:** The paths look unusual.

**Correct understanding:** "google3" is Google's internal code repository structure. Every Google project lives under that path. The inclusion of google3 paths confirms this is Google-internal software, not something publicly available (at least when this documentation was created).

---

**Confusion 8: "Is 'go.shape.struct' a specific Go language feature?"**

**Why people think this:** The phrase appears in struct definitions.

**Correct understanding:** This is not standard Go syntax you would write. It is how Go's compiler represents generic types internally in compiled binaries. When the author extracted strings, they saw these internal representations. You would never write "go.shape.struct" in actual Go code.

---

**Confusion 9: "Can I use AntiGravity to automate any website?"**

**Why people think this:** It is a browser automation tool.

**Correct understanding:** While AntiGravity can interact with most websites, some sites use anti-automation measures (like CAPTCHAs or bot detection). Also, automating certain sites might violate their terms of service. Technical capability does not equal legal permission.

---

**Confusion 10: "Why do browser tools use 'PageId' while file tools use 'AbsolutePath'?"**

**Why people think this:** The inconsistency seems arbitrary.

**Correct understanding:** Each tool references resources differently based on context. Browser tools use IDs because multiple tabs might have the same URL—you need a unique identifier. File tools use paths because that is how file systems work—each file has a unique location. The different approaches reflect different underlying systems.

---

## 5. Big-Picture Mental Model

Let me distill everything into one coherent mental model you can carry forward.

### The Core Idea

AntiGravity is a programmable autopilot for computers. Instead of sitting at your computer manually clicking, typing, and reading, you write instructions in a structured format, and AntiGravity executes them. It is the bridge between high-level intent ("find all documents about project Alpha") and low-level actions ("search these directories, read these files, extract matching text").

### The Main Workflow

Everything follows a cycle:

1. **Intent:** You have a goal (find information, complete a web form, analyze files).

2. **Translation:** You express that goal by selecting appropriate tools and providing arguments.

3. **Execution:** Cortex takes your structured request, validates it, and performs the actions using the tool implementations.

4. **Observation:** Results come back. You process them and decide the next step.

5. **Iteration:** Most goals require multiple tools used in sequence, each building on previous results.

This cycle mirrors how humans work, but at machine speed and scale. What might take you thirty minutes of manual clicking could complete in ten seconds through automation.

### The One Thing to Remember

**Tools are verbs; arguments are objects.**

Every action you want to perform maps to a tool (the verb) and arguments (the objects the verb acts upon). "Open URL" is a verb taking a URL object. "Read file" is a verb taking a file path object. "Search directories" is a verb taking search query and directory list objects.

If you internalize this verb-object pattern, you can look at any tool documentation and immediately understand how to use it. Find the verb that matches your intent, provide the objects it needs, and let the system execute.

---

## 6. Teaching Test — Validating Your Understanding

Let me create questions at different difficulty levels to test comprehension.

### Beginner Level Questions

**Q1:** What is reverse engineering, and why did the document's author need to use it?

**Expected answer:** Reverse engineering means figuring out how something works by examining the finished product instead of having documentation. The author used it because AntiGravity does not have public documentation, so they examined the compiled program to discover its capabilities.

---

**Q2:** What is the purpose of the "strings" command used in the document?

**Expected answer:** The strings command extracts human-readable text from compiled binary files. The author used it to get all text strings out of AntiGravity so they could search for patterns that reveal tool definitions.

---

**Q3:** What does "PageId" mean in browser tools?

**Expected answer:** PageId is a unique identifier for a specific browser tab or page. When you have multiple tabs open, each has a different PageId so tools know which tab to act on.

---

**Q4:** Why do tools require arguments?

**Expected answer:** Arguments provide the specific details a tool needs to do its job. A generic "open file" tool needs to know *which* file to open, so you provide the file path as an argument.

---

**Q5:** What is JSON Schema used for in this context?

**Expected answer:** JSON Schema defines the structure of valid requests for each tool. It specifies what fields are required, what data type each field expects, and what the fields mean. This helps programs construct correct requests and helps AntiGravity validate incoming requests.

---

### Intermediate Level Questions

**Q6:** Explain why the "browser.drag" tool uses an array of waypoints instead of just a start point and end point.

**Expected answer:** A simple start-to-end drag only creates a straight line. Many interactions need curved paths or specific routes (like drawing a signature or navigating around obstacles). An array of waypoints allows complex, multi-segment paths by connecting the dots in sequence.

---

**Q7:** What is the difference between "search.in_file" and "search.in_directories"?

**Expected answer:** "search.in_file" searches within a single file you specify, useful when you know exactly which file contains the information. "search.in_directories" searches across multiple folders and all files within them, useful when you do not know which specific file has the information you seek.

---

**Q8:** Why would the Cortex state machine have both ERROR and INTERRUPTED states?

**Expected answer:** ERROR means the operation tried to run but failed because something went wrong (file not found, network timeout, invalid argument). INTERRUPTED means the operation was deliberately stopped before completion, perhaps by a user canceling it or the system shutting down. They represent different failure modes requiring different handling.

---

**Q9:** What does the presence of "google3" paths tell us about AntiGravity?

**Expected answer:** The google3 paths confirm this is Google-internal software developed within Google's codebase. It suggests AntiGravity is not a public product but an internal tool used within Google's infrastructure.

---

**Q10:** Why does "fs.view_file_range" specify that line numbers are 1-indexed?

**Expected answer:** Most programming languages start counting at zero, but humans naturally start at one. By explicitly stating "1-indexed," the documentation clarifies that line one means the first line (not the second line, as it would be in zero-indexed systems). This prevents off-by-one errors when specifying line ranges.

---

### Teach-Back Prompts

**Prompt 1:** "You have two minutes to explain to a friend who has never programmed why this document exists and what it contains."

**Expected answer:** "A company called Google made a tool called AntiGravity that can automatically control web browsers and computers. They did not share how it works publicly. Someone who needed to know examined the program file itself and figured out what commands it understands. They wrote this document listing all twenty-seven commands you can give AntiGravity, like 'open this webpage' or 'search these files.' Each command needs specific information, which the document explains. Now anyone reading this knows exactly how to talk to AntiGravity."

---

**Prompt 2:** "Your colleague wants to use AntiGravity to automatically test their website. Walk them through which tools they would likely use."

**Expected answer:** "First, use 'browser.open_url' to visit your website. Then use 'browser.read_page' to see the content. If you need to interact, use tools like 'browser.scroll_down' to navigate, element-specific tools to click buttons or fill forms. Use 'browser.capture_screenshot' to document what the page looks like. If there are issues, use 'browser.capture_console_logs' to see JavaScript errors. Each tool takes specific arguments—like which page to interact with and what exactly to do."

---

**Prompt 3:** "Explain the relationship between tools, arguments, and JSON Schema as if teaching a beginner."

**Expected answer:** "Tools are like buttons on a control panel—each button makes the system do one thing. Arguments are like a form you fill out saying exactly what you want—which website to open, which file to read. JSON Schema is like the template for that form showing what information is required and optional. Without the correct form filled out properly, the button will not work. JSON Schema ensures your form is filled out correctly before the system tries to execute your command."

---

## 7. Optional Advanced Section — For Those Ready to Go Deeper

Now that you understand the fundamentals, let me share how experts would analyze this document and what questions they might ask.

### Expert Observations

**On Architecture Patterns:**

The tool organization reveals a well-designed system. Browser tools share a common prefix and consistent patterns (PageId appearing in most). This namespace organization prevents naming conflicts and makes the API intuitive. The separation between broad tools ("search.workspace_simple") and specific tools ("search.in_file") shows attention to both convenience and precision—cover common use cases simply while allowing detailed control when needed.

**On Security Implications:**

The memory tool requiring a "Reason" field suggests audit logging or security controls. Requiring justification for memory access creates accountability. An expert would wonder whether these reasons are stored, who can review them, and whether they affect access control decisions.

**On State Machine Design:**

The granular state tracking (GENERATING, QUEUED, PENDING, RUNNING, WAITING, DONE) indicates sophisticated orchestration capabilities. This level of detail allows for:
- Progress tracking and user feedback
- Resource management (not running everything simultaneously)
- Error recovery and retry logic
- Debugging and performance analysis

An expert would recognize this as characteristic of enterprise-grade workflow engines.

**On Tool Evolution:**

The presence of both "browser.capture_screenshot" and "browser.capture_screenshot_and_save" suggests the API evolved over time. The separate saving arguments were likely added later to support artifact creation without breaking existing tools. This backward-compatibility approach is standard in mature APIs.

**On Performance Considerations:**

Tools like "fs.view_file_items" with ItemOffset pagination reveal awareness of performance constraints. Large files cannot be processed in single operations without exhausting memory. The pagination design allows streaming processing of arbitrarily large inputs. An expert would ask about chunk sizes, caching strategies, and how the system handles concurrent access to the same file.

### Trade-offs and Design Choices

**Generic vs. Specific Tools:**

AntiGravity balances generic ("browser.move_mouse" to any pixel) with specific ("browser.select_dropdown" for dropdowns) tools. Generic tools offer maximum flexibility but require more detailed arguments. Specific tools are less flexible but handle common cases elegantly. The designers chose to provide both, accepting some redundancy for better developer experience.

**Synchronous vs. Asynchronous Execution:**

The state machine suggests asynchronous operation—requests are queued and processed when resources are available. This complicates the programming model (you must track operation states) but enables parallelism and better resource utilization. Synchronous execution would be simpler but less scalable.

**Validation Timing:**

Argument validation happens via ParseToolArgs before execution. This fail-fast approach catches errors immediately rather than during execution. The trade-off is that schema changes must be coordinated—if you change a tool's arguments, all callers must update simultaneously. Runtime flexibility is sacrificed for reliability.

**Tool Granularity:**

Should "scroll up" and "scroll down" be separate tools or one "scroll" tool with a direction argument? The designers chose separation. This makes each tool simpler and slightly faster (no argument parsing for direction) but increases the API surface. It is a classic trade-off between simplicity and compactness.

### Questions an Expert Would Ask

**Q: How does PageId generation work?**

Browser tabs come and go. How are IDs assigned? Are they reused? What happens if you reference a PageId after closing that tab?

**Q: What concurrency guarantees exist?**

If two operations try to modify the same file simultaneously, what happens? Does AntiGravity handle locking, or must callers coordinate?

**Q: How are errors surfaced?**

When a tool fails, what information is returned? Stack traces? Error codes? Retry suggestions?

**Q: What about authentication and authorization?**

Can any caller use any tool? Are there permission systems? Rate limits?

**Q: How observable is the system?**

Beyond state tracking, what monitoring and logging exists? Can you replay operations for debugging?

**Q: What is the relationship to MCP (Model Context Protocol)?**

The state machine mentions "CORTEX_STEP_TYPE_MCP_TOOL" suggesting integration with external services via MCP. How does that work?

These questions reveal the depth of consideration in production systems. The documented tools are just the visible interface—underneath lies sophisticated orchestration, error handling, resource management, and monitoring.

### Why This Matters

Understanding these advanced considerations helps you:

1. **Anticipate limitations:** Knowing the trade-offs helps you design around them
2. **Debug issues:** Understanding the architecture helps diagnose problems
3. **Make informed decisions:** Recognizing design patterns helps you choose appropriate tools
4. **Contribute improvements:** Identifying gaps or inconsistencies enables constructive feedback

---

## Closing Thoughts

You have now completed a comprehensive journey through this reverse engineering document. You started knowing nothing about AntiGravity, binary analysis, or browser automation. Now you understand:

- What reverse engineering is and how it works
- How AntiGravity is structured and what it does
- All twenty-seven tools and their purposes
- How to read and interpret tool documentation
- The patterns and principles underlying the system

Most importantly, you can now explain this document to someone else confidently. You have not just memorized facts—you have built a mental model of how the system works and why it was designed this way.

Browser automation might seem niche, but the principles here apply broadly: APIs, arguments, schemas, state machines, and tool composition are fundamental to modern software. Understanding one system deeply teaches you patterns you will see everywhere.

Whether you need to integrate with AntiGravity, build something similar, or simply understand how sophisticated automation works, you now have the knowledge foundation to proceed. Welcome to the world of programmatic control, where computers can execute human-like tasks at machine speed and scale.