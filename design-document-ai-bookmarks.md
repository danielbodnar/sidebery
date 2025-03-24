# Technical Design: "Automatically Organize Bookmarks with AI" Feature

## Overview

This document outlines the technical design for implementing an "Automatically Organize Bookmarks with AI" feature in Sidebery. This feature will allow users to organize bookmarks using AI by right-clicking on folders, bookmarks, or tabs and selecting "Organize with AI".

## Requirements

1. Allow right-click organization of bookmarks and tabs using AI
2. Support multiple providers: Anthropic Claude, OpenAI, Ollama, Gemini, and Mozilla's built-in LLM
3. Allow customization of model settings and prompt instructions
4. Process each bookmark individually with context
5. Store API keys securely with the ability to test connections
6. Enable/disable the feature through settings

## DEFAULT PROMPT TEMPLATE

```markdown
You are an advanced AI assistant specializing in browser bookmark classification and organization for Sidebery. Your expertise includes information architecture, taxonomy development, metadata enhancement, content categorization, and digital knowledge management across various domains including technology, science, arts, business, education, and research.

Your task is to organize bookmarks into a logical folder structure that enhances discoverability and usability within Firefox's sidebar extension Sidebery. You will also enhance bookmark titles and provide descriptive tags when appropriate.

ANALYSIS METHODOLOGY:
  1. Extract and analyze all available metadata including URLs, titles, descriptions, and existing paths
  2. Parse domain names, URL structures, and query parameters for contextual information
  3. Identify the primary subject matter, domain, technology, platform, or purpose
  4. Analyze existing categorization schemas if present
  5. Determine appropriate hierarchical categorization based on content analysis
  6. Generate comprehensive tag sets covering multiple facets of the resource
  7. Enhance titles for clarity, consistency, and information density when appropriate
  8. Maintain original metadata while adding enhanced classification

CLASSIFICATION FRAMEWORK:
  1. Primary Categories (Level 1):
      - 🧰 Development (programming, software engineering, development tools)
      - 🤖 AI & Machine Learning (AI tools, models, research, applications)
      - 📊 Data & Analytics (analysis, visualization, big data, statistics)
      - 🔒 Security & Privacy (cybersecurity, encryption, authentication)
      - 🌐 Web Development (frontend, backend, web design, APIs)
      - 📱 Mobile Development (iOS, Android, cross-platform frameworks)
      - 🎮 Games & Entertainment (media, streaming, gaming)
      - 📚 Documentation & Learning (tutorials, courses, references, guides)
      - 🧪 Science & Research (academic papers, scientific tools, research)
      - 💼 Business & Productivity (project management, collaboration, workflow)
      - 🎨 Design & Creative (UI/UX, graphics, media creation, typography)
      - 🔌 Hardware & IoT (embedded systems, electronics, IoT, robotics)
      - 📈 Finance & Economics (markets, economics, cryptocurrency, trading)
      - 🎭 Entertainment & Media (streaming, content creation, media consumption)
      - 🔧 Tools & Utilities (general purpose tools, productivity utilities)
      - 🌍 Networking & Infrastructure (infrastructure, cloud, devops, networking)
      - 📡 Systems & Platforms (operating systems, platforms, environments)
      - 🛒 Shopping & Commerce (stores, marketplaces, products)
      - 🏠 Home & Lifestyle (personal, recipes, home improvement)
      - ✈️ Travel (destinations, transportation, accommodations)

  2. Secondary Categories (Level 2):
      - More specific aspects of the primary category
      - Should represent logical specialization/subdivision
      - Include specific technology ecosystems, frameworks, or application domains

  3. Tertiary Categories (Level 3):
      - Highly specific subcategories
      - Specific tools, languages, or narrow domains
      - Detailed specializations of the secondary category

FOLDER STRUCTURE RULES:
  1. Use logical hierarchy from general to specific
  2. Maximum depth of 3 categorization levels
  3. Use concise, descriptive folder names with appropriate emoji
  4. Include emoji at the beginning of each category name
  5. Create sensible defaults for secondary/tertiary categories
  6. Ensure consistency between similar resources
  7. Use kebab-case for folder names (lowercase, hyphen-separated)

TAG GENERATION RULES:
  1. Generate 3-10 tags per bookmark that describe:
      - Primary programming language(s) or technology stack
      - Content type (article, tool, library, framework, documentation, tutorial)
      - Domain-specific terminology and concepts
      - Relevant technologies, platforms, or frameworks
      - Purpose, use case, or application domain
      - Prominent features or capabilities
  2. Format tags as lowercase, hyphen-separated terms without emoji
  3. Deduplicate tags and eliminate redundancy
  4. Include both general/discoverable and specific/precise tags
  5. Balance breadth and depth of tag coverage

TITLE ENHANCEMENT RULES:
  1. Maximum length of 100 characters
  2. Maintain core meaning and essential information
  3. For GitHub repositories:
      - Format as: "[Repository Name]: [Core Purpose/Description]"
      - Remove redundant words like 'GitHub', 'A', 'The', etc.
  4. For documentation:
      - Include technology name and documentation type
      - Add version information if critically relevant
  5. For general websites:
      - Preserve brand names and main topic
      - Improve descriptiveness if original title is vague
  6. Remove unnecessary emoji and special characters
  7. Standardize formatting across similar bookmarks

SPECIAL CASE HANDLING:
  1. GitHub Repositories:
      - Extract language, stars, and activity as classification signals
      - Use repository description for additional context
      - Categorize based on primary purpose rather than implementation language
      - Tag with both functionality and implementation details

  2. Documentation Sites:
      - Categorize primarily by technology, then by documentation type
      - Create logical groupings for related documentation resources
      - Add version information as tags when applicable

  3. Minimal Information Bookmarks:
      - Extract domain name and site section from URL
      - Use URL path components as classification hints
      - Create tentative categories based on available signals

  4. Online Tools and Web Applications:
      - Categorize by primary function rather than technology
      - Tag with both usage domain and implementation technology

OUTPUT FORMAT:
  Create a JSON structure that organizes the bookmarks into appropriate folders:
  ```json
  {
    "folders": [
      {
        "name": "🧰 Development",
        "bookmarks": ["id1", "id2"],
        "subfolders": [
          {
            "name": "JavaScript",
            "bookmarks": ["id3", "id4"]
          }
        ]
      }
    ],
    "enhancedBookmarks": [
      {
        "id": "id1",
        "enhancedTitle": "Improved title if needed",
        "tags": ["javascript", "framework", "frontend"]
      }
    ]
  }
  ```

QUALITY PRIORITIES:
  - Technical accuracy in classification and tagging
  - Logical and intuitive hierarchical structure
  - Consistency in formatting and taxonomy
  - Balance between specific categorization and general discoverability
  - Preservation of original information while adding value
  - Usefulness for both searching and browsing paradigms
```

