# Reverse Engineering AntiGravity browser (Step by Step)
AntiGraviti tools parsed based on reverse engineering and ParseToolArgs structs found in the binary.
We need strings, the juice is in the DATA block and not in the code.

I have used the following commands on my MacOS:
```shell
1. Extract strings to file for faster iterations
# strings /Applications/Antigravity.app/Contents/Resources/app/extensions/antigravity/bin/language_server_macos_arm > ~/Downloads/language_server_macos_arm_strings.txt

2. Search tools
# grep -i "google3/third_party/jetski/cortex/utils/utils.ParseToolArgs\[" ~/Downloads/language_server_macos_arm_strings.txt
# grep --line-buffered "ParseToolArgs\[go.shape.struct {.*}" ~/Downloads/language_server_macos_arm_strings.txt

3. Seach all possible CORTEX states
# grep -i "^CORTEX_STEP_.*$"  ~/Downloads/language_server_macos_arm_strings.txt | uniq

4. Found some more researchers working on it:
https://alokbishoyi.com/blogposts/reverse-engineering-browser-automation.html
```

# Findings
AntiGraviti tools parsed based on reverse engineering and `ParseToolArgs` structs found in the binary.

## 1. Browser drag tool

### Evidence (Go struct)

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageID string "jsonschema_description:\"page_id of the Browser page to perform the drag operation on\"";
    Waypoints []google3/third_party/jetski/cortex/tools/tools.waypoint "jsonschema_description:\"A series of pixel coordinates defining the drag path. When this tool call is executed, the first waypoint will be clicked, then the mouse will be dragged to each subsequent waypoint in the provided order, and finally the mouse will be released at the last waypoint.\""
  }
]
```

### Logical tool name

`browser.drag`

### Purpose

Perform a drag gesture across a page by moving the mouse along a sequence of waypoints, clicking at the first, dragging through all of them, and releasing at the last.

### Arguments

| Field       | Type                | Required? (per tags) | Description                                                                                                                |
| ----------- | ------------------- | -------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `PageID`    | string              | **not** marked       | Page ID of the browser page to perform the drag operation on.                                                              |
| `Waypoints` | array of `waypoint` | **not** marked       | Series of pixel coordinates defining the drag path (first clicked, then dragged through, mouse released at last waypoint). |

> Note: there is **no `jsonschema:"required"` tag**, but logically both are needed.

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.drag.json",
  "title": "browser.drag",
  "type": "object",
  "properties": {
    "PageID": {
      "type": "string",
      "description": "page_id of the Browser page to perform the drag operation on."
    },
    "Waypoints": {
      "type": "array",
      "description": "A series of pixel coordinates defining the drag path. First waypoint is clicked, then mouse is dragged through each subsequent waypoint and released at the last.",
      "items": {
        "type": "object",
        "description": "tools.waypoint (likely holds pixel coordinates; defined elsewhere)."
      }
    }
  },
  "additionalProperties": false
}
```

---

## 2. Terminal read tool

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    ProcessID string "jsonschema:\"required\" jsonschema_description:\"Process ID of the terminal to read.\"";
    Name string "jsonschema:\"required\" jsonschema_description:\"Name of the terminal to read.\""
  }
]
```

### Logical tool name

`terminal.read`

### Purpose

Read from a specific terminal session identified by process ID and name.

### Arguments

| Field       | Type   | Required? | Description                         |
| ----------- | ------ | --------- | ----------------------------------- |
| `ProcessID` | string | yes       | Process ID of the terminal to read. |
| `Name`      | string | yes       | Name of the terminal to read.       |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "terminal.read.json",
  "title": "terminal.read",
  "type": "object",
  "properties": {
    "ProcessID": {
      "type": "string",
      "description": "Process ID of the terminal to read."
    },
    "Name": {
      "type": "string",
      "description": "Name of the terminal to read."
    }
  },
  "required": ["ProcessID", "Name"],
  "additionalProperties": false
}
```

---

## 3. Raw query tool

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Query string "json:\"query\""
  }
]
```

### Logical tool name

`search.raw_query`

### Purpose

Send a raw query string (likely for some internal search or suggestion mechanism). No description, only the `json:"query"` tag.

### Arguments

| Field   | Type   | Required?  | Description       |
| ------- | ------ | ---------- | ----------------- |
| `query` | string | not marked | Raw query string. |

### JSON Schema

*(We treat `query` as required for sanity, but strictly the struct has no `jsonschema:"required"` tag.)*

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "search.raw_query.json",
  "title": "search.raw_query",
  "type": "object",
  "properties": {
    "query": {
      "type": "string",
      "description": "Raw query string."
    }
  },
  "required": ["query"],
  "additionalProperties": false
}
```