## Implementation Plan

### 1. Create AI Service Module

Create a new service module to handle AI-related functionality:

- **File:** `src/services/ai.ts`
- **Purpose:** Define model constants, endpoint management, API key handling
- **Features:**
  - Mapping models to providers
  - Securely retrieving API keys
  - Testing connections to different providers

```typescript
// Key features to implement:
- AI_MODELS constant with supported models
- getEndpoint() function to retrieve provider endpoints
- getApiKey() function to securely retrieve stored API keys
- testConnection() function to verify provider connectivity
```

### 2. Implement AI Actions

Create an actions file to handle bookmark organization logic:

- **File:** `src/services/ai.actions.ts`
- **Purpose:** Process bookmarks, call AI APIs, handle responses, and reorganize bookmarks
- **Features:**
  - Provider-specific API calls (Anthropic, OpenAI, Gemini, Ollama, Mozilla)
  - JSON response parsing
  - Bookmark folder creation and organization

Key functions:
- `organizeBookmarks()`: Main entry point for organization
- `callAiForOrganization()`: Route to appropriate provider
- `processOrganizationResults()`: Handle AI response and restructure bookmarks
- `enhanceBookmarkTitles()`: Apply title enhancements when enabled
- `generateBookmarkTags()`: Generate and apply tags when enabled

### 3. Update Menu System

Add menu options to enable "Organize with AI" functionality:

- **File:** `src/services/menu.options.bookmarks.ts`
- **Changes:** Add new option for organizing bookmarks
- **File:** `src/defaults/menu.ts`
- **Changes:** Add the new menu option to the bookmarks context menu configuration

### 4. Add Settings UI

Create a new settings section for AI configuration:

- **File:** `src/page.setup/components/settings.ai.vue`
- **Purpose:** Allow users to configure AI providers, API keys, and custom prompts
- **Features:**
  - Enable/disable AI functionality
  - Select AI model/provider
  - Input API keys with secure visibility toggle
  - Configure provider-specific settings (Ollama endpoint, etc.)
  - Edit custom prompt template with syntax highlighting
  - Adjust AI parameters (temperature, max folders, max depth)
  - Toggle title enhancement and tag generation
  - Preview example categorization
  - Test connection functionality

### 5. Update Data Types and Defaults

Modify settings types and defaults to include AI-related options:

- **File:** `src/types/settings.ts`
- **Changes:** Add AI settings interface properties
- **File:** `src/defaults/settings.ts`
- **Changes:** Add default values for AI settings

```typescript
// New settings to add:
aiEnabled: boolean
aiSelectedModel: string
aiAnthropicApiKey: string
aiOpenAIApiKey: string
aiGeminiApiKey: string
aiOllamaEndpoint: string
aiOllamaModel: string
aiCustomPromptEnabled: boolean
aiCustomPrompt: string // Will contain the default prompt template by default
aiTemperature: number // Controls randomness (0.0-1.0, default 0.2)
aiMaxFolders: number // Maximum number of folders to create (default 8)
aiEnhanceTitles: boolean // Whether to enhance bookmark titles (default true)
aiGenerateTags: boolean // Whether to generate tags for bookmarks (default true)
aiMaxDepth: number // Maximum folder depth (default 3)
```

### 6. Add Translation Strings

Add new translation keys for the AI feature:

- **Files:**
  - `src/_locales/dict.common.ts`
  - `src/_locales/dict.setup-page.ts`
- **Purpose:** Support internationalization of AI-related UI elements

### 7. Create Assets

Add necessary SVG icons for the AI feature:

- **File:** `src/assets/ai-organize.svg`
- **Purpose:** Visual representation of the AI feature in menus and UI

## Implementation Details

### API Communication

For each provider, implement the appropriate API calls:

1. **Anthropic Claude**
   - Endpoint: https://api.anthropic.com/v1/messages
   - Default model: claude-3-7-sonnet-latest
   - Headers: x-api-key, anthropic-version
   - Response parsing: Extract JSON from content

2. **OpenAI**
   - Endpoint: https://api.openai.com/v1/chat/completions
   - Default model: gpt-4-0125-preview
   - Headers: Authorization
   - Response parsing: Extract JSON from choices[0].message.content

3. **Gemini**
   - Endpoint: https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-pro:generateContent
   - Default model: gemini-1.5-pro
   - Headers: Authorization
   - Response parsing: Extract JSON from candidates[0].content.parts[0].text

4. **Ollama**
   - Endpoint: Configurable, default http://localhost:11434/api/chat
   - Default model: llama3
   - No API key needed
   - Response parsing: Extract JSON from response

5. **Mozilla LLM**
   - Uses browser.experiments.llm if available (Firefox integration)
   - Local processing, no API key needed
   - Requires checking for API availability

### Bookmark Organization Logic

1. Process parent context (where the user right-clicked)
2. Gather bookmark metadata (title, URL, etc.)
3. Generate prompt with instructions for organization
4. Call the selected AI provider
5. Parse JSON response containing folder structure
6. Create folders and reorganize bookmarks according to AI suggestions
7. Apply title enhancements if enabled
8. Generate and apply tags if enabled
9. Show success notification with summary of changes

### Security Considerations

1. API keys stored in browser.storage.local with encryption
2. No API keys sent to any server other than the configured provider
3. Support for Mozilla's embedded LLM for privacy-conscious users
4. Clear documentation of data handling practices
5. Optional custom prompts to control information sent to APIs

## Permissions

The feature requires these permissions:
- `bookmarks` - For bookmark manipulation
- `<all_urls>` - For external API calls (except when using Mozilla embedded LLM)
- `storage` - For storing API keys and settings

## Testing Plan

1. Test each provider with valid and invalid API keys
2. Test bookmark organization with various folder structures
3. Test custom prompts with different instructions
4. Verify error handling for timeout/connection issues
5. Test with Firefox's embedded LLM when available

## Future Enhancements

1. Support for batch organization of bookmarks
2. AI-suggested tags and descriptions
3. Automated organization schedules
4. Support for additional LLM providers

## Conclusion

This design document outlines a comprehensive plan for implementing the "Automatically Organize Bookmarks with AI" feature in Sidebery. Following the existing patterns in the codebase ensures integration will be smooth and maintainable.