---

## 4. Web search tool

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Query string "jsonschema:\"required\" json:\"query\"";
    Domain string "json:\"domain\" jsonschema_description:\"Optional domain to recommend the search prioritize\"";
    SearchType string "json:\"search_type\" jsonschema_description:\"Optional, The type of search to perform. One of: web, image.\" jsonschema:\"enum=web,enum=image\""
  }
]
```

### Logical tool name

`web.search`

### Purpose

Perform a web or image search, optionally biased toward a domain.

### Arguments

| Field         | Type   | Required? | Description                                         |
| ------------- | ------ | --------- | --------------------------------------------------- |
| `query`       | string | yes       | Search query.                                       |
| `domain`      | string | no        | Optional domain to recommend the search prioritize. |
| `search_type` | string | no        | Optional; one of `web`, `image`.                    |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "web.search.json",
  "title": "web.search",
  "type": "object",
  "properties": {
    "query": {
      "type": "string",
      "description": "Search query."
    },
    "domain": {
      "type": "string",
      "description": "Optional domain to recommend the search prioritize."
    },
    "search_type": {
      "type": "string",
      "description": "Optional type of search to perform.",
      "enum": ["web", "image"]
    }
  },
  "required": ["query"],
  "additionalProperties": false
}
```

---

## 5. Document chunk viewer

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    DocumentId string "jsonschema:\"required\" json:\"document_id\" jsonschema_description:\"The ID of the document that the chunk belongs to\"";
    Position int "jsonschema:\"required\" json:\"position\" jsonschema_description:\"The position of the chunk to view\""
  }
]
```

### Logical tool name

`docs.view_chunk`

### Purpose

View a particular chunk of a document by ID and numeric position.

### Arguments

| Field         | Type   | Required? | Description                              |
| ------------- | ------ | --------- | ---------------------------------------- |
| `document_id` | string | yes       | ID of the document the chunk belongs to. |
| `position`    | int    | yes       | Position/index of the chunk to view.     |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "docs.view_chunk.json",
  "title": "docs.view_chunk",
  "type": "object",
  "properties": {
    "document_id": {
      "type": "string",
      "description": "The ID of the document that the chunk belongs to."
    },
    "position": {
      "type": "integer",
      "description": "The position of the chunk to view."
    }
  },
  "required": ["document_id", "position"],
  "additionalProperties": false
}
```

---

## 6. Raw URL reader

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Url string "jsonschema:\"required\" jsonschema_description:\"URL to read content from\""
  }
]
```

### Logical tool name

`http.read_url`

### Purpose

Fetch content from a URL (outside the browser automation path).

### Arguments

| Field | Type   | Required? | Description               |
| ----- | ------ | --------- | ------------------------- |
| `Url` | string | yes       | URL to read content from. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "http.read_url.json",
  "title": "http.read_url",
  "type": "object",
  "properties": {
    "Url": {
      "type": "string",
      "format": "uri",
      "description": "URL to read content from."
    }
  },
  "required": ["Url"],
  "additionalProperties": false
}
```

---

## 7. Suggestions tool

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Suggestions []string "jsonschema:\"required\" jsonschema_description:\"List of suggestions. Each should be at most a couple words, do not return more than 3 options.\""
  }
]
```

### Logical tool name

`ui.suggest_options`

### Purpose

Provide a small set of UI suggestions/options.

### Arguments

| Field         | Type     | Required? | Description                                                                          |
| ------------- | -------- | --------- | ------------------------------------------------------------------------------------ |
| `Suggestions` | []string | yes       | List of suggestions. Each at most a couple words; do not return more than 3 options. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "ui.suggest_options.json",
  "title": "ui.suggest_options",
  "type": "object",
  "properties": {
    "Suggestions": {
      "type": "array",
      "description": "List of suggestions. Each should be at most a couple words, do not return more than 3 options.",
      "items": {
        "type": "string"
      },
      "maxItems": 3
    }
  },
  "required": ["Suggestions"],
  "additionalProperties": false
}
```

---

## 8. File view (with optional line range)

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    AbsolutePath string "jsonschema:\"required\" jsonschema_description:\"Path to file to view. Must be an absolute path.\"";
    StartLine uint32 "jsonschema_description:\"Optional. Startline to view, 1-indexed as usual, inclusive. This value must be less than or equal to EndLine.\"";
    EndLine uint32 "jsonschema_description:\"Optional. Endline to view, 1-indexed as usual, inclusive. This value must be greater than or equal to StartLine.\""
  }
]
```

### Logical tool name

`fs.view_file_range`

### Purpose

View a file, optionally constrained to a 1-based inclusive line range.

### Arguments

| Field          | Type   | Required? | Description                                                             |
| -------------- | ------ | --------- | ----------------------------------------------------------------------- |
| `AbsolutePath` | string | yes       | Path to file to view. Must be an absolute path.                         |
| `StartLine`    | uint32 | no        | Optional 1-based inclusive start line; must be ≤ `EndLine` if provided. |
| `EndLine`      | uint32 | no        | Optional 1-based inclusive end line; must be ≥ `StartLine` if provided. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "fs.view_file_range.json",
  "title": "fs.view_file_range",
  "type": "object",
  "properties": {
    "AbsolutePath": {
      "type": "string",
      "description": "Path to file to view. Must be an absolute path."
    },
    "StartLine": {
      "type": "integer",
      "minimum": 1,
      "description": "Optional 1-based inclusive start line. Must be <= EndLine when provided."
    },
    "EndLine": {
      "type": "integer",
      "minimum": 1,
      "description": "Optional 1-based inclusive end line. Must be >= StartLine when provided."
    }
  },
  "required": ["AbsolutePath"],
  "additionalProperties": false
}
```

---

## 9. File items (paginated)

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    AbsolutePath string "jsonschema:\"required\" jsonschema_description:\"Path to file to view. Must be an absolute path.\"";
    ItemOffset uint32 "jsonschema_description:\"Offset of items to show. This is used for pagination. The first request to a file should have an offset of 0.\""
  }
]
```

### Logical tool name

`fs.view_file_items`

### Purpose

Paginated access to “items” in a file (symbols / chunks / etc.).

### Arguments

| Field          | Type   | Required? | Description                                                       |
| -------------- | ------ | --------- | ----------------------------------------------------------------- |
| `AbsolutePath` | string | yes       | Path to file to view. Must be an absolute path.                   |
| `ItemOffset`   | uint32 | no        | Offset of items to show; used for pagination (first request = 0). |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "fs.view_file_items.json",
  "title": "fs.view_file_items",
  "type": "object",
  "properties": {
    "AbsolutePath": {
      "type": "string",
      "description": "Path to file to view. Must be an absolute path."
    },
    "ItemOffset": {
      "type": "integer",
      "minimum": 0,
      "description": "Offset of items to show. Used for pagination. First request should use 0."
    }
  },
  "required": ["AbsolutePath"],
  "additionalProperties": false
}
```

---

## 10. Directory listing

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    DirectoryPath string "jsonschema:\"required\" jsonschema_description:\"Path to list contents of, should be absolute path to a directory\""
  }
]
```

### Logical tool name

`fs.list_directory`

### Purpose

List contents of a directory.

### Arguments

| Field           | Type   | Required? | Description                                     |
| --------------- | ------ | --------- | ----------------------------------------------- |
| `DirectoryPath` | string | yes       | Absolute path to directory to list contents of. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "fs.list_directory.json",
  "title": "fs.list_directory",
  "type": "object",
  "properties": {
    "DirectoryPath": {
      "type": "string",
      "description": "Path to list contents of; should be absolute path to a directory."
    }
  },
  "required": ["DirectoryPath"],
  "additionalProperties": false
}
```

---

## 11. Search in a single file

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Query string "jsonschema:\"required\" jsonschema_description:\"Search query\"";
    AbsolutePath string "jsonschema:\"required\" jsonschema_description:\"Absolute path to the file to search in\""
  }
]
```

### Logical tool name

`search.in_file`

### Purpose

Run a search query within a specific file.

### Arguments

| Field          | Type   | Required? | Description                             |
| -------------- | ------ | --------- | --------------------------------------- |
| `Query`        | string | yes       | Search query.                           |
| `AbsolutePath` | string | yes       | Absolute path to the file to search in. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "search.in_file.json",
  "title": "search.in_file",
  "type": "object",
  "properties": {
    "Query": {
      "type": "string",
      "description": "Search query."
    },
    "AbsolutePath": {
      "type": "string",
      "description": "Absolute path to the file to search in."
    }
  },
  "required": ["Query", "AbsolutePath"],
  "additionalProperties": false
}
```

---

## 12. Search across directories

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Query string "jsonschema:\"required\" jsonschema_description:\"Search query\"";
    TargetDirectories []string "jsonschema:\"required\" jsonschema_description:\"List of absolute paths to directories to search over\""
  }
]
```

### Logical tool name

`search.in_directories`

### Purpose

Search for a query across multiple directories.

### Arguments

| Field               | Type     | Required? | Description                                           |
| ------------------- | -------- | --------- | ----------------------------------------------------- |
| `Query`             | string   | yes       | Search query.                                         |
| `TargetDirectories` | []string | yes       | List of absolute paths to directories to search over. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "search.in_directories.json",
  "title": "search.in_directories",
  "type": "object",
  "properties": {
    "Query": {
      "type": "string",
      "description": "Search query."
    },
    "TargetDirectories": {
      "type": "array",
      "description": "List of absolute paths to directories to search over.",
      "items": {
        "type": "string"
      }
    }
  },
  "required": ["Query", "TargetDirectories"],
  "additionalProperties": false
}
```

---

## 13. Simple search

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Query string "jsonschema:\"required\" jsonschema_description:\"Search query\""
  }
]
```

### Logical tool name

`search.workspace_simple`

### Purpose

Simple search over a default corpus (e.g. workspace, index).

### Arguments

| Field   | Type   | Required? | Description   |
| ------- | ------ | --------- | ------------- |
| `Query` | string | yes       | Search query. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "search.workspace_simple.json",
  "title": "search.workspace_simple",
  "type": "object",
  "properties": {
    "Query": {
      "type": "string",
      "description": "Search query."
    }
  },
  "required": ["Query"],
  "additionalProperties": false
}
```

---

## 14. Search with path-only / directory options

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Query string "jsonschema:\"required\" jsonschema_description:\"Search query, including filters\"";
    OnlyPaths bool "jsonschema_description:\"Optional, return only file paths, not snippets\"";
    AllowDirs bool "jsonschema_description:\"Optional, allow including directories in results\""
  }
]
```

### Logical tool name

`search.paths_only`

### Purpose

Search with filters for returning only paths vs snippets and whether directories are included.

### Arguments

| Field       | Type   | Required? | Description                                      |
| ----------- | ------ | --------- | ------------------------------------------------ |
| `Query`     | string | yes       | Search query, including filters.                 |
| `OnlyPaths` | bool   | no        | If true, return only file paths, not snippets.   |
| `AllowDirs` | bool   | no        | If true, allow including directories in results. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "search.paths_only.json",
  "title": "search.paths_only",
  "type": "object",
  "properties": {
    "Query": {
      "type": "string",
      "description": "Search query, including filters."
    },
    "OnlyPaths": {
      "type": "boolean",
      "description": "Optional; if true, return only file paths, not snippets."
    },
    "AllowDirs": {
      "type": "boolean",
      "description": "Optional; if true, allow including directories in results."
    }
  },
  "required": ["Query"],
  "additionalProperties": false
}
```

---

## 15. Delete path within knowledge / artifacts

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PathToDelete string "jsonschema_description:\"Absolute path to the file or directory to delete. Must be either within an artifacts/ subdirectory of a Knowledge Item, or a top-level Knowledge Item directory.\""
  }
]
```

### Logical tool name

`fs.delete_knowledge_path`

### Purpose

Delete a file or directory under a Knowledge Item (or top-level KI dir).

### Arguments

| Field          | Type   | Required? (per tags) | Description                                                                                          |
| -------------- | ------ | -------------------- | ---------------------------------------------------------------------------------------------------- |
| `PathToDelete` | string | not marked           | Absolute path to the file/directory to delete, restricted to artifacts/ or top-level KI directories. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "fs.delete_knowledge_path.json",
  "title": "fs.delete_knowledge_path",
  "type": "object",
  "properties": {
    "PathToDelete": {
      "type": "string",
      "description": "Absolute path to the file or directory to delete. Must be either within an artifacts/ subdirectory of a Knowledge Item, or a top-level Knowledge Item directory."
    }
  },
  "additionalProperties": false
}
```

---

## 16. Write file (optionally as artifact)

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    google3/third_party/jetski/cortex/tools/tools.writeToFileArgs;
    IsArtifact bool "jsonschema:\"required\" jsonschema_description:\"Set this to true when creating an artifact file.\"";
    ArtifactMetadata *google3/third_party/jetski/cortex/tools/tools.artifactMetadata "jsonschema_description:\"Metadata for the artifact, required when IsArtifact is true.\""
  }
]
```

### Logical tool name

`code.write_file_with_artifact`

### Purpose

Write to a file, and optionally mark it as an artifact with metadata.

### Arguments

Embedded types are opaque in this output, but structurally:

| Field              | Type    | Required?  | Description                                                                  |
| ------------------ | ------- | ---------- | ---------------------------------------------------------------------------- |
| `writeToFileArgs`  | object  | implicit   | Fields of `tools.writeToFileArgs` (path, contents, etc.; defined elsewhere). |
| `IsArtifact`       | bool    | yes        | Set to true when creating an artifact file.                                  |
| `ArtifactMetadata` | object* | not marked | Metadata for the artifact; required when `IsArtifact` is true.               |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "code.write_file_with_artifact.json",
  "title": "code.write_file_with_artifact",
  "type": "object",
  "properties": {
    "writeToFileArgs": {
      "type": "object",
      "description": "Arguments describing the file to write and its contents (tools.writeToFileArgs)."
    },
    "IsArtifact": {
      "type": "boolean",
      "description": "Set this to true when creating an artifact file."
    },
    "ArtifactMetadata": {
      "type": "object",
      "description": "Metadata for the artifact, required when IsArtifact is true (tools.artifactMetadata)."
    }
  },
  "required": ["IsArtifact"],
  "allOf": [
    {
      "if": {
        "properties": {
          "IsArtifact": { "const": true }
        }
      },
      "then": {
        "required": ["ArtifactMetadata"]
      }
    }
  ],
  "additionalProperties": false
}
```

---

## 17. Browser scroll (down)

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageId string "jsonschema:\"required\" jsonschema_description:\"page_id of the Browser page to scroll down.\"";
    ScrollToEnd bool "jsonschema_description:\"if true, scroll to the very bottom of the page\"";
    ScrollByElementIndex bool "jsonschema_description:\"if true, scroll by the element with the given index\"";
    ElementIndex int32 "jsonschema:\"required\" jsonschema_description:\"index of the element to scroll by\""
  }
]
```

### Logical tool name

`browser.scroll_down`

### Purpose

Scroll a browser page down, either to the very bottom or relative to a DOM element index.

### Arguments

| Field                  | Type   | Required? | Description                                          |
| ---------------------- | ------ | --------- | ---------------------------------------------------- |
| `PageId`               | string | yes       | page_id of the browser page to scroll down.          |
| `ScrollToEnd`          | bool   | no        | If true, scroll to the very bottom of the page.      |
| `ScrollByElementIndex` | bool   | no        | If true, scroll by the element with the given index. |
| `ElementIndex`         | int32  | yes       | Index of the element to scroll by.                   |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.scroll_down.json",
  "title": "browser.scroll_down",
  "type": "object",
  "properties": {
    "PageId": {
      "type": "string",
      "description": "page_id of the Browser page to scroll down."
    },
    "ScrollToEnd": {
      "type": "boolean",
      "description": "If true, scroll to the very bottom of the page."
    },
    "ScrollByElementIndex": {
      "type": "boolean",
      "description": "If true, scroll by the element with the given index."
    },
    "ElementIndex": {
      "type": "integer",
      "description": "Index of the element to scroll by."
    }
  },
  "required": ["PageId", "ElementIndex"],
  "additionalProperties": false
}
```

---

## 18. Browser scroll (up)

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageId string "jsonschema:\"required\" jsonschema_description:\"page_id of the Browser page to scroll up.\"";
    ScrollToEnd bool "jsonschema_description:\"if true, scroll to the very top of the page\"";
    ScrollByElementIndex bool "jsonschema_description:\"if true, scroll by the element with the given index\"";
    ElementIndex int32 "jsonschema:\"required\" jsonschema_description:\"index of the element to scroll by\""
  }
]
```

### Logical tool name

`browser.scroll_up`

### Purpose

Scroll a browser page up, either to the top or by element index.

### Arguments

Same shape as `browser.scroll_down`, different description.

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.scroll_up.json",
  "title": "browser.scroll_up",
  "type": "object",
  "properties": {
    "PageId": {
      "type": "string",
      "description": "page_id of the Browser page to scroll up."
    },
    "ScrollToEnd": {
      "type": "boolean",
      "description": "If true, scroll to the very top of the page."
    },
    "ScrollByElementIndex": {
      "type": "boolean",
      "description": "If true, scroll by the element with the given index."
    },
    "ElementIndex": {
      "type": "integer",
      "description": "Index of the element to scroll by."
    }
  },
  "required": ["PageId", "ElementIndex"],
  "additionalProperties": false
}
```

---

## 19. Browser select dropdown

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageId string "jsonschema:\"required\" jsonschema_description:\"The page_id of the browser page containing the dropdown element.\"";
    Index int32 "jsonschema:\"required\" jsonschema_description:\"Index of the annotated DOM select element to select an option from.\"";
    Value string "jsonschema:\"required\" jsonschema_description:\"The value or text of the option to select from the dropdown.\""
  }
]
```

### Logical tool name

`browser.select_dropdown`

### Purpose

Select an option from a `<select>` element on a page.

### Arguments

| Field    | Type   | Required? | Description                                    |
| -------- | ------ | --------- | ---------------------------------------------- |
| `PageId` | string | yes       | page_id of the page containing the dropdown.   |
| `Index`  | int32  | yes       | Index of the annotated DOM select element.     |
| `Value`  | string | yes       | Value or visible text of the option to select. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.select_dropdown.json",
  "title": "browser.select_dropdown",
  "type": "object",
  "properties": {
    "PageId": {
      "type": "string",
      "description": "The page_id of the browser page containing the dropdown element."
    },
    "Index": {
      "type": "integer",
      "description": "Index of the annotated DOM select element to select an option from."
    },
    "Value": {
      "type": "string",
      "description": "The value or text of the option to select from the dropdown."
    }
  },
  "required": ["PageId", "Index", "Value"],
  "additionalProperties": false
}
```

---

## 20. Browser move mouse

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageId string "jsonschema:\"required\" jsonschema_description:\"page_id of the Browser page to move the mouse cursor to.\"";
    X int32 "jsonschema:\"required\" jsonschema_description:\"x-coordinate of the pixel to move the mouse cursor to.\"";
    Y int32 "jsonschema:\"required\" jsonschema_description:\"y-coordinate of the pixel to move the mouse cursor to.\""
  }
]
```

### Logical tool name

`browser.move_mouse`

### Purpose

Move the mouse cursor to a specific pixel on a browser page.

### Arguments

| Field    | Type   | Required? | Description                             |
| -------- | ------ | --------- | --------------------------------------- |
| `PageId` | string | yes       | page_id of the target browser page.     |
| `X`      | int32  | yes       | X-coordinate of pixel to move mouse to. |
| `Y`      | int32  | yes       | Y-coordinate of pixel to move mouse to. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.move_mouse.json",
  "title": "browser.move_mouse",
  "type": "object",
  "properties": {
    "PageId": {
      "type": "string",
      "description": "page_id of the Browser page to move the mouse cursor to."
    },
    "X": {
      "type": "integer",
      "description": "x-coordinate of the pixel to move the mouse cursor to."
    },
    "Y": {
      "type": "integer",
      "description": "y-coordinate of the pixel to move the mouse cursor to."
    }
  },
  "required": ["PageId", "X", "Y"],
  "additionalProperties": false
}
```

---

## 21. Browser capture console logs

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageId string "jsonschema:\"required\" jsonschema_description:\"page_id of the Browser page to capture console logs of.\""
  }
]
```

### Logical tool name

`browser.capture_console_logs`

### Purpose

Capture console logs from a given browser page.

### Arguments

| Field    | Type   | Required? | Description                               |
| -------- | ------ | --------- | ----------------------------------------- |
| `PageId` | string | yes       | page_id of the page to capture logs from. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.capture_console_logs.json",
  "title": "browser.capture_console_logs",
  "type": "object",
  "properties": {
    "PageId": {
      "type": "string",
      "description": "page_id of the Browser page to capture console logs of."
    }
  },
  "required": ["PageId"],
  "additionalProperties": false
}
```

---

## 22. No-argument tool

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {}
]
```

### Logical tool name

`no_args_tool` (real name unknown)

### Purpose

Some tool that takes no arguments (exact behavior unknown from this evidence).

### Arguments

None.

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "no_args_tool.json",
  "title": "no_args_tool",
  "type": "object",
  "properties": {},
  "additionalProperties": false
}
```

---

## 23. Browser get DOM tree

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageId string "jsonschema:\"required\" jsonschema_description:\"page_id of the Browser page to get the DOM tree of\""
  }
]
```

### Logical tool name

`browser.get_dom_tree`

### Purpose

Retrieve the DOM tree for a given browser page.

### Arguments

| Field    | Type   | Required? | Description                                 |
| -------- | ------ | --------- | ------------------------------------------- |
| `PageId` | string | yes       | page_id of the page to get the DOM tree of. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.get_dom_tree.json",
  "title": "browser.get_dom_tree",
  "type": "object",
  "properties": {
    "PageId": {
      "type": "string",
      "description": "page_id of the Browser page to get the DOM tree of."
    }
  },
  "required": ["PageId"],
  "additionalProperties": false
}
```

---

## 24. Browser read page

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    PageId string "jsonschema:\"required\" jsonschema_description:\"page_id of the Browser page to read\""
  }
]
```

### Logical tool name

`browser.read_page`

### Purpose

Read/extract content from a browser page.

### Arguments

| Field    | Type   | Required? | Description                          |
| -------- | ------ | --------- | ------------------------------------ |
| `PageId` | string | yes       | page_id of the Browser page to read. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.read_page.json",
  "title": "browser.read_page",
  "type": "object",
  "properties": {
    "PageId": {
      "type": "string",
      "description": "page_id of the Browser page to read."
    }
  },
  "required": ["PageId"],
  "additionalProperties": false
}
```

---

## 25. Browser screenshot (capture + saving)

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    google3/third_party/jetski/cortex/tools/tools.captureBrowserScreenshotToolArgs;
    google3/third_party/jetski/cortex/tools/tools.captureBrowserScreenshotSavingArgs
  }
]
```

### Logical tool name

`browser.capture_screenshot_and_save`

### Purpose

Capture a browser screenshot and save it (likely as an artifact or file).

### Arguments

Struct is a merge of two embedded structs:

| Field                                | Type   | Required? | Description                                       |
| ------------------------------------ | ------ | --------- | ------------------------------------------------- |
| `captureBrowserScreenshotToolArgs`   | object | implicit  | Fields defining what/where to capture.            |
| `captureBrowserScreenshotSavingArgs` | object | implicit  | Fields defining where/how to save the screenshot. |

*(Exact internal fields are in those embedded types, not in this output.)*

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.capture_screenshot_and_save.json",
  "title": "browser.capture_screenshot_and_save",
  "type": "object",
  "properties": {
    "captureBrowserScreenshotToolArgs": {
      "type": "object",
      "description": "Arguments describing which browser page/region to capture (captureBrowserScreenshotToolArgs)."
    },
    "captureBrowserScreenshotSavingArgs": {
      "type": "object",
      "description": "Arguments describing where/how to save the screenshot (captureBrowserScreenshotSavingArgs)."
    }
  },
  "required": ["captureBrowserScreenshotToolArgs", "captureBrowserScreenshotSavingArgs"],
  "additionalProperties": false
}
```

---

## 26. Browser open URL

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    Url string "jsonschema:\"required\" jsonschema_description:\"The URL to open in the user's browser.\"";
    PageIdToReplace string "jsonschema_description:\"An existing page ID which will be replaced with this new URL. Use this to redirect a page if it's not needed anymore. Do not redirect pages you did not open. Leave blank if you want to open a new page.\""
  }
]
```

### Logical tool name

`browser.open_url`

### Purpose

Open a URL in the browser, optionally replacing an existing page.

### Arguments

| Field             | Type   | Required? | Description                                                                               |
| ----------------- | ------ | --------- | ----------------------------------------------------------------------------------------- |
| `Url`             | string | yes       | The URL to open in the user's browser.                                                    |
| `PageIdToReplace` | string | no        | Existing page ID to replace with this new URL (redirect). Leave blank to open a new page. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "browser.open_url.json",
  "title": "browser.open_url",
  "type": "object",
  "properties": {
    "Url": {
      "type": "string",
      "format": "uri",
      "description": "The URL to open in the user's browser."
    },
    "PageIdToReplace": {
      "type": "string",
      "description": "An existing page ID which will be replaced with this new URL. Leave blank to open a new page."
    }
  },
  "required": ["Url"],
  "additionalProperties": false
}
```

---

## 27. Memory get tool

### Evidence

```text
google3/third_party/jetski/cortex/utils/utils.ParseToolArgs[
  go.shape.struct {
    MemoryIds []string "jsonschema:\"required\" jsonschema_description:\"A unique list of MEMORY IDs to retrieve. Can be empty if no MEMORIES are relevant.\"";
    Reason string "jsonschema:\"required\" jsonschema_description:\"A detailed explanation for why you are retrieving these MEMORIES\""
  }
]
```

### Logical tool name

`memory.get`

### Purpose

Retrieve a list of memories by ID, with an explicit reason for retrieval.

### Arguments

| Field       | Type     | Required? | Description                                                     |
| ----------- | -------- | --------- | --------------------------------------------------------------- |
| `MemoryIds` | []string | yes       | Unique list of memory IDs to retrieve; may be empty.            |
| `Reason`    | string   | yes       | Detailed explanation of why these memories are being retrieved. |

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "memory.get.json",
  "title": "memory.get",
  "type": "object",
  "properties": {
    "MemoryIds": {
      "type": "array",
      "description": "A unique list of MEMORY IDs to retrieve. Can be empty if no MEMORIES are relevant.",
      "items": {
        "type": "string"
      }
    },
    "Reason": {
      "type": "string",
      "description": "A detailed explanation for why you are retrieving these MEMORIES."
    }
  },
  "required": ["MemoryIds", "Reason"],
  "additionalProperties": false
}
```

---


# Cortex State Machine States:
```
CORTEX_STEP_SOURCE_UNSPECIFIED
CORTEX_STEP_SOURCE_MODEL
CORTEX_STEP_SOURCE_SYSTEM
CORTEX_STEP_STATUS_UNSPECIFIED
CORTEX_STEP_STATUS_GENERATING
CORTEX_STEP_STATUS_QUEUED
CORTEX_STEP_STATUS_PENDING
CORTEX_STEP_STATUS_RUNNING
CORTEX_STEP_STATUS_WAITING
CORTEX_STEP_STATUS_DONE
CORTEX_STEP_STATUS_INVALID
CORTEX_STEP_STATUS_CLEARED
CORTEX_STEP_STATUS_CANCELED
CORTEX_STEP_STATUS_ERROR
CORTEX_STEP_STATUS_INTERRUPTED
CORTEX_STEP_STATUS_HALTED*
CORTEX_STEP_TYPE_UNSPECIFIED
CORTEX_STEP_TYPE_DUMMY
CORTEX_STEP_TYPE_FINISH
CORTEX_STEP_TYPE_MQUERY
CORTEX_STEP_TYPE_CODE_ACTION
CORTEX_STEP_TYPE_GIT_COMMIT
CORTEX_STEP_TYPE_GREP_SEARCH
CORTEX_STEP_TYPE_COMPILE
CORTEX_STEP_TYPE_VIEW_CODE_ITEM
CORTEX_STEP_TYPE_ERROR_MESSAGE
CORTEX_STEP_TYPE_RUN_COMMAND
CORTEX_STEP_TYPE_FIND
CORTEX_STEP_TYPE_COMMAND_STATUS
CORTEX_STEP_TYPE_SEARCH_WEB
CORTEX_STEP_TYPE_MCP_TOOL
CORTEX_STEP_TYPE_CLIPBOARD
CORTEX_STEP_TYPE_LIST_RESOURCES
CORTEX_STEP_TYPE_READ_RESOURCE
CORTEX_STEP_TYPE_LINT_DIFF
CORTEX_STEP_TYPE_READ_TERMINAL
CORTEX_STEP_TYPE_CODE_SEARCH
CORTEX_STEP_TYPE_BROWSER_INPUT
CORTEX_STEP_TYPE_TASK_BOUNDARY
CORTEX_STEP_TYPE_NOTIFY_USER
CORTEX_STEP_TYPE_BROWSER_SCROLL
CORTEX_STEP_TYPE_GENERATE_IMAGE
CORTEX_STEP_TYPE_SYSTEM_MESSAGE
CORTEX_STEP_TYPE_WAIT
CORTEX_STEP_TYPE_USER_INPUT
CORTEX_STEP_TYPE_VIEW_FILE
CORTEX_STEP_TYPE_LIST_DIRECTORY
CORTEX_STEP_TYPE_CHECKPOINT
CORTEX_STEP_TYPE_FILE_CHANGE
CORTEX_STEP_TYPE_MOVE
CORTEX_STEP_TYPE_COMPILE_APPLET
CORTEX_STEP_TYPE_PLAN_INPUT
CORTEX_STEP_TYPE_PROPOSE_CODE
CORTEX_STEP_TYPE_POST_PR_REVIEW
CORTEX_STEP_TYPE_BRAIN_UPDATE
CORTEX_STEP_TYPE_ADD_ANNOTATION
CORTEX_STEP_TYPE_MEMORY
CORTEX_STEP_COMPILE_TOOL_PYLINT
```
